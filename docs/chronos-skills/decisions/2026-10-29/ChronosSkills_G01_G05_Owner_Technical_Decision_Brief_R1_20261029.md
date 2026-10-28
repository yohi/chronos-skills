# ChronosSkills — G-01〜G-05 技術判断・計画完成に向けたHuman Owner判断資料 R1

**基準日:** 2026-10-29（JST）  
**Repository:** `yohi/chronos-skills`  
**証拠固定HEAD:** `b778b20856613f9c50649347cad2628520ced5b4`  
**作業権限:** `DOCUMENT_ONLY / EXISTING_APPROVED_SOURCES / DECISION_PREPARATION_ONLY / NO_NEW_FEASIBILITY_RESEARCH / NO_HOST_VERIFICATION / NO_TEST / NO_SOURCE_MODIFICATION / NO_GITHUB_WRITE / NO_ARCHITECTURE_ADOPTION / NO_GATE_AUTHORITY`

> **目的:** Human Ownerが次に「何を、どの順で、何の証拠が揃えば判断するか」を選べるようにする。これは**判断資料**であり、技術方式の採択・調査実施承認・実行可能計画の完成・実装開始ではない。既存設計・計画文書のレビューをやり直さない。

## 1. 結論：最短の進行方針

1. **設計書R3.1と条件付き計画書R1の作成工程を再開しない。** R3.1は受入済みの論理設計、R1は作成済みだが**NOT_EXECUTABLE**の条件付き計画として扱う。
2. **G-01をクリティカルパスとして明示し、証拠所在の再探索をしない。** 以前のQ8静的検証でF-01/F-02/F-05は`NOT_VERIFIED`、Option Aは`OWNER_ACCEPTED_WITH_LIMITATIONS / CLOSED`。必要なら**別途認可した実質的に新しい、境界の明確な技術成立性判断**に限る。反復するR2/Option A探索は推奨しない。
3. **G-02〜G-05については、技術実験を始める前に、Ownerが決める責任主体・入出力・保証条件・受入証拠を一枚ずつ提示できる。** 既存論理設計からの整理だけなら実装検証は不要。ただし**具体的な方式・型・ストアを確定するには別途承認**が必要。
4. G-01〜G-05に必要な判断・証拠が揃い、言語・レイアウトが決まって初めて、I-01〜I-09を実在パス、API署名、RED/GREENコマンド、コミット単位まで具体化した**実行可能な実装計画**を作る。計画受入と実装開始は別判断。

**優先順位の根拠:** G-01が解消されない限りD-02の自律生成が成立したとは言えず、検索のみの機能をMVP完成と呼ぶこともできない。一方で、G-01未証明を理由にG-02〜G-05の「未決定点の文書整理」まで止める必要はない。ただし整理と採択を混同しない。

## 2. Gate別の判断パッケージ

| Gate / Finding | 未決定事項または未証明事項 | 既存設計で固定された要求 | Ownerが今後選ぶべき判断 | 採択・実装前に必要な根拠 | 未充足時 |
|---|---|---|---|---|---|
| **G-01 / CS-R1-001 (Blocker)** | 信頼できるRoot新規発行起点、Caller認証、MCP/Shell/Host等への完全仲介。Caller Bが実効CならCとして評価 | Root発行・既存利用・子委譲・Attempt消費を別権限とする。非認可時fail-closed | ①現在HOLD継続 ②将来の限定的な成立性検討を別承認 ③採択済み要件の変更提案を検討、のどれを選ぶか | 発行者真正性、経路別の拒否・迂回不能性、A/B/実効Cの強制分離、独立した証拠とOwner採択 | `OPEN / NOT_ESTABLISHED / HOLD` |
| **G-05 / CS-R1-005** | 信頼するProject/Task識別元と検索・Draft参照の強制主体 | 自己申告のprojectラベルだけで認可しない。越境時fail-closed | 誰が真正なProject/Taskを保証し、照会結果ごとの帰属を検査するか | 責任境界、信頼入力と拒否ケース、越境防御の成立確認 | `UNDECIDED / NOT_VERIFIED` |
| **G-04 / CS-R1-004** | Adapter/Core公開操作・認可入力・type shape・エラー・監査秘密境界 | `CONFLICT`/`UNDETERMINED`/認可拒否/予算不足を区別。Q2のMCP優先・STDIOは採択済み | 公開操作と内部操作、エラー伝播、情報秘匿、versionの契約をどう選ぶか | Owner採択済み契約、実際の認可責務との整合、型・シリアライズ仕様 | `UNDECIDED / NOT_IMPLEMENTED` |
| **G-03 / CS-R1-003** | 唯一の予算状態所有者、永続Store、原子的Attempt更新・冪等性・復旧 | Root全体のAttempt最大2、`STARTED`は開始許可と消費永続化、`UNCERTAIN`は枠保持、`RELEASED`は開始未許可の証明必須 | 原子化・耐障害境界・保存方式・状態遷移責任をどう確定するか | 競合・応答逸失・クラッシュ・再起動の反例と根拠、Ownerの方式採択 | `UNDECIDED / NOT_VERIFIED` |
| **G-02 / CS-R1-002** | OpenCode v2.1.4のSkill提供・発見・キャッシュ・失効の実際の境界 | 現在Taskだけで利用、次Task自動利用なし、Global/既存Skillsへ未承認混入なし | 提供者・保存先・発見経路・終了時失効の方式をどう選ぶか | 固定v2.1.4での提供/解除・終了通知逸失・次Taskの非利用の成立確認 | `UNDECIDED / NOT_VERIFIED` |

**注意:** 表の並びは**判断事項を説明する推奨順**であり、決定済みの技術依存順序や実施許可ではない。特にG-04の最終APIは、G-01とG-05の信頼境界・G-03の状態所有境界が明確になる前に固定しない。

## 3. 判断の順序：文書整理と成立性証明を分ける

### 段階D0 — 今回完了：判断材料の整理

- R3.1、R1、Owner決定履歴から未決定点を抽出。**方式は選択しない**。
- Gateを「未決定（UNDECIDED）」「未証明（NOT_VERIFIED）」「未承認（NOT_AUTHORIZED）」の異なる状態として管理。
- 現行のGateチェックリストは**0件のまま**。

### 段階D1 — 推奨する次のOwner判断（まだ承認されていない）

**最初に決めるべきことは、G-01を無期限に再調査するか否かではなく、次の作業の種類と終了条件である。**

| 選択肢 | 内容 | 得られる成果 | 制限・コスト | 推奨 |
|---|---|---|---|---|
| **A（推奨）** | G-01はHOLD維持のまま、既存資料からG-05→G-04→G-03→G-02の**方式未採択の判断項目・契約候補と依存関係**を1回限りDOCUMENT-ONLYで整理する | Ownerが選択可能な具体的判断点、次に必要な根拠、実行可能計画の入力欠損表 | 技術的成立は増えず、実装計画はまだNOT_EXECUTABLE | **推奨** |
| B | G-01について**新たな独立の技術成立性検討**を一回限り別途設計・承認する | 根拠に基づく可能・不可能・未証明の切分けが期待できる | 既存Q8/Option Aの再探索を禁止し、調査対象、反例、STOP条件の厳格な定義が必要 | 将来、明示承認後 |
| C | G-01を含め全技術判断を止め、現状の条件付き計画で凍結 | 追加コスト・リスクなし | 実行可能計画へは進まない | 許容 |
| D | D-02の自律生成等MVP要件を変更する議論を開く | 別スコープの選択肢 | 採択済み要件を変更するためOwnerの独立判断が必要 | 現時点非推奨 |

選択肢Aでも、**新たなAPI/DB/認可方式を確定しない**。単なる不足項目の再掲に終わらせず、各Gateごとに「候補が満たすべき契約と反例」「他Gateから必要な入力」「採択時のOwner確認事項」を明記し、**一度で終了**することを成功条件とする。

### 段階D2 — 技術方式の個別採択・成立性検証（将来、別承認）

- G-01：発行起点・Callerの実効能力・全経路仲介の成立性の証拠を扱う。このGateを**文書だけでPASSにしない**。
- G-05/G-03/G-02：選ばれた境界の真正性、Atomic性、OpenCode v2.1.4でのTask限定提供など必要な検証を別工程で扱う。
- G-04：決まった境界に基づくAPI・型・失敗契約を確定する。
- 不成立・証拠不足なら当該GateはHOLD/OPENのまま止め、無承認の別アーキテクチャ・Host検証へ飛ばない。

### 段階D3 — 実行可能計画化（条件が揃った後のみ）

- I-01〜I-09を実在するファイルパス、依存、関数/型署名、RED→GREEN、期待assert、実行コマンド、コミット単位に分解。
- 最終実装計画とR3.1設計の整合性を独立確認。計画受入は実装開始・GitHub書込の許可と別。

## 4. 依存関係：混同すべきでない2種類

**実行可能計画への必須Gate:** G-01〜G-05と開発環境・インターフェースが揃わなければ、`superpowers:writing-plans`形式の実行可能版は完成したと主張できない。

**判断資料作成上の依存:** G-05の信頼識別元とG-01のCaller/Root境界がG-04の認可入力へ反映される。G-03の予算所有境界とG-02のSkill提供主体も、G-04の実際の操作・エラー契約へ影響する。したがってG-04を最終固定する前に依存境界を確認する。G-01がHOLDでも、G-02〜G-05の契約上の未決定点の列挙は可能。

**実装スロットへの影響（固定R1計画に依拠）:** I-03/I-04/I-05/I-06はG-01の未証明により開始不可。I-01/I-02/I-07/I-08もそれぞれG-04/G-05などのGate・実装許可を必要とするため、今回実装可能になるわけではない。I-09は全Gateと全先行成果物が前提。

## 5. 実行可能計画の完成条件チェック（今回の達成数は増やさない）

| 要件 | 現在 | 次に必要な判断・根拠 |
|---|---|---|
| G-01 Trusted Root | OPEN/NOT_ESTABLISHED | 発行起点・完全仲介の実証、Owner採択 |
| G-02 Task限定ロード | OPEN/NOT_VERIFIED | 提供/失効方式の採択、v2.1.4成立根拠 |
| G-03 予算/復旧 | OPEN/NOT_VERIFIED | 状態所有・永続化・原子性の採択と検証 |
| G-04 API/型契約 | OPEN/UNDECIDED | 依存Gateと整合する公開契約の採択 |
| G-05 Project/Task認可 | OPEN/NOT_VERIFIED | 信頼元・強制点の採択と越境確認 |
| 開発基盤 | UNDECIDED | 言語、パッケージ構成、テスト環境、正確なパスの確定 |
| 実行可能版計画 | NOT_PREPARED | I-01〜I-09の実行単位・コマンド・assertの確定 |
| 計画受入と実装権限 | NOT_AUTHORIZED | 別々のOwner判断 |

**固定計画R1のGateチェックは0件。** 判断パッケージの作成はこれらを自動で進捗扱いしない。

## 6. 役割と境界

| 次工程 | 第一担当候補 | 役割 | 現在の権限 |
|---|---|---|---|
| D1判断項目と契約候補の文書化 | **このChatGPT** | 承認された場合のみ、既存資料から各Gateの選択肢を整理 | 未承認（この資料の作成のみ実施） |
| D1成果の採否・D2の要否判断 | **Human Owner** | 方向と権限境界を指定 | Owner判断権限 |
| D2で承認された限定証拠・技術調査 | Codex等 | 指定された1タスクのみ | 未承認 |
| D2で必要な独立評価 | 別セッションのChatGPT | 証拠と著者を分離して限定評価 | 未承認 |
| D3実行可能計画作成 | このChatGPT | Gate成立・採択後に具体的な計画化 | 未承認 |

無限ループ防止：D1は一回限りの判断パッケージで閉じる。D2で証拠が未成立ならOPENを記録してOwner判断に戻し、勝手にR2/R3調査を増やさない。既にCLOSEDのOption Aや証拠棚卸しは再開しない。

## 7. 維持状態とSTOP

```text
DESIGN_R3_1 = OWNER_ACCEPTED_LOGICAL_DESIGN
CONDITIONAL_PLAN_R1 = PREPARED_AND_SAVED / NOT_EXECUTABLE
G01_EVIDENCE_INVENTORY_AND_REMEDIATION = OWNER_ACCEPTED / CLOSED
OPTION_A = OWNER_ACCEPTED_WITH_LIMITATIONS / CLOSED
OPTION_A_INDEPENDENT_REVIEW = BLOCKED

G01_TECHNICAL_BLOCKER = OPEN
G02_G03_G04_G05 = OPEN
F01_F02_F05 = NOT_VERIFIED
FEASIBILITY_NOT_VERIFIED
ROOT_AUTHORITY_NOT_ESTABLISHED
AUTONOMOUS_GENERATION_HOLD
IMPLEMENTATION_NOT_AUTHORIZED
EXECUTABLE_PLAN_BLOCKED
NO_AUTO_R2
NO_NEW_GITHUB_WRITE_AUTHORIZED
```

**STOP:** この判断資料の作成後、方針採択、追加技術調査、Host/Runtime検証、コード/テスト/実装、Git/GitHub書込、Gate変更および別の独立レビューを自動開始しない。

## 8. 根拠

1. [固定HEAD: REQUIREMENTS.md](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/REQUIREMENTS.md) — Blob SHA-1 `490e4d38d022c5ba3c748131775035b0f33f7b21`。
2. [固定HEAD: R3.1 MVP設計書](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md) — Blob SHA-1 `ce3dccb99e607e74b608c636f4146a65c1671f34`。
3. [固定HEAD: R1条件付き実装計画書](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/docs/superpowers/plans/2026-10-28-chronos-skills-mvp-implementation-plan-R1-gated.md) — Blob SHA-1 `64e888009e46586a572dde98dc6a7e1ec127c701`。
4. [前工程の文書到達点確定記録](ChronosSkills_Design_And_Conditional_Plan_Status_Closure_R1_20261029.md) — GitHubとは別の作業成果物。Owner判断は本会話中の承認を根拠とし、固定文書内の古いステータスを現在の権限へ自動転用しない。

---

**本資料のステータス:** `OWNER_DECISION_BRIEF_PREPARED / TECHNICAL_DECISIONS_NOT_ADOPTED / NO_ACTION_AUTHORIZED`。
