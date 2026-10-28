# ChronosSkills MVP Implementation Plan — R1（条件付き・実行禁止）

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking. **IMPORTANT: This R1 is NOT EXECUTABLE; the instruction applies only to a separately approved executable successor plan. No task may be started from this document.**

**Goal:** 採択済みD-01〜D-10を維持し、既存Skills／ChronosGraphを優先した上で、新規一時Skillの自律生成・静的検証・元タスク限定利用・軽量評価・Human承認候補提示をOpenCode v2で成立させるMVPを、実装前の技術判断と権限ゲートを満たした後に実現する。

**Architecture:** Q1＝Agentの意味判断＋決定論的Core、Q2＝MCP優先／STDIO／最小CLI／共通Core／HTTP後回し、Q3＝検索優先の保守的判断。Q4〜Q7＝Core管理Root Taskの共有予算・原子的Attempt・冪等性。Q8の信頼済みRoot発行起点は**条件付き採択・実現可能性未証明**であり、現状では生成経路を起動しない。

**Tech Stack:** 初期対象はOpenCode **v2.1.4**（`anomalyco/opencode`固定commit `68bc26b7da1990ce10ffecfbc1e09eb13aa5d824`）。MCP/stdio・CLI最小・共通Coreは方針採択済み。**実装言語、フレームワーク、永続ストア、公開API・型、OpenCodeの一時Skill提供方式は未決定**。根拠なしにPython/TypeScript、DB、認可基盤を決定しない。

**Spec:** `docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md`（`yohi/chronos-skills`固定HEAD `e0d203d71d2f8b0230c7c587678257d35f96c13b`、Blob `ce3dccb99e607e74b608c636f4146a65c1671f34`）。要件は同HEADの`REQUIREMENTS.md`（Blob `490e4d38d022c5ba3c748131775035b0f33f7b21`）。

**Plan location per `superpowers:writing-plans`:** `docs/superpowers/plans/2026-10-28-chronos-skills-mvp-implementation-plan-R1-gated.md`（ローカルで作成した計画書。後続のHuman Owner明示指示により、この文書1ファイルに限ってGitHubへのcommit・pushを許可）。

**Read-only source lock:** `yohi/chronos-skills`固定HEAD `e0d203d71d2f8b0230c7c587678257d35f96c13b`。R3.1設計書 blob `ce3dccb99e607e74b608c636f4146a65c1671f34`、`REQUIREMENTS.md` blob `490e4d38d022c5ba3c748131775035b0f33f7b21`。文書内容は採択済み、残存Findingの技術状態はOPEN。

**Document status:** `R3_1_OWNER_ACCEPTED / WRITING_PLANS_R1_PREPARED_CONDITIONAL / NOT_EXECUTABLE / IMPLEMENTATION_REVIEW_GATE_BLOCKED / NO_EXECUTION_AUTHORITY`。

## Global Constraints

- D-01〜D-10を削除・縮退しない。特にD-02の**実際の自律Skill生成**を検索だけで代替せず、D-10により既存Skill自動改変は実装しない。
- Q1〜Q7は採択済み設計方針、Q8は`OWNER_ADOPTED_CONDITIONAL`、Q10はCaller A＋B基準／実効権限がCならCとして評価／C除外には強制分離の証拠を必要とする。
- Root新規発行／既存Context利用／子Agentへの委譲／Attempt予約・消費は別権限。Session ID・Metadata・`session.created`・MCP接続・単なる一般Server認証をRoot発行権限にしない。
- 1 Root Taskに対して親子Agent共通の生成・修正Attempt上限は**最大2**。トークン／時間も別途上限、測定不能は`UNKNOWN`でありゼロとしない。
- `STARTED`＝開始許可と消費の永続確定。`UNCERTAIN`は枠保持。`RELEASED`は開始許可未発行を証明できる場合のみ。`CONFLICT`は同一Idempotency Key・異なるFingerprint、同一Key同一Fingerprintは既存Attempt状態照会。
- 認可・予算・帰属・静的検証が不明な場合、**生成側はfail-closed**。元の通常開発タスクをChronosSkillsの失敗のみで止めない。
- 未承認Skillは元のProjectとTaskに限定。別タスクで自動利用しない。未承認のグローバル設定・既存Skill・APM等を変更しない。正式採用・Git変更・配布は別のHuman Owner承認。
- ChronosGraph・ChronosGate・SkillPort・APM・dotfiles-aiの全面改修や、新しい専用認可サービスを自動でMVP必須範囲に追加しない。
- 現在は`FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED / AUTONOMOUS_GENERATION_HOLD / IMPLEMENTATION_NOT_AUTHORIZED / NO_AUTO_R2`。この計画の作成や受入はこれらを変更しない。
- 技術的Findingの扱いはR2 Review Handoffに従う：`CS-R1-001〜005 = PARTIALLY RESOLVED`（文書面のみ・技術課題OPEN）、`CS-R1-006 = RESOLVED`、`Implementation Review Gate = BLOCKED`。このレビュー結果はユーザー提示の記録であり本書が独立再検証した判定ではない。

## Review Focus（実装時に漏らせない5つの境界）

| # | 失敗条件・入力クラス | 期待される振る舞い | 将来の所有タスク |
|---|---|---|---|
| RF-1 | 同一作業の別Session／Import／子Agentから新Root作成を試みる | 無権限Root発行を拒否し、予算を再取得させない | I-03、I-04（Q8成立後にのみテスト化） |
| RF-2 | 同一Key同一Fingerprint再送、異なるFingerprint、永続化後の応答逸失・クラッシュ | 重複開始なし／`CONFLICT`／`UNCERTAIN`枠保持、最大2上限維持 | I-04 |
| RF-3 | 越境したChronosGraph検索結果や保存済みDraftを参照する | Project/Taskの認可・結果帰属を検査。不明・越境なら提供拒否 | I-02、I-07 |
| RF-4 | Skillの発見キャッシュ・タスク終了イベント逸失・次回Sessionでの再検索 | 未承認Skillの自動ロード・継続有効化を防止 | I-06、I-07 |
| RF-5 | 静的検証失敗、秘密混入、依存障害、トークン計測不能 | Skillの生成／利用を停止、未評価・UNKNOWNとして記録、元タスクは通常手段へ復帰 | I-05、I-08 |

---

## 0. Superpowersの計画作成ゲートと今回の到達範囲

`superpowers:brainstorming`を経たR3.1はHuman Ownerが本対話で受け入れた。`superpowers:writing-plans`（参照：`obra/superpowers` v6.4.2）は、本来、正確なファイルパス・型／署名・失敗テスト・実行コマンド・期待結果・Commit単位まで定める**実行可能な実装計画**を要求する。

しかしR3.1 §4.1.1〜§4.1.3、§6.2.1、§7.4はこれらを確定せず、Human Ownerの別判断に留保している。また固定HEADでは今回確認したルートの`pyproject.toml`・`package.json`・`src/__init__.py`・`tests/__init__.py`が未確認（該当パスの読取は404）であり、採用する言語・実装レイアウトも決まっていない。

よって**実装可能な最終版の`writing-plans`成果物は現時点では成立しない**。本R1は、設計決定を勝手に埋めずに、技術判断の順序・STOP・その後の実装Taskの対応を記録する**条件付き計画の前段階**とする。次に作る実行可能版では、以下の決定と証拠を確認したうえで初めてファイルパス、signatures、具体的テスト、コマンド、コミットを設定する。以下のTask名は**設計・要件の追跡用**であり、作業許可ではない。

## 1. 実装前の必須判断ゲート（現在すべてOPEN）

| Gate／Finding | 確定・証明する事項 | 最低限の受入証拠 | STOP条件と次の判断 |
|---|---|---|---|
| **G-01 CS-R1-001 Blocker** | Q8信頼済みRoot発行起点、独立したCaller認証、全入口の完全仲介。Caller A/B、実効C、Cを除外する強制分離。Root発行と既存Context利用を分ける | F-01/F-02/F-05に対する固定OpenCode・構成証拠と経路別の反例検査、Human Ownerの明示採択 | 根拠不足・迂回可能・権限自己申告依存なら**STOP / HOLD**。追加検証や認可方式の新設を自動実施しない |
| **G-02 CS-R1-002 Major** | OpenCode v2.1.4で一時Skillの提供主体・配置・発見・利用認可・失効・キャッシュ排除・次回自動利用禁止 | 既存API/Host境界に照らした方式選択の記録、v2.1.4成立確認の証拠、Owner判断 | Global Skillsへの流入、タスク限定を保証不能、検証未実施なら**STOP** |
| **G-03 CS-R1-003 Major** | 共有予算のauthoritative owner、永続Store、Attempt状態・冪等性の原子的予約／開始許可、Crash復旧、未知トークン時の制限 | 単一の状態所有境界・遷移表・並行/再送/クラッシュ反例と検証条件、Owner判断 | 部分確定・二重消費・枠復活・復元不能で生成を可能とする案なら**STOP** |
| **G-04 CS-R1-004 Major** | Adapter/Coreの操作一覧、Root/Task/Projectの認可入力、型・バージョン・シリアライズ、`CONFLICT`／`UNDETERMINED`／認可拒否／予算不足の区分、Agent伝播、監査と秘匿 | 外部に出すメソッド／型・エラー契約の文書と責任分界、Owner判断 | API/型を実装担当が独断で決める／エラーを成功扱い／秘密の誤記録なら**STOP** |
| **G-05 CS-R1-005 Major** | Project識別の信頼元、検索結果・Draft・評価記録の参照強制点、境界不明時のfail-closed、Task終了後のアクセス | 参照・書込・検査の責任表、越境否認ケース、Owner判断 | Projectラベルのみで隔離とみなす／他Projectの記憶を無条件提供するなら**STOP** |

### 1.0.1 Gateの成果物定義（方式を新規採択しない）

以下は既存CS-R1-001〜005を**実行可能な計画の入力へ変換するための必要資料**であり、新しいシステム機能・MVPスコープではない。文書記録と技術的な成立証拠を分ける。

| Gate | 後続計画が参照すべき決定資料 | 一義的に判定する点 | 証拠なしの結果 |
|---|---|---|---|
| G-01 | Root発行Actor/入口/権限境界・拒否経路の固定証拠表、A/B/実効C到達性、Owner採択 | 新規Root発行が非認可Callerから完全仲介されるか | `NOT_VERIFIED`・生成HOLD。Root APIを定義しない |
| G-02 | v2.1.4限定ロード/発見/キャッシュ/失効/所有の責任表、検証結果、Owner選択 | 当該Taskだけで利用し、次回・Globalへ流出しないか | ロード処理を実装スロットへ移さない |
| G-03 | 共有台帳のauthoritative owner、排他・永続化方式、再起動/不明応答の状態遷移表 | 最大2試行、原子的STARTED、再送不変条件を守れるか | 生成開始許可を発行しない |
| G-04 | 境界ごとの公開操作・決定済みtype shape・シリアライズ・version・結果分類・監査秘匿の契約表 | 同一の名前・型・エラーをAdapterとCoreが解釈するか | 具体的署名を発明しない |
| G-05 | 認可されたProject信頼元・検索結果検査/候補参照の責任表と越境反例 | 未承認データ越境や別Task自動利用を阻止するか | 参照・利用をfail-closed |

**未決定と未証明の分離:** G-02〜05で具体方式のOwner判断が未取得なら`UNDECIDED`。G-01または方式の成立証拠が不足なら`NOT_VERIFIED`。調査・実装の実施許可がないことは独立して`NOT_AUTHORIZED`。いずれも「計画に書いたのでPASS」にしない。

### 1.0.2 実装計画の前提となる開発環境決定（既存Findingとは別の計画入力）

現行の`REQUIREMENTS.md`およびR3.1は言語、ライブラリ、パッケージ構成、永続ストアを指定していない。固定HEADで確認した代表的な`pyproject.toml`・`package.json`等の候補パスは未存在であり、**リポジトリ全ファイルを網羅監査したとの主張ではない**。実行可能な計画では、採択後の環境・依存・実在するファイル配置を根拠として、正確なCreate/Modify/Testパスを定義する。この決定は技術選択の追記であって、MVP必須機能の追加ではない。ここではPython/TypeScript、SQLite/DB、Host権限方式等を推測で決めない。

### 1.0.3 判断ゲートのチェックリスト（承認記録用・実行指示ではない）

- [ ] G-01：Trusted Root発行境界の成立証拠とHuman Owner判断がある
- [ ] G-02：OpenCode v2.1.4の一時Skill提供・無効化方式が採択され、成立証拠がある
- [ ] G-03：Root共有予算の所有者・永続化・原子性・復旧が採択・検証されている
- [ ] G-04：Adapter/Coreの操作、型、エラー、秘匿の公開契約が採択されている
- [ ] G-05：Project/Task境界の認可責任と強制点が採択・検証されている
- [ ] 実装言語・パッケージ構成・テスト環境が明示的に確定している
- [ ] 後続の実行可能計画に具体的なパス・署名・RED/GREEN・コミット・独立整合性確認が記載されている
- [ ] 実行可能計画のレビューと、実装開始の個別許可が別途与えられている

**現在のチェック数：0件**。チェックの記載は事実の証明でも、実装・検証開始の指示でもない。

### 1.1 ゲートの順序と個別承認

1. Human Ownerは必要な証拠取得・静的追加確認・Host実験・新規アーキテクチャ検討について**それぞれ**権限を判断する。本書はこれらを開始しない。既存Option Aは`OWNER_ADOPTED / NOT_EXECUTED`のまま。
2. G-01の未証明状態を前提に、後段の方式やコードを決めてもRoot発行HOLDは解除されない。G-02〜G-05は採択済みQ1〜Q10を変更せず、実装前の方式選択に留まる。
3. 追加方式が必要と判断されたときは、選択肢・MVP範囲への影響をHuman Ownerへ返す。変更は自動採択しない。
4. G-01〜G-05の判断と成立根拠が揃い、設計書との整合が独立確認された場合に限り、**実行可能な`writing-plans`後続版**を作成するか別途判断する。
5. 後続版の計画受入、Implementation Review Gate、実装権限、GitHub書込、E2E実行・MVP完成判定は**別の判断**であり、段階的に許可されない限り進めない。

## 2. 将来の実装タスク分解（すべて条件付き／作業不可）

以下の`I-01〜I-09`は要件の実装順序を示す**追跡用スロット**。正確なファイルパス／型／テストコマンドを確定できないため、`superpowers:writing-plans`で要求される実行単位ではない。実装言語・リポジトリ構成の決定後にのみ、1タスクにつき独立したRED→GREEN→必要な回帰→コミットのチェックボックスを具体化する。ここに架空のコードを置かない。

| スロット | 将来の独立成果物 | 主な依存Gate | 受入条件・テスト観点（未実行） |
|---|---|---|---|
| **I-01** | Agentの不足候補入力・既存資産優先の生成要否判定。Coreによる`SKIP` / `UNDETERMINED`制御 | G-04、G-05 | FR-01/03、AC-01。既存Skill充分なら生成なし。不明判断なら生成なし |
| **I-02** | 既存Skill/ChronosGraph参照と結果のProject帰属検査 | G-04、G-05 | FR-02、AC-10。`memory_type`や`project`ラベルだけを強制分離としない。越境結果を遮断 |
| **I-03** | 正当なRoot発行起点とRoot Context利用／子委譲の別認可 | **G-01**、G-04、G-05 | FR-06/07、NFR-04/05。A+B/実効Cからの偽発行・再発行拒否。未証明なら実装禁止 |
| **I-04** | 共通Root予算・Attempt永続状態・冪等性・原子的更新／復旧 | **G-01**、G-03、G-04 | FR-07、AC-05。上限2、並行、同一Key再送、`CONFLICT`、`UNCERTAIN`/`RELEASED`不変条件 |
| **I-05** | Agentの既存LLMを使う新規一時Skillドラフト生成と利用前の静的検証 | **G-01**、G-04、G-05 | FR-04/05、AC-02/03。新規のみ、秘密混入・衝突・危険指示で利用停止、生成スクリプトは無条件実行しない |
| **I-06** | 生成元Task限定の一時Skill提供・ロード・失効／非持越し | **G-01**、**G-02**、G-04、G-05 | FR-06/09、AC-04/08。元Taskでのみ有効、全体Skillsに混入せず、Task終了後無効 |
| **I-07** | プロジェクト内候補・評価記録の期限付き保存と照会・権限検査 | G-04、G-05、G-02 | FR-09/12、AC-07/08。再評価参照と次Task自動利用を分離、保存容量・期限境界 |
| **I-08** | 軽量評価、元タスクへの通常復帰、正式昇格候補表示 | G-04、G-05 | FR-08/10/11、AC-06/07/09。未評価は未評価、Human採用とGit/配布の権限は別 |
| **I-09** | OpenCode v2.1.4での統合・E2Eと全受入条件の確認 | G-01〜G-05と全先行スロット | AC-01〜11。検知→生成→検証→当該Task利用→評価。現在は未実行・未認可 |

### 2.0.1 スロット別のテスト契約案（将来のRED/GREEN定義入力・未実行）

以下はテストの**期待振る舞い**のみを固定する。関数名・型・テストパス・コマンドは採択済みではないため記載しない。実行可能な後続版では、各ケースを確定APIに対する**単一のテスト名とassert**へ落とし込み、REDの失敗原因とGREENの期待結果を明記する。現時点でのテスト実施や既存テストPASSの主張ではない。

| 所有Task | 固定要件に由来する入力ケース | 必須の検証可能な期待結果 |
|---|---|---|
| I-01 | 既存Skillだけで解決可能 | 新規生成Attemptなし、`SKIP`と理由を記録（AC-01） |
| I-01 | Agentの不足提案が空/矛盾/評価不能 | `UNDETERMINED`、生成許可を出さない（FR-01/03） |
| I-02 | ChronosGraph `memory_type`検索条件に不適合な結果が混入 | 絞り込み実装を前提にしない。帰属・許可を個別検査し、越境項目を遮断（AC-10） |
| I-02 | 不明なProject識別または検索結果の帰属証拠なし | 他Projectの結果を返さず、生成補助に採用しない（RF-3） |
| I-03 | `session.created`/Session Import/MetadataだけでRoot発行を試す | Trusted issuerの証拠がなければRoot発行拒否（RF-1） |
| I-03 | 子AgentがRoot発行/予算増額を要求 | 子の権限内の利用のみ可、独立Root新設・増枠は拒否（Q5/Q8） |
| I-04 | 同時に3件目のAttemptを予約する | Root全体上限2のため拒否、子Agentを含め共有（AC-05） |
| I-04 | 同一Context+Key+Fingerprintを再送 | 新たにSTARTEDにせず既存Attempt照会（RF-2） |
| I-04 | 同一KeyでFingerprint不一致 | `CONFLICT`、新たな消費・開始許可なし（Q7） |
| I-04 | 開始許可永続化直後に応答を喪失して再起動 | 消費枠保持、開始許可の再発行なし、復元不能ならfail-closed（RF-2） |
| I-04 | 開始未許可を証明できない`RESERVED`/`UNCERTAIN` | `RELEASED`へ移せず枠保持、未知トークンは`UNKNOWN`（Q6） |
| I-05 | ドラフトに秘密・危険指示・既存Skill識別子衝突 | 静的検証拒否、元Taskでロードしない（AC-03、RF-5） |
| I-05 | 静的検証に合格した新規Skill | 利用対象候補としてのみ登録、生成スクリプトは無条件実行しない |
| I-06 | 元Taskが終了・終了通知が喪失・キャッシュが残存 | 別Taskで自動発見・ロードできない（RF-4、AC-08） |
| I-06 | グローバルSkills/既存配置への候補混入を要求 | 未承認の変更拒否、既存Skillsを上書きしない |
| I-07 | 保存済み候補を同Projectの次Taskから参照 | 明示参照・再評価のみ許可し、自動利用禁止（D-09） |
| I-07 | 別Project/Taskから候補または評価記録を参照 | 認可なしは拒否・漏えいなし（RF-3） |
| I-08 | 依存障害・元Task成功要因不明・比較不能 | 成功実績を捏造せず未評価を記録し、通常開発へ復帰（AC-06/07） |
| I-08 | Humanが正式採択のみ承認 | Git変更・APM配布は行わない（AC-09） |
| I-09 | 認可・予算・一時利用が成立した正常系 | 不足発見→生成→検証→元Task限定利用→評価記録をE2Eで確認（AC-11） |
| I-09 | Root境界未証明の実環境 | 生成経路に到達しない。検索のみをMVP完成として扱わない（AC-02） |

### 2.0.2 実装依存順序とレビュー境界（実装は未承認）

| 順序（仮の依存関係） | スロット | 先行して必要な決定 | Review Gateで拒否できる成果単位 |
|---|---|---|---|
| A | I-01 / I-02 | G-04、G-05 | 検知・検索/帰属検査の責任を分離し、各単位を独立テスト |
| B | I-03 | **G-01**、G-04、G-05 | 4権限を混同しないRoot認可と拒否経路 |
| C | I-04 | B、G-03 | Root共有台帳・原子状態遷移・クラッシュ回復 |
| D | I-05 | B/C、G-04、G-05 | 新規生成と静的検証を権限付きで結合 |
| E | I-06 / I-07 | G-02、G-04、G-05、先行Task | Task限定ロードと候補保持/参照を独立テスト |
| F | I-08 / I-09 | 前段すべて | 評価と昇格分離、OpenCode v2.1.4 E2E受入 |

**依存順序を理由に実装を開始しない。** Ownerが後で別順序を採択してもD-01〜D-10の機能を削らない。技術的に異なる独立サブシステムへ分割する必要が生じた場合は、R3.1のMVP範囲を変更せず、各成果物を独立レビュー可能な別計画に分けることをOwnerへ提案する。

### 2.1 後続の実行可能計画に必ず追加する情報

各スロットについて、(a)選択済み言語／実在する正確なパスと依存、(b)入力・戻り値・エラーの確定した型と関数署名、(c)他タスクとのConsumes/Produces、(d)仕様値を含む失敗テストの名前とassert、(e)REDコマンドと期待FAIL、(f)最小実装、(g)GREENコマンドと期待PASS、(h)Commit対象・メッセージ、(i)必要な独立レビュー境界を記載する。**どれも現時点で推測して固定しない**。

## 3. 設計書との整合性確認（実行可能性のPASSではない）

| 比較軸 | R3.1の出典 | 本R1で守った事項 | 未確認事項 |
|---|---|---|---|
| MVP目標・除外範囲 | §1、§5、§12 | 自律生成を削らず、既存Skill自動改変等を追加しない | MVP動作自体 |
| コンポーネント責務・技術選択 | §2〜§4 | Q1/Q2/Q3を変えず、言語／実装方式を未決定のまま明記 | 正確なファイル／API |
| Q8/Q10認可 | §4.1.1、§7 | 4権限分離・A+B/実効C・未証明HOLD | Trusted Root発行・完全仲介 |
| 型・インターフェース／失敗 | §4.1.2、§5.1 | `CONFLICT`/`UNDETERMINED`、認可／予算拒否を区別 | 型名・フィールド・シリアライズ |
| 予算とAttempt | §6.2〜6.2.1 | 最大2、`STARTED`/`UNCERTAIN`/`RELEASED`、冪等性を保持 | 状態所有者／永続化／原子性 |
| Task限定Skill提供 | §4.1.3、§6.3 | 権限拡大なし・当該Task限定・次Task自動利用禁止 | Hostロード／失効方式 |
| Project境界 | §3.1、§7.4 | projectラベルでは認可しない、越境fail-closed | 実際の強制主体・証拠 |
| テスト方針 | §9〜§10 | AC-01〜11と未実行を対応付け、RF-1〜5をスロットに結び付ける | 実テストパス／コマンド／結果 |
| NFR | §8〜§9 | コスト・権限・障害分離・再現性・運用簡素化を維持 | 性能値・実行保証 |

**自己点検結果:** R3.1の既存設計判断を変更する記載なし。FR-01〜FR-12・NFR-01〜NFR-07は対応スロットあり。AC-01〜AC-11は対応スロットあり。ただし**具体的なtype/interface・エラー伝播・テスト方法の両文書整合性は `NOT_CHECKED / NOT_YET_SPECIFIED`**。したがってR3.1 §11.2.2にある後続の独立照合ゲートを通過したと主張しない。

## 3.1 `writing-plans`自己点検（スキル要求との一致・不一致）

| 自己点検項目 | 判定 | 根拠／不足 |
|---|---|---|
| 目的・制約・採択設計方向 | PASS（文書対応） | D-01〜D-10、Q1〜Q10、OpenCode固定基準、HOLDを維持 |
| FR-01〜FR-12のタスク対応 | PASS（計画追跡） | §2のI-01〜I-09。テスト未実行 |
| NFR-01〜NFR-07のタスク対応 | PASS（計画追跡） | §3のNFR対応、§2.0.1の境界ケース |
| AC-01〜AC-11の検証観点 | PASS（テスト観点） | §2/§2.0.1。E2Eは未実行 |
| 各Taskの**正確なファイルパス** | **BLOCKED** | 実装言語、構成、責任分担が未決定 |
| 各Taskの**確定した関数/型署名** | **BLOCKED** | G-01/G-02/G-04/G-05の実装前判断が必要 |
| 各Taskの**RED/GREENコマンドと期待出力** | **BLOCKED** | テストランナー・実装API・実行環境が未決定 |
| コミット単位・相互interfaceの整合性 | **NOT_CHECKED** | まだ確定interface/実装がなく、仮の型名を固定しない |
| R3.1との独立した設計↔計画書整合レビュー | **NOT_EXECUTED** | 概念上の対応は自己点検済み。独立レビューと混同しない |

**結論：** `writing-plans`を呼び出したが、規則が求める**実行可能な詳細実装計画の作成完了は宣言しない**。未決定事項を設計者の無断の採択で埋めれば、先に完了したbrainstormingの承認境界を破るためである。現段階の成果物は「条件付き計画R1」で、以後の実施は全て別承認を要する。

## 4. 判断・STOPと実行引継ぎ

- **今回の許可:** R3.1 Human Owner受入＋`writing-plans`による**文書作成**。本書は提案／計画であり、コード・テスト・Host操作・実装の権限なし。後続のHuman Ownerの明示指示は本計画書1ファイルのcommit・pushに限る。
- **完了したこと:** 固定R3.1・元要件の読取、設計対実装の未確定事項の特定、実行禁止の条件付き計画・追跡表の作成。
- **完了していないこと:** G-01〜G-05の解消、方式採択、具体的な実装ファイル／型／テストの確定、独立整合性レビュー、Implementation Review Gate PASS、実装開始承認。
- **STOP条件:** 未証明のQ8をPASSと扱う、未承認API/型/Store/認可機構を採択する、実装や実験／本計画書保存以外のGit書込を開始する、RF項目に対する試験実施を開始する、元のMVPスコープを追加/縮小することが必要になった場合。
- **後続判断:** Human OwnerはこのR1を計画整理の成果物としてレビューできるが、**実行方式の選択は現在行わない**。安全性と設計詳細の成立後、別途実行可能版の計画をレビューし、実装方式・実装権限を選択する。

**最終状態:** `R3_1_OWNER_ACCEPTED / WRITING_PLANS_R1_DOCUMENT_PREPARED / EXECUTABLE_PLAN_BLOCKED / CS_R1_001_TO_005_TECHNICAL_OPEN / FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED / AUTONOMOUS_GENERATION_HOLD / IMPLEMENTATION_NOT_AUTHORIZED / NO_AUTO_R2 / NO_FURTHER_GITHUB_WRITE_AUTHORIZED`。
