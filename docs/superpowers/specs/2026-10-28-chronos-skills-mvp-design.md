# ChronosSkills — MVP設計書 R3.1（Superpowers Review Gate R1 文書限定是正案・実装未承認）

> **文書状態:** R3_OWNER_ACCEPTED / R3_1_DOCUMENT_REMEDIATION_COMMITTED_OWNER_REACCEPTANCE_PENDING / REVIEW_GATE_R1_FINDINGS_REMAIN_OPEN / FEASIBILITY_NOT_VERIFIED / NOT_IMPLEMENTATION_AUTHORITY  
> **作成基準日:** 2026-10-28（JST）  
> **対象:** `yohi/chronos-skills`  
> **設計の基礎:** `REQUIREMENTS.md` + 本対話のQ1〜Q10設計判断記録  
> **文書是正時の権限:** DOCUMENT_REVIEW_REMEDIATION_ONLY / NO_IMPLEMENTATION / NO_TEST_EXECUTION / NO_HOST_VERIFICATION / NO_GITHUB_WRITE / NO_GATE_AUTHORITY（文書限定Git反映は別途Human Ownerが明示承認。追加の変更権限は生じない）  
> **総合状態:** `R3_OWNER_ACCEPTED / R3_1_REMEDIATION_COMMITTED_OWNER_REACCEPTANCE_PENDING / CS_R1_001_BLOCKER_OPEN / CS_R1_002_TO_005_MAJOR_OPEN / Q2_Q7_OWNER_ADOPTED / Q8_OWNER_ADOPTED_CONDITIONAL / Q10_OWNER_ADOPTED / FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED / AUTONOMOUS_GENERATION_HOLD / IMPLEMENTATION_NOT_AUTHORIZED / NO_AUTO_R2`

## 0. 本書の位置づけと読み方

本書は既存要件・設計対話・Human Ownerの採択記録を統合した**MVP設計書R3（Human Owner受入済み）に対する文書限定是正案R3.1**である。R3は固定HEAD `9f947ec72b476d1facdda1fad346ae9d73945f44`でGitHubに保存済み。本書R3.1はHuman Ownerの個別指示に基づき**設計書ファイルのみGitHubへコミット・反映した文書是正版**であり、**独立Review Gateの再判定とR3.1差分のHuman Owner受入は未実施**。今回のゴールは設計文書の整合性是正であって、実現可能性実証・実装・MVP稼働ではない。Q2〜Q7は既存推奨案のまま設計方針として採択、Q8は信頼済み起点＋Core管理Contextを**実現可能性未証明の条件付き設計方針**として採択する。採択は実装可能性の証明、詳細実装仕様の完成、実装・GitHub書き込み承認を意味しない。

- **ADOPTED:** Human Ownerが既に確定した要件・判断。
- **OWNER_ADOPTED_DESIGN_DIRECTION:** Human Ownerが既存推奨方式を設計方針として採択。実装方法・実行許可の確定とは区別する。
- **NOT_VERIFIED / NOT_ESTABLISHED:** 必要な証拠が揃っていない。成立、実装可能性、安全性を断言しない。
- **UNDECIDED:** 実装前に具体的な方式・責任主体・インターフェース等を選択する必要がある。未採択の方式を本書で決定しない。
- **NOT_AUTHORIZED:** 行為に対する実施権限がない。未決定／未証明とは独立する。
- **REVIEW_FINDING_OPEN:** 本書の文言是正はFindingの技術的解消やGate PASSではない。
- **HOLD / NOT_AUTHORIZED:** 自律Skill生成・Host実行・実装・GitHub書き込み等は許可されていない。

本書の図・処理ステップは**採択された論理設計（実装未実施）**を示す。Q2〜Q8の方針採択はHuman Ownerの明示承認によるものであり、設計文書の存在による自動転換ではない。未証明の認可境界・実行時安全性・技術的詳細をPASSに格上げしない。

### 設計書としての完了基準（実装着手条件とは独立）

本書の対象は、**①目的とMVP境界、②採択済み設計判断、③構成と責務、④生成・検証・利用・評価・昇格の論理フロー、⑤予算・状態遷移・認可境界、⑥要件／受入条件との対応、⑦未証明事項と実装前の前提**を一つの文書に揃えること。この七点を満たすことで**文書作成作業の完了候補**とする。Human Ownerによる文書の承認は独立した判断である。

- Q8 Root発行の実現可能性、実行時隔離、OpenCodeへの一時ロード方式等は**未証明・実装前に決める事項**として正直に残す。未証明事項があることだけを理由に設計書作成を無期限に延長しない。
- 文書が完成候補となっても、実行可能な詳細仕様が確定した、実装可能と証明された、MVPが完成した、とは表示しない。
- この文書を完成させるためにQ9-R2・Option Aの追加調査・Host実験・新しい認可方式の設計を必須工程として追加しない。



### 0.1 文書管理

| 属性 | 記録 |
|---|---|
| 文書種別 | MVP設計書（Human Owner採択方針と未解決事項の台帳） |
| 改訂 | R3.1 — 固定HEADのR3を母本とするCS-R1-001〜006の**文書限定**是正案。採択済み方針は不変 |
| 直前版 | R3：`docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md`（固定HEAD `9f947ec72b476d1facdda1fad346ae9d73945f44`、Git blob `7cb1cdd46d0a0f959342481ef3982772fe01b5ae`）。R3の直前版はR2 |
| 基準要件 | `yohi/chronos-skills/REQUIREMENTS.md`（R0で照合したGit blobを維持） |
| 実行中のサービスを記述しているか | いいえ。未実装・未実証の候補設計を含む |
| この改訂で新しい設計決定を行ったか | **いいえ**。R2の採択済み設計方針は変更せず、設計書作成をゴールとする記述へ整理 |
| GitHubへの反映 | **R3はmasterに反映済み**（固定HEAD `9f947ec72b476d1facdda1fad346ae9d73945f44`）。**R3.1は設計書1ファイルの文書限定更新としてmasterにコミット・反映済み（コミットIDはGit履歴を正とする）** |
| 正式な設計承認 | D-01〜D-10、Q1〜Q8（Q8は条件付き）、Q10の設計方針は採択済み。**R3はHuman Owner受入済み**。本書**R3.1の差分受入とReview Gate是正判定は未取得**。いずれも実装承認ではない |

### 0.2 改訂履歴（R0→R1→R2→R3）

- 文書冒頭にレビュー対象と権限上の限界を明示した。
- 要件と候補設計の対応関係を追跡できる表を追加した。
- Q10の実効能力に基づくCaller分類と、Q8未実証の区別を整理した。
- 未確定事項を、未採択の設計決定・実証不足・実装詳細未決定に分けた。
- R1：Human Ownerのレビュー対象を明記した。
- **R2：Human Ownerの「推奨案をそのまま承認」という明示回答により、R1記載のQ2〜Q8方式を設計方針として採択したことを記録。Q8の実現可能性、実証、実装権限は変わらない。**
- R2後：F-01/F-02/F-05の最小検証計画R0がHuman Ownerに採択され、限定静的検証R0を実施・STOP（全項目NOT_VERIFIED）。Option Aの方向性が採択されたが、所在・取得可能性の追加調査は未実施。
- **R3：ゴール逸脱を防ぐため、設計書の完成候補と技術実証の別工程を明確化。採択済み方式の追加・変更なし。**
- **R3のその後：Human OwnerがR3の文書を受入れ、Superpowers規則に基づきGitHubの固定HEADへ保存。文書の採択とGit書込承認は、Q8成立や実装着手権限とは別。**
- **R3.1（本書）：CS-R1-001〜006について、未証明／未決定／未承認の境界、実装前判断事項、GitHub保存履歴を文書上で是正。新方式採択・追加実証・コード変更なし。**

### 0.3 レビューの判断境界

今回の承認を適用した後も、少なくとも次の判断は別扱いとする。

| 判断 | 現在の効力 |
|---|---|
| D-01〜D-10 | 原要件として採択済み。書き換えない |
| Q1 | 設計方針採用済み |
| Q10 | 防御対象と評価条件に限りOWNER_ADOPTED |
| Q2〜Q7 | 既存推奨設計方針のまま OWNER_ADOPTED（実装未承認） |
| Q8 | 信頼済み起点＋Core管理Context：OWNER_ADOPTED_CONDITIONAL（FEASIBILITY_NOT_VERIFIED） |
| Q9 R1 | 静的調査の履歴。安全性の承認ではない |
| F-01/F-02/F-05検証計画R0・限定静的検証R0 | 計画OWNER_ADOPTED、限定静的検証COMPLETED/CLOSED・全項目NOT_VERIFIED |
| Option A | OWNER_ADOPTED / NOT_EXECUTED（静的証拠の所在・取得可能性の追加調査） |
| Q9-R2・Host検証 | NOT_AUTHORIZED / NOT_EXECUTED |
| 自律Skill生成・実装・設定・GitHub変更 | HOLD / NOT_AUTHORIZED（過去の設計書保存実績は本件の新規書込承認ではない） |
| R3文書受入とR3.1是正案 | R3：OWNER_ACCEPTED、固定HEAD保存済み／R3.1：DOCUMENT_REMEDIATION_COMMITTED、Owner差分再受入待ち・Review Gate再判定待ち |
| CS-R1-001〜005 | 技術的なFindingはOPEN。文書による明確化をRESOLVEDとしない |
| CS-R1-006 | 文書中のGitHub履歴を是正。Review Gateによるclosureは未判定 |

## 1. 元のゴールとMVP境界

### 1.1 目的

OpenCode v2による通常の開発中に、既存Skills、ChronosGraphの記憶、およびCoding Agentの通常能力を優先活用し、それでも必要かつ再利用価値がある場合に限り、**新規のプロジェクト内一時Skillを生成・静的検証・現在のタスクで利用・軽量評価**できるようにする。正式昇格と共通配布はHuman Ownerの個別の明示的判断後とする。

**第一優先は元の開発タスクの達成であり、Skill生成数ではない。** Skill生成が失敗しても通常の開発タスクは可能な限り継続する。他方、不明なSkill生成・利用の権限は許可しない。

### 1.2 MVPに含む（要件維持）

- 不足Skillの検知・必要性／再利用価値評価
- SkillPort等による既存Skills調査、およびChronosGraph記憶参照
- 新規一時Skillドラフト生成、静的検証、現在のタスクだけでの利用
- 累積予算・試行上限の制御
- 元タスク結果を使った軽量評価と未評価事項の記録
- プロジェクト内期限付き保持、次回の自動利用禁止
- Human Owner向け正式採用候補の提示
- OpenCode v2でのE2E確認（**まだ未実施**）

### 1.3 MVPに含めない（要件維持）

既存Skillの自動改善、継続的な自己改善ループ、未承認の正式登録・横断配布、生成スクリプトの自動Sandbox実行、巨大ベンチマーク、専用Web UI、全Coding Agentの初期対応、APM/SkillPortの置換、ChronosGraph/ChronosGateの全面再設計。新たに見つかった技術課題をHuman Owner判断なしにMVP必須工程へ追加しない。

### 1.4 固定されたHuman Owner要件

| ID | 採択済みの要件（概要） | 状態 |
|---|---|---|
| D-01 | 開発中の不足をハイブリッド検知 | ADOPTED |
| D-02 | 一時Skillを自律生成し、静的検証後に当該タスクで利用 | ADOPTED |
| D-03 | 正式昇格は評価とHuman承認後、Git操作・配布は別権限 | ADOPTED |
| D-04 | 不足があるだけでは生成せず必要性・再利用価値を評価 | ADOPTED |
| D-05 | トークン・時間・試行の予算上限。原則最大2回 | ADOPTED |
| D-06 | 軽量な実用評価。可能ならSkillなしとの比較 | ADOPTED |
| D-07 | 未承認一時Skillはプロジェクト内に限定 | ADOPTED |
| D-08 | マルチエージェントを念頭に、初期対象はOpenCode v2 | ADOPTED |
| D-09 | 一時Skillは期限付き保存。再評価可能だが次回自動利用しない | ADOPTED |
| D-10 | 初期対象は新規Skill生成のみ | ADOPTED |

## 2. 設計決定の台帳（Q1〜Q10）

| 論点 | 採択された設計方向・調査履歴 | 権限状態 |
|---|---|---|
| Q1 | B：Agentが意味判断、決定論的Coreが検索・予算・ポリシーを検証 | **設計方針採用済み** |
| Q2 | C：`MCP_PRIMARY / STDIO / CLI_MINIMAL / SHARED_CORE / HTTP_DEFERRED` | **OWNER_ADOPTED** |
| Q3 | C：Agent提案＋Coreによる必要性・重複・リスクの保守的検査 | **OWNER_ADOPTED** |
| Q4 | C：Root Task累積枠＋生成要求単位枠の二層予算 | **OWNER_ADOPTED** |
| Q5 | C：Core管理Root Task Context、親子継承・原子的予約 | **OWNER_ADOPTED** |
| Q6 | C：予約後、生成開始許可の永続化を消費確定とする | **OWNER_ADOPTED** |
| Q7 | C：Context＋Idempotency Key＋正規化要求Fingerprint | **OWNER_ADOPTED** |
| Q8 | C：信頼済み起点＋Core管理Context（**実現可能性未証明**） | **OWNER_ADOPTED_CONDITIONAL** |
| Q9 | v2.1.4固定・静的検証R1実施。Root権限は未証明 | REVIEW_COMPLETED / FEASIBILITY_NOT_VERIFIED |
| Q10 | A＋Bを基準、実効的にC相当ならCとして評価し、C除外は分離証拠必須 | **OWNER_ADOPTED** |

注記：Q9は設計採択事項ではなく、証拠調査の履歴である。Q2〜Q8の採択根拠は今回の明示回答であり、Q1の採用から自動推論したものではない。

### 2.1 Human Owner承認の適用範囲

- **承認入力:** 本対話における「推奨案をそのまま承認します」。直前の提案は、R1にあるQ2〜Q8推奨方針を確認して採否を決める工程だったため、既存推奨案を**内容変更なしで設計方針として採択**したものとして記録する。
- **採択対象:** Q2〜Q7のC案およびQ8の条件付きC案（§2の表）。Q10やD-01〜D-10の既存採択を変更しない。
- **採択に含まれないもの:** Root信頼済み起点の実証、実行時の防御保証、設計詳細の未決定部分、F-01/F-02/F-05検証計画作成、Q9-R2、Host検証、実装・GitHubへの変更。
- **Q8の条件:** Trusted Root Bootstrap が成立するまでは自律Skill生成HOLD。Caller分類は採択済みQ10のA＋B、実効Cを使用。
- **監査上の留保:** この承認解釈は既存の候補方式を採択するもので、Q8に新たな成立証拠が提出されたことを意味しない。


## 3. 関連資産と境界（読み取り専用の資料確認）

| コンポーネント | 役割と利用方針 | 改変・権限上の境界 |
|---|---|---|
| OpenCode v2 | 初期Coding Agent、既存LLM契約・利用枠を活用 | v2.1.4のRoot発行信頼境界は未証明。Host操作は未承認 |
| ChronosSkills Core（論理設計・未実装） | 必要性検査、重複抑止、予算、Context、候補ライフサイクル | Agentの自己申告をRoot認可に用いない |
| ChronosGraph | 経験・知識・手順の記憶検索・保存 | 現行仕様の制約を尊重。本体変更をMVP前提にしない |
| SkillPort | 検索、Agent Skills形式検証、候補の既存機能活用 | 単純な仕様検証だけで安全性検証完了とみなさない |
| ChronosGate | 既存ツール操作のセキュリティ評価 | ChronosSkillsが判断を代行・上書きしない |
| Microsoft APM | 採用済みSkills等の依存関係管理 | Human承認なしに依存関係・配布を変更しない |
| dotfiles-ai / agent-skills | 既存設定と正式採用Skillsの管理 | 初期ドラフトがグローバル領域を変更しない |

R0作成時に確認した既存資料：`yohi/chronos-skills/REQUIREMENTS.md`、ChronosGraph `SPEC.md`、ChronosGate `README.md`、SkillPort `README.md`、Microsoft APM `README.md`、dotfiles-ai `apm.yml`。本改訂で再実証したものではない。資料が示す公開機能の存在と、ChronosSkills統合時の実行保証は異なる。外部資産との実結合・バージョン互換・権限挙動は未検証。

### 3.1 既知のChronosGraph制約（元要件に依拠）

- `memory_search`の`memory_type`は現在厳密な絞り込みを保証しない。
- `memory_search_graph`の`edge_types` / `depth`は専用グラフ探索として厳密に反映されない。
- `project`正規化を考慮し、プロジェクト境界を検索パラメータだけに委ねない。検索結果・ドラフトの**認可強制責任はUNDECIDED**であり、実装前の判断事項（§7.4）。
- 過去の記憶は現行コード・公式仕様より優先しない。
- `CHRONOS_INGESTION_MODE`による既存の保存挙動と競合させない。

## 4. 全体構成（採択された論理方針と未実証の信頼境界を区別）

```text
                         OpenCode v2 Coding Agent
                         (通常の開発タスク)
                                  |
                不足検知・既存手段の検索・提案
                                  v
                [ChronosSkills Adapter / MCP等]
                    Q2: OWNER_ADOPTED（未実装）
                                  |
                                  v
                +------------------------------+
                | ChronosSkills Core           |
                | 検索検証 / 必要性 / ポリシー    |
                | 予算 / Attempt / 評価記録     |
                +------------------------------+
                 |           |            |
                 v           v            v
             SkillPort  ChronosGraph  プロジェクト限定の候補
                                          |
                                     静的検証後のみ
                                     元タスクで利用

   [信頼済みRoot発行経路 Q8: 条件付き採択、未証明]
                   - - - ? - - -> CoreのRoot Task Context

   ChronosGate: 既存のツール実行認可境界を尊重
   Human Owner: 正式Skill採択・Git変更・配布を個別に判断
```

設計案の比較（各Qの選択結果を反映）：

| 論点 | 代替案 | 採否・理由 |
|---|---|---|
| 推論の責務 | Coreによる直接LLM呼び出し vs Agentの既存LLM活用 | Q1ではAgent意味判断＋Core検査を採用。追加API課金を必須化しない |
| 接続 | MCP / HTTP / CLI中心 | Q2はMCP優先を採択（STDIO、CLI最小、共通Core、HTTP延期）。実装は未承認 |
| 生成の起動 | Agent裁量のみ vs Core一律判断 vs ハイブリッド | Q3はハイブリッド方針を採択。実装・検証は未実施 |
| Root権限 | Agent自己申告 vs Core発行IDのみ vs 信頼済み起点 | Q8は信頼済み起点を条件付き採択。ただし成立は未証明 |


### 4.1 実装に依存しない責務境界

- **Coding Agent（意味判断）:** Skill不足候補、検索対象、再利用価値、ドラフト本文を提示する。Agentの申告はRoot発行・予算増額・正式採択の認可証拠ではない。
- **ChronosSkills Core（採択済みの論理設計・未実装）:** 入力・予算・試行状態・冪等性・候補状態の制御を担う方向。Q4〜Q8の論理契約は設計方針として採択されたが、技術的実現性・永続化・実行時安全性は未証明。
- **OpenCode Host/Adapter（候補）:** 現在のAgent/SessionとCoreの接続を担い得る。Pluginの存在・Session ID・Hostイベントだけで信頼済みRoot発行を宣言しない。
- **外部システム:** ChronosGraphは記憶、SkillPortは既存Skills検索・形式検証、ChronosGateはツール操作の既存安全判断、APM等は正式採用後の管理・配布。ChronosSkillsによる一括代行をしない。
- **Human Owner:** Q2〜Q8の採択済み方針を維持しつつ、Root認可境界の証拠評価、未決定の詳細、Skillの正式採用、Git変更・配布をそれぞれ個別に判断する。

### 4.1.1 Q8の四つの権限と成立条件（CS-R1-001／実装前Blocker）

以下は**採択済み論理方針の責務区分**であり、権限発行の実装や、稼働中の認可能力を記述するものではない。

| 権限 | 目的と認可上の区別 | 現在の状態／実装開始前の成立条件 |
|---|---|---|
| **Root Task Context新規発行** | 正規の新規Root Taskに対して発行する特別な権限。同じ作業の再発行・枠再取得と区別 | 信頼済み発行起点、非認可Callerからの完全仲介、発行者の真正性は**NOT_ESTABLISHED**（F-01/F-02/F-05）。設計方針のみ条件付き採択 |
| **既存Contextの利用** | 検証済みの当該Contextで照会・提案等を行う権限。新規Root発行を含めない | CallerとProject/Taskの一致、許可範囲の検証方法は**UNDECIDED** |
| **子Agentへの委譲** | 親Rootの権限・予算・期限の内側で必要な操作を限定継承。子Agentによる独立Root新設ではない | 親子関係と許可範囲の真正性、越権防止方法は**UNDECIDED / NOT_VERIFIED** |
| **Attempt予約・予算消費** | 認可された既存Root内で開始許可と試行消費を管理する権限。Root発行やHuman承認ではない | 権限チェックと原子的永続化の方式は**UNDECIDED**、実証は**NOT_VERIFIED** |

**実装着手前の成立条件（今回の検証対象ではない）**：信頼できる発行元と完全仲介点を特定し、Caller A＋B（実効能力がC相当ならC）からの全到達経路・なりすまし不能性・権限共有の境界・拒否時のfail-closedを根拠付きで示すこと。OpenCodeの`session.created`、Session ID、Metadata、MCP接続、一般Server認証のみでRoot発行権限を推定しない。証明できなければ`FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED`として**AUTONOMOUS_GENERATION_HOLD継続**。本書は新たなRoot認可機構を採択しない。

### 4.1.2 Adapter／Coreの公開契約と失敗の責務（CS-R1-004／技術判断未決定）

Q2の`MCP_PRIMARY / STDIO / CLI_MINIMAL / SHARED_CORE / HTTP_DEFERRED`は採択済み。一方、具体的な公開API・型名・type shape・フィールド・シリアライズ形式・プロトコル互換性は**UNDECIDED**であり、ここで新しい仕様を採択しない。

| 実装前に決定する境界 | 必要な判断（方式・型・名前は未確定） |
|---|---|
| Adapter → Coreの主要操作 | 既存Skill/記憶検索、生成必要性評価、Root Context検証、Attempt予約・開始、候補の静的検証・一時利用・評価・状態照会のうち、何を公開し、何をCore内部に限定するか |
| Root Contextの受渡し | どの入力を非信頼データとして扱うか、主体・Task・Project・権限範囲の照合をどこで行うか。Context文字列を所持しているだけでRoot新規発行は認めない |
| 入出力の主要type shape | Request/Responseと結果状態、Attempt/Context/候補の識別子、必須・任意情報、境界を越えて渡せない内部権限材料の区別 |
| 結果・失敗分類 | `UNDETERMINED`（判断不能・生成不可）、`CONFLICT`（同一Keyで異なるFingerprint）、認可失敗、予算不足、依存停止、永続化不明・再送・期限切れ等の区別と、どこで確定するか |
| Agentへの失敗伝播 | 失敗を許可・成功に変換しない。Coreの拒否や不明状態をAdapterが隠さず、元タスクへの通常復帰と生成側fail-closedを両立する責任の割当て |
| 機密データと監査 | 入力本文・Project/Task識別子・認可素材・生成候補・Fingerprint・監査イベントの扱い、記録者・保持先・秘匿化・アクセス範囲を別途決める |

**判断ゲート**：これらは採択済みQ1〜Q8を実現するための**既存スコープ内の実装前設計判断**。具体化にはHuman Ownerの別途承認が必要であり、未決定のまま実装計画がAPI／typeを新規採択したと扱わない。

### 4.1.3 一時SkillのOpenCode v2への提供境界（CS-R1-002／技術判断未決定）

一時Skillは**静的検証合格後、生成元の現在のタスクだけに限り利用し、別タスクへは自動持越ししない**というD-02/D-07/D-09の契約を維持する。ただし、そのための具体的なロード・発見経路は**API実装上の細部ではなく、実装前に選択が必要な方式**である。

| 境界 | 実装前に決定する事項（現時点ではUNDECIDED） |
|---|---|
| 一時Skillの提供責任 | Draftの保存先・公開者、発見可能な範囲、SkillPort/Adapter/Core/Hostのうち提供・解除の責任主体 |
| タスク限定の利用認可 | Current Task/Root/Projectに対して利用できるかを検証する主体、権限拡大を防ぐ仕組み |
| グローバルSkills混入防止 | 既存Skill配置・APM・dotfiles-ai・Agent全体設定へ未承認Draftを混入・上書きしない実効的境界 |
| タスク終了時の無効化 | 終了判定の信頼元と、有効化・キャッシュ・発見状態を失効させる責任主体、終了通知逸失時のfail-closed |
| 次回の自動利用禁止 | 期限付き保存された候補を再評価目的で参照する権限と、別タスクでの自動ロード禁止の区別 |
| v2.1.4成立確認 | 固定OpenCode `v2.1.4`で、選択方式が上記境界を実際に満たす証拠の取得が必要。**現在はNOT_VERIFIED** |

**判断ゲート**：提供／解除方式の選択、v2.1.4成立確認、実装着手は各々Human Ownerの別判断。現在は自律Skill生成と一時利用への遷移をHOLDし、未承認の検証や代替方式採択は行わない。

### 4.2 信頼境界が証明されない場合の動作

`Root Context新規発行が未認可／識別不能／不明`なら、Skillsの**自律生成・利用への遷移を認めない**。既存Skills・記憶の参照や、元のCoding Agentによる通常の開発まで失敗扱いにはしない。検索のみで動作する状態を、AC-02/AC-11を満たしたMVPとは呼ばない。

## 5. 論理ワークフロー（未実装の設計候補）

1. Coding Agentが通常の開発作業中にSkill不足候補を識別する（D-01）。
2. 既存Skills・ChronosGraph・Agent標準能力を調査する。既存手段で十分なら`SKIP`（D-04）。
3. Agentが不足理由、再利用価値、代替手段、予算影響などをCoreへ提案する（Q1、Q3採択方針）。
4. Coreは必要性・既存資産重複・入力完全性・リスク・残予算を検査。結論が不明なら`UNDETERMINED`として**生成しない**。
5. 生成が許容できる候補であっても、Root発行権限・継承関係が未証明の現在の実行環境では**生成をHOLD**（Q8/Q9）。
6. 将来必要な権限と条件が実証・承認された場合にのみ、CoreによるAttempt予約→開始許可/消費永続化→Agentによる新規ドラフト生成を可能とする（Q4〜Q7採択方針、未実装）。
7. Skillドラフトを、形式・参照整合・資格情報混入・危険指示・名前衝突等について静的検証する（D-02、FR-05）。
8. 合格した一時Skillのみ、権限拡大なしで**元のタスク限定**利用する（FR-06）。
9. 成果と限界、測定可能な時間・トークン、未評価項目を記録する（D-06）。
10. プロジェクト内で期限付き保管し、次のタスクでは自動利用しない（D-07、D-09）。
11. Human Ownerが正式採択を判断する。採択とGit操作・APM配布の実行権限は別（D-03）。

### 5.1 実行経路に置けない未決定境界（CS-R1-002／004）

§5のStep 3〜8は**論理ワークフロー**であり、Adapter/Coreの公開操作、認可入力、結果分類、Agentへの失敗伝播、およびOpenCodeへの一時Skill提供・失効の手段を確定したものではない。これらの実装前判断は§4.1.2／§4.1.3に列挙した。`CONFLICT`と`UNDETERMINED`、認可失敗と予算不足を同一の「再試行可」にまとめず、発行不能・未確定状態からの生成開始を認めない。実効境界が未証明の現在、Step 6〜8の自律経路は**NOT_AUTHORIZED / HOLD**である。

## 6. 論理状態と不変条件

### 6.1 生成判断の状態

`DETECTED → SEARCHED → EVALUATED → GENERATE_CANDIDATE / SKIP / UNDETERMINED`

`SKIP`または`UNDETERMINED`からは新規Skill生成を始めない。これは設計上の論理分類であり、具体的なイベント名/APIは未確定。

### 6.2 試行と予算（Q4〜Q7：OWNER_ADOPTED / NOT_IMPLEMENTED）

```text
          [RESERVED] -----> [RELEASED]   開始未許可の証明がある場合のみ
              |
              | Coreが開始許可と消費を永続化
              v
           [STARTED] ----> [COMPLETED]
              |
              +----------> [UNCERTAIN]

   ※ 状態不明時は既存の予約／消費済み試行を保持。
   ※ UNCERTAINを理由に別の試行として二重計上しない。
   ※ RESERVEDでも開始許可が出ていないと原子的に証明できなければ返却しない。
```

- `STARTED`は**LLMの実行開始の観測ではない**。Coreが開始許可と試行消費を永続化した状態。
- 1 Root Taskあたり生成・修正Attemptは原則最大2件。親Agent・子Agent間で共有し、二層予算を適用する。
- `予約中の枠 + 消費確定済み試行 + 不明のため保持中の枠 ≤ 2`。一つのAttemptを重複して区分計上しない。この不変条件はQ4〜Q7の**採択された論理設計**である（検証・実装は未実施）。
- 予約/開始許可は並行要求で原子的に処理する設計候補。永続化技術・障害復旧の具体方式は未決定。
- 同じRoot Context＋Idempotency Keyで同じFingerprintなら**同じAttemptの状態照会**。`STARTED`後の再送でも開始許可は再発行しない。
- 同一Key・異なるFingerprintは`CONFLICT`。`RELEASED`は再予約で復活させない。
- Root終了後は生成再開・予算復活を許さず、許可された状態照会のみ。記録保持期間は未決定。
- Agentへ渡った開始許可による**物理的なLLM呼び出しの厳密な単回性までは未保証**。Core管理下の認可済み生成要求に保証を限定する。
- トークン使用量が取得できない場合は`UNKNOWN`として扱い、取得可能な試行数・時間等で保守的に制限する。厳密なトークン上限を保証したと表示しない。

### 6.2.1 Root Task共有予算の所有・原子性・復旧（CS-R1-003／技術判断未決定）

Q4〜Q7の論理契約は維持しつつ、**authoritative owner・永続ストア・原子操作の単位と順序はまだ採択されていない**。Root Taskに属する全Agent・子Agentが同じ予算を消費することを設計要件とし、Agent側のローカルカウンタや自己申告を権威ある値としない。

| 実装前に決定する事項 | 成立させる契約・未解決点 |
|---|---|
| **Root Task予算のauthoritative owner** | Coreが論理上管理する累積枠について、再起動・複数Adapter/Agentから見ても唯一の権威ある状態所有者と保存境界を指定する必要がある（方式・所在はUNDECIDED） |
| **Attempt状態の永続化責任** | `RESERVED / STARTED / UNCERTAIN / RELEASED / COMPLETED`等を誰が永続化し、読み出し・監査・回収するか。Context期限・Root終了・重複防止情報も含める |
| **原子的予約と開始許可** | 全Agentに共有する残予算確認、Idempotency Key/Fingerprint照合、予約、開始許可＋消費確定が競合時にも単一の確定履歴として成立する境界。具体的なトランザクション・排他方式はUNDECIDED |
| **再送・並行要求** | 同一Context＋Key＋Fingerprintは既存Attempt照会に収束。同一Keyの異なるFingerprintは`CONFLICT`。子Agentも新規Root/新規枠を勝手に発行できない |
| **クラッシュ・復旧** | 永続化済み開始許可と送信済み応答の間で中断しても、新規試行や許可再発行として計上しない。判別不能は`UNCERTAIN`／消費枠保持とし、証拠なしの返却禁止 |
| **再起動後の状態復元** | Root累積使用量、保留枠、Attempt遷移、Idempotency tombstone、期限・終了状態を復元できること。復元不能なら新規生成fail-closed |
| **トークン計測不能時** | 未測定値を0や確定値にしない。試行数・時間・観測できる制約による保守的制御の具体方針を選択し、厳密なトークン上限を保証したと主張しない |

**状態不変条件（採択済み論理契約の確認）**：`STARTED`は開始許可と消費が永続的に確定した状態で、呼出側への応答逸失を理由に再発行しない。`UNCERTAIN`は開始したか・返却できるかを証明できない状態であり、枠を保持して勝手に解放しない。`RELEASED`は開始許可が確実に発行されていないと証明された予約についてのみ許され、後日の同じAttemptで再始動・枠復活しない。各Attemptは互いに排他的な計上区分に一度だけ属し、Root Task全体で最大2回の上限を破らない。

**判断ゲート**：ストア・排他方式・更新APIを本書で採択しない。方式の決定、並行性・障害回復の成立確認、実装開始はいずれも別判断であり、これを新規サービスの自動追加理由としない。

### 6.3 一時Skill候補のライフサイクル

```text
DRAFT → STATIC_VALIDATION → VERIFIED / REJECTED
VERIFIED → CURRENT_TASK_USE → EVALUATED
EVALUATED → PROJECT_LOCAL_RETAINED → EXPIRED
EVALUATED → OWNER_REVIEW → APPROVED / REJECTED / DEFERRED
APPROVED ──[別途の書込・配布許可]──> FORMALLY_PUBLISHED
```

この図は論理上の設計候補。`VERIFIED`は**静的検証合格のみ**を意味し、実行安全性や有用性の証明ではない。`APPROVED`と`FORMALLY_PUBLISHED`は別の権限と操作である。

## 7. Root Task認可とQ10防御範囲

### 7.1 Q8：条件付き設計採択、実現可能性未証明

- Root発行と、既存Context利用・子Agent委譲・試行消費・状態照会を別権限として扱う。
- Root Task IDやSession ID、Metadata、MCP接続そのものを新規Root発行の認可根拠としない。
- 親子継承はProject・予算・期限・認可範囲を超えない。Agent自己申告だけでは認めない。
- 正当な新規Root発行と、同じ作業についての予算再取得を完全に区別できるとは未証明。
- 信頼済み起点の実現可能性が不明な状態では**自律生成経路を利用しない**。
- **CS-R1-001:** §4.1.1の四権限は別物であり、Q8方針採択はRoot発行機能の存在や完全仲介の成立を示さない。実装前の成立条件が未充足ならHOLDを継続する。

### 7.2 Q10：OWNER_ADOPTED（防御対象・評価条件のみ）

| Caller | 有効な防御評価境界 |
|---|---|
| A：MCP-only | 通常のMCPツール呼び出しを利用できる非認可Caller |
| B：Agent Tool | MCPに加えて通常許可されたShell・一般ツール等を利用できるCaller |
| C：任意コード | 実効的にHostと同等の任意コード実行権限がある場合、Cとして評価する |

A＋Bを基準防御対象とし、ツール名ではなく**実効能力**で分類する。Cを除外するには環境・権限・資格情報の実効的分離証拠が必要。単なる「利用させない」というAgent向け指示では足りない。

### 7.3 Q9-R1の結果と未証明事項

固定OpenCode：`v2.1.4` → `68bc26b7da1990ce10ffecfbc1e09eb13aa5d824`。

- **F-01 Root発行:** 一般Session作成とChronosSkills Root発行は異なる。`session.create`や`session.import`由来のイベントだけでは発行を認可できない。
- **F-02 Caller識別:** Server接続資格・Session ID・Metadataでは独立したRoot発行権限を証明できない。
- **F-05 MCP迂回:** 通常MCPツールにはPermission評価があるが、ShellやHost APIなど他経路をすべて防げるとは証明していない。

**判定:** `FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED`。一部の方式に対する静的反例があることと、OpenCode v2.1.4全体で実現不可能であることは区別する。**CS-R1-001はBlockerとしてOPEN**。Q9-R2やHost実行検証を自動開始しない。

**R2以後の証拠履歴（設計変更なし）**：Owner採択済み最小検証計画R0に基づく限定静的検証R0は`COMPLETED / CLOSED / STOP`。F-01、F-02、F-05の判定はいずれも`NOT_VERIFIED`。OpenCodeの一般Session生成をChronosSkills Root認可に昇格できないこと、Runtime/Host分離証拠とRoot発行の実体が足りないことを記録した。Option A（不足する静的証拠の所在・取得可能性の確認）は採択済みだが`NOT_EXECUTED`。**設計書完成の前提作業として追加調査を義務化しない。**

### 7.4 プロジェクト認可の強制点（CS-R1-005／技術判断未決定）

Project名やChronosGraphの`project`パラメータは**論理識別情報**にすぎず、それ自体が安全な権限境界であるとはみなさない。

| 実装前の責任境界 | 判断が必要な点（現時点UNDECIDED） |
|---|---|
| Project識別の信頼元 | Agentが自己申告したProject/ディレクトリ名と、信頼された実行・リポジトリ／Task文脈との対応を誰が検証するか |
| 参照要求の認可 | ChronosGraph検索・既存Skill参照・保存済み候補参照の各Caller/Project/Taskアクセスをどの主体が判定し、拒否するか |
| 検索結果の境界検査 | ChronosGraphの検索結果や過去記憶が他Projectのデータを含む場合の検査責任。検索filterだけで完全なアクセス制御が得られるとしない |
| ドラフト・評価結果の所有 | 保管先、読取・更新・失効権限、Project/Taskとの対応、越境表示禁止、正式昇格前のアクセス範囲を決める |
| 不正横断参照のfail-closed | Caller／Projectの認可や検索結果の帰属が不明なときは候補・記憶の提供を停止し、元タスク全体の失敗と混同しない |
| 別タスク利用の禁止 | 保存済み候補の明示的な参照・再評価と、次回の自動提供・自動ロードを分離し、失効とキャッシュの境界を明示する |

**判断ゲート**：強制点（Adapter/Core/連携先のどこか）と責任割当ては採択前。ChronosGraph本体の全面改修を前提にしない。具体化と成立確認が必要でも**本書の文書是正だけでは実施権限が生じない**。

## 8. 安全性、コスト、障害時の契約

1. **Skill生成側はfail-closed:** 予算、権限、重複、静的検証の状態が不明なら新規生成や利用を許可しない。
2. **元タスク側は継続優先:** ChronosSkills障害・生成失敗で既存Coding Agentの通常開発を不要に止めない。失敗を成功と表示しない。
3. **既存ツール権限を継承:** Skillの記述や生成スクリプトによって権限を拡大しない。ChronosGateの判断を代行しない。
4. **機密情報保護:** Skill本文・Fingerprint・評価記録・ログに秘密情報を無条件に保存しない。単純なハッシュ化だけで秘匿性を確保したとみなさない。
5. **既存資産の保護:** 未承認Skillで既存Skillsを上書きしない。APM・dotfiles-ai・agent-skillsを無断変更しない。
6. **コスト:** 既存LLM契約・枠を優先し、追加従量課金APIを必須としない。計測不能な値を確定値として扱わない。
7. **権限委譲:** Root発行、Context利用、子Agent委譲、試行消費、正式採択、Git操作、配布を同一権限とみなさない。
8. **Project境界:** ラベル付与だけでアクセス制御を保証したとみなさない。横断検索や自動再利用は無承認で行わない。検索結果と候補の参照認可の実際の強制主体は**UNDECIDED**（§7.4）。不正な越境参照はfail-closed。


## 9. 要件への追跡可能性（設計内容／未検証を分離）

この対応表は元要件を採択された論理設計へ写像したものであり、テストPASSや実装完了を示さない。FR/NFRの内容は`REQUIREMENTS.md`を正とする。

### 9.1 機能要件 FR-01〜FR-12

| 要件 | 設計書内の対応 | 決定・証拠状況 |
|---|---|---|
| FR-01 不足検知 | §5、§6.1 | D-01・Q3方針採択済み。未実証 |
| FR-02 既存資産検索 | §3、§5、§7.4 | D-04採択済み。検索結果の越境検査・参照認可の強制点UNDECIDED、未実証 |
| FR-03 必要性・再利用価値 | §5、§6.1 | Q1/Q3設計方針採択済み、未実証 |
| FR-04 新規ドラフト生成 | §1、§5、§6.3 | D-02/D-10採択済み。生成はHOLD |
| FR-05 静的検証 | §5、§6.3、§8 | 必要条件採択済み。具体ルール・効果は未実証 |
| FR-06 当該タスクでの一時利用 | §4.1.3、§5、§6.3、§8 | D-02/D-07採択済み。OpenCode v2.1.4への提供・発見・タスク限定認可・失効・自動再利用禁止の技術方式は**実装前判断UNDECIDED / NOT_VERIFIED** |
| FR-07 予算管理 | §6.2、§6.2.1 | D-05/Q4〜Q7方針採択済み。共有予算の権威ある永続所有者・原子操作・復旧方式はUNDECIDED、未実証 |
| FR-08 軽量実用評価 | §5、§6.3 | D-06採択済み。評価尺度未決定 |
| FR-09 期限付き保持 | §4.1.3、§6.3、§7.4、§8 | D-09採択済み。保持日数・容量・提供/失効責任・別タスク自動利用の禁止方式UNDECIDED |
| FR-10 Human承認による昇格 | §5、§6.3 | D-03採択済み。Git/配布の権限は別 |
| FR-11 元タスクへの復帰 | §5、§8 | 要件確定。障害時の実動作未実証 |
| FR-12 追跡可能性 | §6〜§8 | 情報項目は原要件。保存方式・秘匿化未決定 |

### 9.2 非機能要件 NFR-01〜NFR-07

| 要件 | 設計書内の対応 | 留保 |
|---|---|---|
| NFR-01 コスト | §4、§6.2、§8 | 既存契約優先。厳密計測不能時はUNKNOWN |
| NFR-02 エージェント非依存 | §1、§4 | Q2採択済み。共通Coreは未実装 |
| NFR-03 障害分離 | §4.2、§8 | 元タスク継続を優先、未実証 |
| NFR-04 安全・権限 | §6〜§8 | Q8方針は条件付き採択・実現可能性未証明。Q10の防御範囲は採択済み |
| NFR-05 プロジェクト分離 | §6〜§8（特に§7.4） | 識別ラベルだけでは不十分。参照認可・検索結果検査・ドラフトアクセスの強制主体UNDECIDED |
| NFR-06 再現性 | §6.2、§8 | Q7採択済み。Fingerprint処理は未実装・未実証 |
| NFR-07 運用簡素化 | §1、§3〜§4 | 新規大規模基盤を必須工程にしない |

## 10. 検証観点（計画の承認・実行を意味しない）

要件の受入条件を設計上で追跡するための対応表。**AC-01〜AC-11の実行検証は未実施**。Q8の限定静的検証はMVP受入テストの代替ではない。

| 受入条件 | 設計上必要な観測・評価 | 現在の状態 |
|---|---|---|
| AC-01 | 既存手段が十分なら生成されない | NOT_TESTED |
| AC-02 | 必要な場合に一時Skillを自律生成 | HOLD / NOT_TESTED |
| AC-03 | 利用前に静的検証 | NOT_TESTED |
| AC-04 | 生成元タスクだけで一時Skillを利用 | NOT_TESTED |
| AC-05 | 試行・時間・トークン予算が機能 | NOT_TESTED |
| AC-06 | 生成失敗でも元タスクへ復帰 | NOT_TESTED |
| AC-07 | 評価・未評価・根拠の追跡 | NOT_TESTED |
| AC-08 | 未承認Skillの分離と期限管理 | NOT_TESTED |
| AC-09 | 承認前の正式公開・既存Skill変更なし | NOT_TESTED |
| AC-10 | ChronosGraph既存契約を破壊しない | NOT_TESTED |
| AC-11 | OpenCode v2で検知→生成→検証→利用→評価E2E | HOLD / NOT_TESTED |

## 11. 実現可能性未証明・実装詳細未決定事項と判断ゲート

### 11.1 Q2〜Q8の採択状態

`Q2〜Q7 = OWNER_ADOPTED`、`Q8 = OWNER_ADOPTED_CONDITIONAL`。今回のHuman Ownerの明示承認を反映したもので、単なる文書作成・黙示的合意による採択ではない。Q8の実現可能性は依然 `FEASIBILITY_NOT_VERIFIED`。

### 11.2 根拠不足・技術判断未決定・実施未承認の分離（CS-R1-001〜005）

- Q8の信頼済みRoot発行経路の実現可能性・Caller A/B/Cに対する完全仲介（F-01/F-02/F-05）。
- Host再接続・Root再発行と同一作業の継続との区別の実証。
- Coreの保存方式・原子的操作・障害復旧・期限・予算初期値。
- OpenCode v2に一時Skillを提供する際の実際のロード／無効化方法。
- SkillPortとChronosGraph検索結果の厳密なプロジェクトアクセス制御。
- 静的検証の具体的ルール、評価尺度、候補保持日数・容量上限。
- Human承認後の正式保存先と配布手順、および別個のGit書込承認。
- 完全なMVP E2EおよびHost実行時の安全性実証。

#### 11.2.1 実装前Blocker／未決定事項と必要証拠の台帳

| Finding | 問題の種類 | 実装前に成立・確定が必要な条件 | 現在の正しい状態 | 参照 |
|---|---|---|---|---|
| **CS-R1-001 (Blocker)** | Q8の**実現可能性未証明** | 信頼済みRoot新規発行起点、A+B／必要に応じCに対する完全仲介と非認可Caller拒否を証拠で示す | **OPEN / FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED / HOLD** | §4.1.1、§7.1〜7.3 |
| **CS-R1-002 (Major)** | 一時Skill提供方式の**技術判断未決定** | v2.1.4での提供・発見・タスク限定認可・終了時失効・次回自動利用禁止・グローバル混入防止の責務・成立条件を選択／確認 | **OPEN / UNDECIDED / NOT_VERIFIED** | §4.1.3、§5.1、§9.1 FR-06 |
| **CS-R1-003 (Major)** | 共有予算の権威・永続化・原子性の**技術判断未決定** | authoritative owner、Attempt永続化責任、予約/開始許可の原子境界、再送・クラッシュ・再起動・計測不能時の方針を確定／確認 | **OPEN / UNDECIDED / NOT_VERIFIED** | §6.2.1 |
| **CS-R1-004 (Major)** | Adapter/Core契約の**技術判断未決定** | 主要操作、Context・Caller認可入力、type shape、`CONFLICT / UNDETERMINED`・認可失敗・予算不足の区別、エラー伝播・監査秘密境界を確定 | **OPEN / UNDECIDED / NOT_IMPLEMENTED** | §4.1.2、§5.1 |
| **CS-R1-005 (Major)** | Project参照強制責任の**技術判断未決定** | Project識別の信頼元、検索結果／候補の参照認可強制主体、越境fail-closed、別タスク自動利用禁止を確定／確認 | **OPEN / UNDECIDED / NOT_VERIFIED** | §3.1、§7.4、§8 |

**用語の使い分け**：`UNDECIDED`は方式や責任分担が未採択、`NOT_VERIFIED`は採択済みの方向の成立証拠不足、`NOT_AUTHORIZED`は調査・実装・Git書込等の実施権限なしを表す。これらの併存は矛盾しない。**CS-R1-001〜005は、本書の追記だけではRESOLVEDでもPASSでもない**。実装前に必要な判断であって、文書を完成させるための自動追加工程ではない。

#### 11.2.2 設計書と実装計画書の照合ゲート

実装計画書は**今回未提供・未照合**。したがって、仕様・用語・type/interface・エラー処理・テスト方針・NFRの相互整合性をPASSとは判定できない。後続に実装計画書が作成・提示された場合のみ、両文書について差分確認を行う（別工程・別権限）。この未実施は今回の設計書の**文書限定是正**を否定しないが、実装計画へ移行できる証拠にもならない。

これらは**未解決**という記録であり、Human Ownerの承認なしに新しい必須機構・追加レビュー・実装タスクへ転換しない。

### 11.3 次の意思決定ゲート

1. Q2〜Q8の既存推奨方針はHuman Ownerが採択済み。個々の未確定な技術詳細、Q8実現可能性、実装権限は別扱いとする。
2. Q10は防御対象・評価条件として採択済みだが、Q8の技術的実現可能性を承認するものではない。
3. F-01/F-02/F-05の**最小実現可能性検証計画R0は採択済み**、限定静的検証R0はNOT_VERIFIEDのまま終了した。Option Aは採択済み・未実施。これらの実証作業は**設計書完成判定の必要条件ではない**。
4. 計画作成承認・Q9-R2独立レビュー承認・Host検証承認・新規認可設計承認・実装承認・Git書込承認は、それぞれ独立する。
5. 自律Skill生成HOLDの解除は本書では判断できない。

## 12. 将来拡張（MVPへの自動昇格禁止）

複数Coding Agentへの実運用展開、継続改善ループ、自動的な既存Skill更新、生成スクリプトのSandbox評価、大規模ベンチマーク、専用Web UI、全面的な記憶・認可基盤再設計、常時稼働の認可サービス等は、実施の必要性が示されても別スコープとして扱う。実現可能性が不明なことを理由に新たなサービスや分離プロセスを自動的なMVP必須成果物として追加しない。

## 13. Human Ownerによる次の判断対象（文書審査・実装未承認）

本章は**設計書の文書レビュー**に必要な確認点を示す。追加検証計画の着手や実装指示ではない。

| レビュー対象 | 本書で確認してほしい点 | 既存状態 |
|---|---|---|
| 目的とMVP範囲 | D-01〜D-10およびAC-01〜11を減らしていないか | D-01〜10採択済み、MVP未実装 |
| 役割分担 | Q1〜Q3の設計方針と実装済み保証を混同しないか | Q1〜Q3採択済み、未実装 |
| 予算とAttempt | Q4〜Q7の論理設計を実装済み契約として読めないか | 採択済み・未実証 |
| Root信頼境界 | Q8未証明なのに実現可能と誤記していないか | 未証明 |
| 防御対象 | Caller A＋Bを基準、実効Cを正しく分類しているか | Q10 OWNER_ADOPTED |
| 検証工程 | 調査結果をPASS・実行承認へ読み替えていないか | Q9-R1完了、限定静的検証R0はNOT_VERIFIEDでCLOSED、Option A未実施 |
| 外部依存 | ChronosGraph・ChronosGate・SkillPort・APMの変更を必須化していないか | 既存境界を維持 |

### 13.1 次に判断できる範囲

本書R3.1の**CS-R1-001〜006に限定した文書是正の充足、未決定事項の露出、採択済み方針との整合性**のみが今回の文書レビュー対象である。R3はHuman Owner受入済みだがR3.1差分の受入は未取得。設計書作成を閉じるかの判断と、Q9-R2、Option A追加調査、Host検証、新規認可方式、実装・Git操作は分離する。

### 13.2 設計書の完成とMVPの完成は別

設計書がレビューを通過しても、自律Skill生成がHOLDならAC-02・AC-04・AC-11は達成済みと判定できない。また、静的検証やSkill検索だけで完成済みMVPとは扱わない。

### 13.3 設計文書の受入チェック（R3ベース・R3.1限定是正）

| チェック | R3の対応 | ドキュメント判定 |
|---|---|---|
| 目的と対象/非対象が定義される | §1 | 記載済み |
| D-01〜D-10とQ1〜Q10の採択状態が区別される | §1.4、§2 | 記載済み |
| 設計案と既存システムの責務境界が分かる | §3、§4 | 記載済み |
| 自律生成・静的検証・利用・評価・正式昇格が追える | §5、§6 | 記載済み（論理設計） |
| 安全性・予算・失敗時の方針が追える | §6〜§8 | 記載済み（未実証を明記） |
| 要件・受入条件と対応付く | §9、§10 | 記載済み（実行結果ではない） |
| 未証明・未決定・別権限を混同しない | §7、§11、§13 | 記載済み |

**文書作成工程の履歴と今回の結論:** R3はHuman Ownerに受入済みで、GitHub固定HEADにコミット済み。本書R3.1はCS-R1-001〜006の**文書限定是正案が作成済み**だが、Review GateのBlocker／Major技術事項は**OPEN**、本書R3.1のHuman Owner再受入も未取得。**設計書完成判定と実装開始判定は独立**し、Q8の未証明を文書作成の無期限延長に転換しない一方、実装可能性を証明したとも扱わない。

## 14. 根拠資料

### 14.1 プロジェクト要件（GitHub読取）

- [ChronosSkills `REQUIREMENTS.md`](https://github.com/yohi/chronos-skills/blob/master/REQUIREMENTS.md) — 確認したGit blob SHA：`490e4d38d022c5ba3c748131775035b0f33f7b21`。元要件のD-01〜D-10、FR-01〜12、NFR-01〜07、AC-01〜11、MVP境界。
- **R3設計書のGitHub保存**：`docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md`、固定HEAD [`9f947ec72b476d1facdda1fad346ae9d73945f44`](https://github.com/yohi/chronos-skills/commit/9f947ec72b476d1facdda1fad346ae9d73945f44)、設計書Blob `7cb1cdd46d0a0f959342481ef3982772fe01b5ae`。**本R3.1差分は設計書1ファイル限定でGitHubに反映済み（コミットIDはGit履歴を正とする）**。
- **Q1〜Q10の設計判断**：本対話で採択された内容がR3の§2および本R3.1の§2に**記載されている**（R3文書としてはリポジトリ保存済み）。一方、対話上のHuman Owner採択の**独立した署名済み証拠・別ファイル／別レコードがGitHubに保存されているかは未確認**。設計書への記載と独立の承認証跡を混同せず、コミットを新規実装承認の証拠としない。

### 14.2 関連リポジトリ（参照のみ）

- [ChronosGraph `SPEC.md`](https://github.com/yohi/chronos-graph/blob/master/SPEC.md)
- [ChronosGate `README.md`](https://github.com/yohi/chronos-gate/blob/master/README.md)
- [SkillPort `README.md`](https://github.com/gotalab/skillport/blob/main/README.md)
- [Microsoft APM `README.md`](https://github.com/microsoft/apm/blob/main/README.md)
- [dotfiles-ai `apm.yml`](https://github.com/yohi/dotfiles-ai/blob/master/apm.yml)

### 14.3 Q9調査で固定したOpenCodeソース

- [OpenCode v2.1.4 commit](https://github.com/anomalyco/opencode/commit/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824)
- [Session Core](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/session.ts)
- [Session HTTP handler](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/server/src/handlers/session.ts)
- [PluginHost](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/plugin/host.ts)
- [MCP Tool](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/tool/mcp.ts)
- [Server authorization](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/server/src/middleware/authorization.ts)

### 14.4 後続記録（文書の背景であり設計の改訂根拠ではない）

- `ChronosSkills_Q8_Minimal_Feasibility_Verification_PLAN_R0.md` — 採択済み。
- `ChronosSkills_Q8_Bounded_Static_Verification_Report_R0.md` — F-01/F-02/F-05はNOT_VERIFIED、作業はCLOSED/STOP。
- Option A — Human Ownerが調査方向性を採択。追加調査はNOT_EXECUTED。

---

**文書の最終ステータス:** `R3_OWNER_ACCEPTED_AND_GITHUB_COMMITTED / R3_1_DOCUMENT_REMEDIATION_COMMITTED_OWNER_REACCEPTANCE_PENDING / CS_R1_001_TO_005_TECHNICAL_FINDINGS_OPEN / CS_R1_006_DOCUMENT_CORRECTED_GATE_NOT_REVIEWED / D01_D10_UNCHANGED / Q1_DIRECTION_ADOPTED / Q2_Q7_OWNER_ADOPTED / Q8_OWNER_ADOPTED_CONDITIONAL / Q9_R1_COMPLETED / Q8_STATIC_R0_CLOSED_NOT_VERIFIED / OPTION_A_OWNER_ADOPTED_NOT_EXECUTED / Q10_OWNER_ADOPTED / Q8_FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED / AUTONOMOUS_GENERATION_HOLD / IMPLEMENTATION_NOT_AUTHORIZED / GITHUB_WRITE_NOT_AUTHORIZED / NO_AUTO_R2`。

> 本書はHuman Ownerが受け入れた**R3の設計判断を維持した文書限定是正案R3.1**であり、安全性証明、Review Gate合格、MVP完成宣言、Option A実施指示、実装・GitHub書き込み許可ではない。
