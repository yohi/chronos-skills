# ChronosSkills — MVP設計書 R3（文書完成候補・実装未承認）

> **文書状態:** DESIGN_DOCUMENT_CANDIDATE_COMPLETE / OWNER_DOCUMENT_REVIEW_PENDING / FEASIBILITY_NOT_VERIFIED / NOT_IMPLEMENTATION_AUTHORITY  
> **作成基準日:** 2026-10-28（JST）  
> **対象:** `yohi/chronos-skills`  
> **設計の基礎:** `REQUIREMENTS.md` + 本対話のQ1〜Q10設計判断記録  
> **権限:** DOCUMENTATION_ONLY / NO_ADDITIONAL_FEASIBILITY_REVIEW / NO_HOST_VERIFICATION / NO_EXECUTION / NO_GITHUB_WRITE  
> **総合状態:** `DESIGN_DOCUMENT_CANDIDATE_COMPLETE / Q2_Q7_OWNER_ADOPTED / Q8_OWNER_ADOPTED_CONDITIONAL / Q10_OWNER_ADOPTED / FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED / AUTONOMOUS_GENERATION_HOLD / IMPLEMENTATION_NOT_AUTHORIZED / NO_AUTO_R2`

## 0. 本書の位置づけと読み方

本書は既存要件・設計対話・Human Ownerの採択記録を統合した**MVP設計書の完成候補**である。今回のゴールは設計文書の作成であって、実現可能性実証・実装・MVP稼働ではない。Q2〜Q7は既存推奨案のまま設計方針として採択、Q8は信頼済み起点＋Core管理Contextを**実現可能性未証明の条件付き設計方針**として採択する。採択は実装可能性の証明、詳細実装仕様の完成、実装・GitHub書き込み承認を意味しない。

- **ADOPTED:** Human Ownerが既に確定した要件・判断。
- **OWNER_ADOPTED_DESIGN_DIRECTION:** Human Ownerが既存推奨方式を設計方針として採択。実装方法・実行許可の確定とは区別する。
- **NOT_VERIFIED / NOT_ESTABLISHED:** 必要な証拠が揃っていない。成立、実装可能性、安全性を断言しない。
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
| 改訂 | R3 — R2の採択済み論理設計を維持。文書完成基準・履歴の最新化だけを実施 |
| 直前版 | `ChronosSkills_DESIGN_OWNER_ADOPTED_R2.md`（R1とR0はその前版） |
| 基準要件 | `yohi/chronos-skills/REQUIREMENTS.md`（R0で照合したGit blobを維持） |
| 実行中のサービスを記述しているか | いいえ。未実装・未実証の候補設計を含む |
| この改訂で新しい設計決定を行ったか | **いいえ**。R2の採択済み設計方針は変更せず、設計書作成をゴールとする記述へ整理 |
| GitHubへの反映 | なし |
| 正式な設計承認 | D-01〜D-10、Q1〜Q8（Q8は条件付き）、Q10の設計方針採択済み。本書R3全体の文書承認は未取得 |

### 0.2 改訂履歴（R0→R1→R2→R3）

- 文書冒頭にレビュー対象と権限上の限界を明示した。
- 要件と候補設計の対応関係を追跡できる表を追加した。
- Q10の実効能力に基づくCaller分類と、Q8未実証の区別を整理した。
- 未確定事項を、未採択の設計決定・実証不足・実装詳細未決定に分けた。
- R1：Human Ownerのレビュー対象を明記した。
- **R2：Human Ownerの「推奨案をそのまま承認」という明示回答により、R1記載のQ2〜Q8方式を設計方針として採択したことを記録。Q8の実現可能性、実証、実装権限は変わらない。**
- R2後：F-01/F-02/F-05の最小検証計画R0がHuman Ownerに採択され、限定静的検証R0を実施・STOP（全項目NOT_VERIFIED）。Option Aの方向性が採択されたが、所在・取得可能性の追加調査は未実施。
- **R3：ゴール逸脱を防ぐため、設計書の完成候補と技術実証の別工程を明確化。採択済み方式の追加・変更なし。**

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
| 自律Skill生成・実装・設定・GitHub変更 | HOLD / NOT_AUTHORIZED |

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
- `project`正規化を考慮し、プロジェクト境界を検索パラメータだけに委ねない。
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

**判定:** `FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED`。一部の方式に対する静的反例があることと、OpenCode v2.1.4全体で実現不可能であることは区別する。Q9-R2やHost実行検証を自動開始しない。

**R2以後の証拠履歴（設計変更なし）**：Owner採択済み最小検証計画R0に基づく限定静的検証R0は`COMPLETED / CLOSED / STOP`。F-01、F-02、F-05の判定はいずれも`NOT_VERIFIED`。OpenCodeの一般Session生成をChronosSkills Root認可に昇格できないこと、Runtime/Host分離証拠とRoot発行の実体が足りないことを記録した。Option A（不足する静的証拠の所在・取得可能性の確認）は採択済みだが`NOT_EXECUTED`。**設計書完成の前提作業として追加調査を義務化しない。**

## 8. 安全性、コスト、障害時の契約

1. **Skill生成側はfail-closed:** 予算、権限、重複、静的検証の状態が不明なら新規生成や利用を許可しない。
2. **元タスク側は継続優先:** ChronosSkills障害・生成失敗で既存Coding Agentの通常開発を不要に止めない。失敗を成功と表示しない。
3. **既存ツール権限を継承:** Skillの記述や生成スクリプトによって権限を拡大しない。ChronosGateの判断を代行しない。
4. **機密情報保護:** Skill本文・Fingerprint・評価記録・ログに秘密情報を無条件に保存しない。単純なハッシュ化だけで秘匿性を確保したとみなさない。
5. **既存資産の保護:** 未承認Skillで既存Skillsを上書きしない。APM・dotfiles-ai・agent-skillsを無断変更しない。
6. **コスト:** 既存LLM契約・枠を優先し、追加従量課金APIを必須としない。計測不能な値を確定値として扱わない。
7. **権限委譲:** Root発行、Context利用、子Agent委譲、試行消費、正式採択、Git操作、配布を同一権限とみなさない。
8. **Project境界:** ラベル付与だけでアクセス制御を保証したとみなさない。横断検索や自動再利用は無承認で行わない。


## 9. 要件への追跡可能性（設計内容／未検証を分離）

この対応表は元要件を採択された論理設計へ写像したものであり、テストPASSや実装完了を示さない。FR/NFRの内容は`REQUIREMENTS.md`を正とする。

### 9.1 機能要件 FR-01〜FR-12

| 要件 | 設計書内の対応 | 決定・証拠状況 |
|---|---|---|
| FR-01 不足検知 | §5、§6.1 | D-01・Q3方針採択済み。未実証 |
| FR-02 既存資産検索 | §3、§5 | D-04採択済み。実結合・重複判断は未実証 |
| FR-03 必要性・再利用価値 | §5、§6.1 | Q1/Q3設計方針採択済み、未実証 |
| FR-04 新規ドラフト生成 | §1、§5、§6.3 | D-02/D-10採択済み。生成はHOLD |
| FR-05 静的検証 | §5、§6.3、§8 | 必要条件採択済み。具体ルール・効果は未実証 |
| FR-06 当該タスクでの一時利用 | §5、§6.3、§8 | D-02/D-07採択済み。OpenCodeロード方式未決定 |
| FR-07 予算管理 | §6.2 | D-05/Q4〜Q7方針採択済み。未実装・未実証 |
| FR-08 軽量実用評価 | §5、§6.3 | D-06採択済み。評価尺度未決定 |
| FR-09 期限付き保持 | §6.3、§8 | D-09採択済み。保持日数・容量未決定 |
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
| NFR-05 プロジェクト分離 | §6〜§8 | 識別ラベルだけでは不十分、強制力未証明 |
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

### 11.2 根拠不足で仕様化していない項目

- Q8の信頼済みRoot発行経路の実現可能性・Caller A/B/Cに対する完全仲介（F-01/F-02/F-05）。
- Host再接続・Root再発行と同一作業の継続との区別の実証。
- Coreの保存方式・原子的操作・障害復旧・期限・予算初期値。
- OpenCode v2に一時Skillを提供する際の実際のロード／無効化方法。
- SkillPortとChronosGraph検索結果の厳密なプロジェクトアクセス制御。
- 静的検証の具体的ルール、評価尺度、候補保持日数・容量上限。
- Human承認後の正式保存先と配布手順、および別個のGit書込承認。
- 完全なMVP E2EおよびHost実行時の安全性実証。

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

本書R3の**構成の完結性・表現・要件追跡・採択済み方針との整合性**のみが次の文書レビュー対象である。設計書作成を閉じるかの判断と、Q9-R2、Option A追加調査、Host検証、新規認可方式、実装・Git操作は分離する。

### 13.2 設計書の完成とMVPの完成は別

設計書がレビューを通過しても、自律Skill生成がHOLDならAC-02・AC-04・AC-11は達成済みと判定できない。また、静的検証やSkill検索だけで完成済みMVPとは扱わない。

### 13.3 設計文書の受入チェック（R3）

| チェック | R3の対応 | ドキュメント判定 |
|---|---|---|
| 目的と対象/非対象が定義される | §1 | 記載済み |
| D-01〜D-10とQ1〜Q10の採択状態が区別される | §1.4、§2 | 記載済み |
| 設計案と既存システムの責務境界が分かる | §3、§4 | 記載済み |
| 自律生成・静的検証・利用・評価・正式昇格が追える | §5、§6 | 記載済み（論理設計） |
| 安全性・予算・失敗時の方針が追える | §6〜§8 | 記載済み（未実証を明記） |
| 要件・受入条件と対応付く | §9、§10 | 記載済み（実行結果ではない） |
| 未証明・未決定・別権限を混同しない | §7、§11、§13 | 記載済み |

**R3文書作成としての結論:** 必要な設計文書の項目は一通り記載したため、`DESIGN_DOCUMENT_CANDIDATE_COMPLETE`とする。Human Ownerが文書として受け入れるかは未判断。**Q8の未証明を文書不備に読み替えない一方、実装可能性が証明されたと扱わない。**

## 14. 根拠資料

### 14.1 プロジェクト要件（GitHub読取）

- [ChronosSkills `REQUIREMENTS.md`](https://github.com/yohi/chronos-skills/blob/master/REQUIREMENTS.md) — 確認したGit blob SHA：`490e4d38d022c5ba3c748131775035b0f33f7b21`。元要件のD-01〜D-10、FR-01〜12、NFR-01〜07、AC-01〜11、MVP境界。
- Q1〜Q10：本対話で提示された設計判断記録。Q10採択に加え、今回のQ2〜Q8の明示採択を含む。**リポジトリへの保存・反映は未実施**。

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

**文書の最終ステータス:** `DESIGN_DOCUMENT_CANDIDATE_COMPLETE / OWNER_DOCUMENT_REVIEW_PENDING / D01_D10_UNCHANGED / Q1_DIRECTION_ADOPTED / Q2_Q7_OWNER_ADOPTED / Q8_OWNER_ADOPTED_CONDITIONAL / Q9_R1_COMPLETED / Q8_STATIC_R0_CLOSED_NOT_VERIFIED / OPTION_A_OWNER_ADOPTED_NOT_EXECUTED / Q10_OWNER_ADOPTED / Q8_FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED / AUTONOMOUS_GENERATION_HOLD / NO_IMPLEMENTATION_AUTHORITY / NO_AUTO_R2`。

> 本書はHuman Ownerが採択した**論理設計方針に基づくMVP設計書完成候補**であり、安全性証明、MVP完成宣言、Option A実施指示、実装・GitHub書き込み許可ではない。
