# ChronosSkills G-01 — 次段技術成立性判断の範囲・STOP条件 R1（Human Owner判断用）

**基準日:** 2026-10-29（JST）  
**Repository / 固定HEAD:** `yohi/chronos-skills` / `b778b20856613f9c50649347cad2628520ced5b4`  
**OpenCode固定参照:** v2.1.4 / `68bc26b7da1990ce10ffecfbc1e09eb13aa5d824`（バージョン対応は既存資料の固定前提）  
**作業権限:** `DOCUMENT_ONLY / G01_SCOPE_AND_STOP_DEFINITION / EXISTING_SOURCES_ONLY / NO_NEW_FEASIBILITY_RESEARCH / NO_CODE_ANALYSIS / NO_HOST_VERIFICATION / NO_TEST / NO_BUILD / NO_POC / NO_IMPLEMENTATION / NO_GIT_OR_GITHUB_WRITE / NO_ARCHITECTURE_ADOPTION / NO_GATE_AUTHORITY`  
**本書の状態:** `SCOPE_DRAFT_PREPARED / FUTURE_TECHNICAL_REVIEW_NOT_AUTHORIZED / HUMAN_OWNER_DECISION_REQUIRED`

> **最終ゴール:** ChronosSkillsの採択済みMVP設計を保ち、将来の実行可能な実装計画へ進めるための、**G-01だけ**に関する判断範囲を確定する。技術的に成立したと証明したり、Q8・Option Aの調査を再実行したりする資料ではない。

## 1. 既に決まっていること・再開しないこと

- 設計書R3.1はHuman Ownerが**論理設計文書として受入済み**。D-01〜D-10、とくにD-02の新規一時Skill自律生成を維持する。Q8は`OWNER_ADOPTED_CONDITIONAL / FEASIBILITY_NOT_VERIFIED`、Q10はCaller A+B（実効C相当ならC）の評価条件を採択済み。
- 条件付き実装計画R1は`NOT_EXECUTABLE`。G-01〜G-05は実装前判断であり、G-01はBlockerとして`OPEN`。
- F-01（Root新規発行）、F-02（Caller真正性）、F-05（MCP以外の経路・実効能力）はすべて`NOT_VERIFIED`。Q8限定静的検証R0は完了／STOP。既存コードの一般Session操作と信頼済みRoot発行を同一視しない。
- G-01棚卸し・文書是正は`CLOSED`。Option A所在確認は`OWNER_ACCEPTED_WITH_LIMITATIONS / CLOSED`、独立レビューの入力不足`BLOCKED`は履歴として保持する。**どちらも再実行しない**。
- G-02〜G-05の契約候補R1は`DOCUMENT_ONLY`で完成しているが、方式・API・Store・実装言語は未採択。G-01の成立を示す証拠は増えていない。

## 2. 今回確定するのは「次段の検討条件」のみ

**問い（単一の判断対象）:** 固定OpenCode v2.1.4を初期対象とし、採択済みQ8/Q10と四権限分離の下で、**明示された1つの具体的なRoot発行境界候補**について、非認可Callerからの新規Root発行・再発行・予算再取得が強制的に防止できると説明する根拠があるか。

**成果として必要な区別:**

1. **設計契約の定義** — どのActorがRoot発行を許可し、どのCallerが使うのか。まだ未採択の候補に過ぎない。
2. **静的成立根拠** — 実際の固定ソース・構成に支えられた入口／資格・認証境界／拒否点。既存資料への参照と新規証拠を区別する。
3. **成立未証明** — Host実効権限・構成情報が欠ける場合は`NOT_VERIFIED`とし、構成を想像して補わない。
4. **個別候補への反例** — 特定の発行境界で迂回が成立するなら、その候補に限定して不成立を記録。全方式の普遍的不可能性に拡張しない。

**重要:** 本書は検討する境界の要件を示すだけで、具体的なTrusted Issuerや新しい認可方式を設計・採択しない。「候補が未指定」のまま大規模ソース探索を開始してはならない。

## 3. 将来の1回限りの技術検討に先立つ入力ゲート（P0）

以下は**将来の実行承認時にHuman Ownerが指定するか、既存の資料から明示的に特定できる必要がある**。現時点では充足を認定しない。

| ID | 必須入力 | 不足時の扱い |
|---|---|---|
| P0-01 | **単一候補の指定**：Root発行Actor／非認可Callerとの差／信頼の起点／Rootを新規発行する入口を説明した候補境界 | `CANDIDATE_NOT_DEFINED / STOP`。新方式を勝手に創作・列挙しない |
| P0-02 | 固定範囲：Opencode v2.1.4の参照Commit、ChronosSkills固定HEAD、既存のQ8静的検証で確認済み経路との対応 | 固定対象が揺れるなら`SOURCE_SCOPE_NOT_FIXED / STOP` |
| P0-03 | Q10 A/B/実効Cの評価対象、Host/OS/プロセス/資格情報の境界について利用可能な実在資料の列挙 | C除外やBの制限を仮定できない。`CALLER_BOUNDARY_NOT_ESTABLISHED / STOP` |
| P0-04 | 権限別に「Root新規発行」「既存Context利用」「子委譲」「Attempt消費」を明示的に別扱いした契約 | 混同する候補は`AUTHORITY_BOUNDARY_AMBIGUOUS / STOP` |
| P0-05 | 過去R0の何を**繰り返さず**、何の新しい具体的差分／判断可能性だけを確認するか | 新規性を定義できなければ`DUPLICATE_Q8_RESEARCH / STOP` |

P0が成立しない場合の推奨は、**検討を始めずにOwnerへ返す**こと。追加資料の取得、Host確認、新しい別候補へのピボットを自動で認可しない。

## 4. 将来の限定検討での合否を判別する問い（実行未承認）

| 観点 | 反証可能なチェック | 不足・不適合時の結果 |
|---|---|---|
| G01-1 Trusted Issuer | Caller入力のSession ID/Metadata/MCPヘッダー等に依存せず、新規Root発行に固有の信頼元を指名・検証可能か | `ROOT_AUTHORITY_NOT_ESTABLISHED` |
| G01-2 完全仲介 | 対象候補についてRoot発行に至るすべての**実効的に到達可能な**API・Plugin・MCP・Shell・IPC等の入口と拒否点を限定し、検査できるか | 未列挙の経路は`NOT_VERIFIED`、示された迂回は**候補限定の反例** |
| G01-3 Caller実効能力 | A/Bで強制的に制限できる根拠、Bが実効CならCとして扱う根拠、Cを除外する実効分離証拠があるか | `CALLER_SCOPE_NOT_VERIFIED`。Shellの存在だけでB=Cと断定しない |
| G01-4 同一作業の再発行 | Create/Import/Fork/再接続/子Agentの経路で無権限のRoot増設・予算再取得ができないことを説明できるか | `REISSUE_PREVENTION_NOT_VERIFIED` |
| G01-5 四権限分離 | 既存Context使用・子委譲・Attempt予約の資格でRoot新規発行が許されないか | `AUTHORITY_SEPARATION_NOT_VERIFIED` |
| G01-6 fail-closed | 未認証、権限不明、必要情報欠落、構成の不確定時に**自律Skill生成だけ**を拒否し、元の開発タスクを不必要に停止しないか | `FAIL_CLOSED_NOT_ESTABLISHED` |

対象の根拠は、根拠元のCommit/パス/該当行または構成資料識別子、観測されたこと、推論の前提、未観測事項を区別して記録する。単なる概念図や一般Session機構の説明で`PASS`にしない。

## 5. 将来の作業権限の上限（未承認）

**推奨候補:** `ONE_BOUNDED_G01_CANDIDATE_STATIC_ASSESSMENT`（1候補・1回・固定版）。実行する場合は、その時点でHuman Ownerが**具体的な入力、参照可能なソース範囲、評価担当、取得方法、STOP条件**を明示した別承認を出す。

以下は**本書の作成では許可しないし、将来の静的作業にも自動付与しない**：

- Option Aの再実行、Q8-R0/Q9-R1の単純ななぞり直し、別候補の無制限探索。
- 新しい発行基盤・外部認可サービス・OS境界の設計採択。
- Host/Runtime実験、プロセス起動、PoC、テスト、ビルド、導入検証。
- ソースコード・ワークツリー変更、Git/GitHub書込、CI、実装。
- G-01のGate PASS認定、HOLD解除、G-02〜G-05への自動遷移。

静的検討だけでHost実効能力を確定できない場合はそのままSTOPし、**Host検証を自動的な次の工程にしない**。

## 6. 判定とSTOP条件

将来の1候補評価を承認する場合、その成果は次のいずれか1つに限定する。

| 成果分類 | 意味 | 次段への自動効力 |
|---|---|---|
| `CANDIDATE_STATICALLY_SUPPORTED_BUT_NOT_RUNTIME_PROVEN` | 指定候補の一部または全静的条件に根拠があるが、Runtime/Host保証ではない | なし。G-01を自動解除しない |
| `CANDIDATE_COUNTEREXAMPLE` | 特定候補の明示前提に対する反例を根拠付きで確認 | なし。別候補探索は自動開始しない |
| `CANDIDATE_NOT_VERIFIED` | 入力・経路・Host権限・資格情報・構成のどれかが未証明 | なし。現行HOLDを維持 |
| `INPUT_BLOCKED` | P0不足・資料取得不能・範囲不整合 | なし。既存資料探索を繰り返さない |

**即時STOP:** P0不足、根拠を欠くActor信頼性、A/B/実効Cの不明な到達性、全入口検査が未成立、既存静的調査の反復に陥る、候補スコープを増やす必要がある、Host/PoC/実装/書込の必要が生じる、必要資料がアクセス不能、評価の前提を採択済み要件から変える場合。

**工程完了条件:** 上記の分類・根拠・残存不足・Ownerに必要な判断を一度だけ提出しSTOP。**判定が`NOT_VERIFIED`でも工程は終了可能**。追加R2/R3研究を自動提案・開始しない。

## 7. 次のHuman Owner判断（今回まだ採択していない）

- **選択肢1 — HOLD継続:** 新規の技術成立性評価は開始せず、G-01のまま保留する。
- **選択肢2 — 推奨（条件付き）:** 上記P0を満たす**特定の1候補が示された場合に限り**、別途Human Ownerが静的評価の1回限りの実施範囲を承認する。候補の特定そのものに新規設計が必要なら、それも別のOwner判断に戻す。
- **選択肢3 — スコープ変更の議論:** D-02等の変更を議題にする。ただし要件変更は自動採択せず、現行のD-01〜D-10を維持したままOwner判断を待つ。

**推奨方針:** まず`P0`が成立するかを既存資料の範囲で**次回のOwner判断時に確認する**。特定候補がないなら`CANDIDATE_NOT_DEFINED`でSTOPし、抽象的な「さらなるQ8調査」の依頼を出さない。G-02〜G-05については、文書契約候補R1をそのまま判断材料として保持する。

## 8. 現時点で維持する状態

```text
G01_SCOPE_DEFINITION = PREPARED / HUMAN_OWNER_REVIEW_PENDING
G01_NEW_TECHNICAL_ASSESSMENT = NOT_AUTHORIZED
G01_TECHNICAL_BLOCKER = OPEN
F01_F02_F05 = NOT_VERIFIED
G02_G03_G04_G05 = OPEN / UNDECIDED
OPTION_A = OWNER_ACCEPTED_WITH_LIMITATIONS / CLOSED
OPTION_A_INDEPENDENT_REVIEW = BLOCKED
FEASIBILITY_NOT_VERIFIED
ROOT_AUTHORITY_NOT_ESTABLISHED
AUTONOMOUS_GENERATION_HOLD
IMPLEMENTATION_NOT_AUTHORIZED
EXECUTABLE_PLAN_BLOCKED
NO_AUTO_R2
NO_NEW_GITHUB_WRITE_AUTHORIZED
```

## 9. 根拠（本書の作成に使用した既存資料）

1. `REQUIREMENTS.md`、固定HEAD Git blob `490e4d38d022c5ba3c748131775035b0f33f7b21`。
2. `docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md`、固定HEAD Git blob `ce3dccb99e607e74b608c636f4146a65c1671f34`。特に§4.1.1、§7、§11。
3. `docs/superpowers/plans/2026-10-28-chronos-skills-mvp-implementation-plan-R1-gated.md`、固定HEAD Git blob `64e888009e46586a572dde98dc6a7e1ec127c701`。特に§1、§2.1。
4. `ChronosSkills_Q8_Bounded_Static_Verification_Report_R0.md`（既存ローカルコピー）。全F項目`NOT_VERIFIED`と過去検討の停止記録を確認。
5. `ChronosSkills_G01_G05_Owner_Technical_Decision_Brief_R1_20261029.md`（前工程判断資料）。
6. `ChronosSkills_G02_G05_Contract_Candidates_Owner_Decisions_R1_20261029.md`（前工程DOCUMENT-ONLY成果物）。

---

**この資料の作成で完了すること:** G-01の次段検討の範囲、前提入力、判定規則、STOP条件を文書として提示。  
**この資料の作成では実施しないこと:** 候補の技術分析、方式採択、独立レビュー、Codex実行、Host/PoC/テスト/ビルド、実装、Git/GitHub変更、Gate変更。

`DOCUMENT_SCOPE_DEFINITION_COMPLETE / FUTURE_TECHNICAL_WORK_NOT_AUTHORIZED / STOP`
