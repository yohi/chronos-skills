# ChronosSkills G-01 — 単一Trusted Root発行境界候補 定義案 R1

**基準日:** 2026-10-29（JST）  
**Repository / 固定HEAD:** `yohi/chronos-skills` / `b778b20856613f9c50649347cad2628520ced5b4`  
**OpenCode固定参照:** v2.1.4 / `68bc26b7da1990ce10ffecfbc1e09eb13aa5d824`（バージョン対応は既存文書の前提）  
**今回の権限:** `DOCUMENT_ONLY / ONE_G01_CANDIDATE_DEFINITION_PROPOSAL / EXISTING_SOURCES_ONLY / NO_NEW_FEASIBILITY_RESEARCH / NO_SOURCE_CODE_ANALYSIS / NO_HOST_VERIFICATION / NO_TEST / NO_BUILD / NO_POC / NO_IMPLEMENTATION / NO_GIT_OR_GITHUB_WRITE / NO_ARCHITECTURE_ADOPTION / NO_GATE_AUTHORITY`  
**文書状態:** `ONE_CANDIDATE_PROPOSED / NOT_ADOPTED / P0_NOT_SATISFIED / TECHNICAL_ASSESSMENT_NOT_AUTHORIZED`  
**根拠となる承認:** Human OwnerはG-01検討スコープR1を採択し、続いて既存資料に基づく**候補1件の定義案作成**を依頼。**候補の方式採択や実証は承認していない。**

> **結論（最初に読む）:** 採択済みQ8「信頼済み起点＋Core管理Context」を変えない範囲で、1件の条件付き候補 `G01-CAND-01` を提示する。しかし、**信頼できるHost側起動Actor・その入口・Agent側からの強制分離が既存資料で実在・成立すると確認できない**。従って、技術的な静的成立性評価へ進むためのP0は未充足。**候補の存在を発見した／実現可能性を確認した／技術評価を開始できるとは宣言しない。**

## 1. 既存の確定事項と候補が変更できない契約

- MVPは新規Skillの**自律生成**を含む（D-02）。検索専用へ縮退しない。既存Skillの自動改変はしない（D-10）。
- Q8は**条件付きの設計方針**：信頼済みRoot発行起点とCore管理Root Task Context。成立は`FEASIBILITY_NOT_VERIFIED`。
- Q10防御対象はCaller A（MCP-only）とB（Agent tools/Shell）。Bの**実効能力がC（Host同等任意コード）ならC**として評価。Cを除外するには実効的な強制分離証拠が必要。
- 四権限は別：①Root Context新規発行、②既存Context利用、③子Agentへの委譲、④Attempt予約・予算消費。Session ID、Metadata、Session生成イベント、単なるServer認証、MCP接続は①の資格ではない。
- G-01 `CS-R1-001` は`OPEN`、F-01/F-02/F-05は`NOT_VERIFIED`、自律生成HOLD。Option Aは制約付きCLOSEDで再開しない。
- 今回は**契約の候補を1件だけ定義**。受信API・OS構成・秘密管理方式などの仕様を実装済みとして記述しない。

## 2. 唯一の候補：G01-CAND-01

**仮称:** `HOST_TRUSTED_TASK_BOOTSTRAP_TO_CORE_ISSUER`  
**分類:** `UNADOPTED_DESIGN_HYPOTHESIS`（既存Q8の具体化仮説。別方式の正式採択ではない）

### 2.1 Actor、信頼起点、発行入口（仮説と事実を分離）

| 項目 | この候補の定義・要求 | 現在の証拠状態 |
|---|---|---|
| **発行を許可するActor** | OpenCode Host側で正規Task開始を識別し、Agentの自己申告と独立して起動する**信頼済みTask bootstrap主体**（候補上の役割） | そのような専用Actorの**実在・権限・実装は未特定**。Host全体や任意Pluginを無条件に信頼しない |
| **非循環の信頼起点** | bootstrap主体の真正性は、Agentが自由に作るSession/Metadata/ヘッダー/Tool入力によらず、Host/OSの強制境界に基づいて確立されること | **未証明**。どのOS主体・構成・信頼資料に基づくか未提示 |
| **Rootを新規発行する入口** | Coreの**発行専用の内部境界**を通る論理操作（ここでは`ISSUE_ROOT`と仮称）。一般MCP/HTTP/CLIやSessionイベントは直接この発行を許可できない | **実在する入口・関数・非公開性・呼出経路は未提示**。`ISSUE_ROOT`はAPI名・型・実装の採択ではない |
| **発行時の確認** | bootstrap主体の真正性、正規Task開始、同一作業のRoot再発行でないこと、Project/Task帰属を検証し、Coreが許可した場合にのみRoot Contextを発行 | 検証主体・入力・状態所有者・同一作業を識別する根拠は未決定 |
| **保護資産** | 新規Root発行権限、Rootと正規Taskの結合、Root共有予算を増枠できない不変条件 | 認可効果の実装なし。方式の成立証拠なし |
| **禁止する代替経路** | `session.create` / Import / Fork / Metadata / Plugin Session API / MCP / Shell / 公開HTTPだけでRoot新規発行できない | OpenCodeには**一般Sessionに関する**複数入口があるとR0に記録。Rootとの呼出閉包は**未証明** |

**境界図（構成済み実装を意味しない）**

```text
   [Host-side authorized Task bootstrap: TRUSTED?]
                      |  agent-self-assertion でない発行資格が必要
                      v
                [Core ISSUE_ROOT?]  --- Rootの新規発行に専用認可
                      |
                      v
              [Core-managed Root Context]
                    |           |           |
             既存Context利用   子への限定委譲   Attempt予約・消費
                  （各々別認可。Root新規発行資格への昇格は禁止）

   非認可Caller A (MCP) --------X----> ISSUE_ROOT
   非認可Caller B (Shell等) ----X----> ISSUE_ROOT
   実効C（Host同等） ----------?----> 保護境界：未証明ならC除外不可
```

**最も重大な未確定事項:** OpenCode Host内にAgentから真に分離されたbootstrap主体が存在するか、Agent Tool/Shellがその発行経路または資格情報に到達できないかは未証明。単なる「Host内部API」「非公開関数」「Plugin専用」という名称だけでは強制分離にならない。実効Cが同一権限で発行素材・内部APIを操作できるなら、この候補は防御要求を満たさない。

### 2.2 入口・Caller別の拒否契約（すべて**要求**、実証ではない）

| Caller / 経路 | 受信側の要求動作 | 未解決の成立条件 |
|---|---|---|
| A：一般MCP呼出 | 発行資格なしなら`ISSUE_ROOT`を拒否。MCP Session ID／`_meta`で昇格しない | 発行機能の非公開性・別入口を含む仲介、Coreでの資格検証 |
| B：Shell/一般Tool | HTTP/IPC/ファイル/Module/Plugin経由で発行資格を得られない。一般Server認証をRoot発行認可に代用しない | 実効OS主体・権限・資格情報共有・到達経路の固定証拠 |
| 実効C | C相当の到達能力があればCとして評価。対象外にするなら別の実効強制境界が必要 | Host権限を持つ主体とRoot発行素材の分離根拠。不明なら対象外としない |
| 正規bootstrap Actor | そのTaskのRootが未発行の場合だけ認可し、発行後は同一作業の再発行/枠再取得を拒否 | 真正なTask識別・再接続・Import/Forkの意味、authoritative stateの根拠 |

### 2.3 四権限の分離（候補の要件）

1. **Root新規発行:** Host側の独立した信頼起点からの専用操作。既存Contextを所持していても行使不可。
2. **既存Context利用:** Caller/Task/Projectの結び付きと許可範囲の照合が必要。新Root発行不可。
3. **子Agent委譲:** 親Rootの期限・Project・上限内でのみ許可。子が独立Rootを増設しない。
4. **Attempt予約・消費:** Coreが状態を管理する論理契約。`STARTED`の永続開始許可、`UNCERTAIN`の枠保持、最大2回の共有上限は既存Q4〜Q7のまま。Root発行Actor権限とは別。

上記を具体的に実現する型・認可材料・保存方式を**本案で確定しない**（G-03/G-04/G-05もOPENのまま）。

## 3. 既存証拠から言えること／言えないこと

| 証拠 | 既存資料が示す範囲 | **示さない**こと |
|---|---|---|
| R3.1設計書 §4.1.1、§7 | 採択済みQ8/Q10、四権限とHOLD契約 | 実在するTrusted bootstrap Actor、強制仲介の証明 |
| Q8限定静的検証報告R0 §3〜6 | 一般Session Create/Import/Fork/Plugin、MCP、Shell等の入口の一部、CE-01/02と不足E-01〜04 | ChronosSkills Root発行の実装された経路閉包、OS/Host分離、F項目PASS |
| G-01検討スコープR1 §3〜6 | P0-01〜05、限定評価条件、STOP判定 | 当該P0の実際の充足・評価の許可 |
| 条件付き実装計画R1 §1 | G-01〜05がOPENで、Root発行がブロックされること | 実行可能なAPI/テストコマンド・実装権限 |

**追加探索・ソースコード再解析・Host検証は今回行っていない。** 今回のActor／入口は既存コードで見つかった実体ではなく、既存Q8を満たすための**仮説上の境界**である。

## 4. P0の充足判定（本案作成者の文書上の評価。正式Gate判定ではない）

| P0 | 本案による状況 | 次段への扱い |
|---|---|---|
| P0-01：単一候補・Actor/入口/信頼起点 | **候補1件は論理的に指定**したが、信頼Actorの実体と入口は未特定 | **NOT_SATISFIED**（具体的な固定Actor/入口を証拠で指定できない） |
| P0-02：固定Source lock | 固定コミットと既存R0検討範囲を特定済み | **DOCUMENT_SATISFIED**。タグ対応や最新環境の新規実証はしていない |
| P0-03：A/B/実効C、Host/OS/資格の実在資料 | Caller分類は定義済み。実在する分離構成・権限表は未提示 | **NOT_SATISFIED** |
| P0-04：四権限の分離 | 既存採択契約に従って記載 | **DOCUMENT_SATISFIED**（実装での成立ではない） |
| P0-05：R0に対する新しい差分 | 今回は候補のActor・発行入口・拒否要件を明文化。ただしR0の未証明事項に**新しい証拠はない** | **ASSESSMENT_DIFFERENCE_NOT_ESTABLISHED**。R0の再調査ならSTOP |

**P0結論:** `P0_NOT_SATISFIED / ONE_BOUNDED_G01_CANDIDATE_STATIC_ASSESSMENT_NOT_READY`。現時点で別途の技術評価承認を求める実行可能な入力パッケージは成立していない。単一候補の仮説文書を作れたことは、P0ゲートPASSや静的実現可能性を意味しない。

## 5. Owner判断に返す論点（現時点では方式採択を求めない）

**提案する取扱い:** 本候補を`UNADOPTED_CANDIDATE_DOCUMENTED`として保存し、**技術調査へ自動移行せずSTOP**する。理由は単一候補の名称は付いたが、P0-01/03/05を既存証拠で満たせず、同じQ8-R0調査をもう一度行っても現時点の欠落が埋まる根拠がないため。

後続に判断が必要なら、次の事実を区別してHuman Ownerへ返す。

- **要求との整合:** Q8（信頼済み起点＋Core Context）とQ10、四権限分離を変更しないという設計上の位置づけ。
- **証明がない:** 正規Task開始の権威あるHost Actor、Root発行入口の実在、B/C到達不能性、非循環の信頼起点、全入口の完全仲介。
- **新たな方式の選択:** Host/OS分離、新しい専用プロセスや認可サービスを必要とする場合は**新規設計判断**となり、本案から自動でMVP必須機構に追加しない。
- **進め方:** 実在するActor/境界を指定できなければ`CANDIDATE_NOT_CONCRETE_ENOUGH / STOP`。その場合は追加の広範調査を自動指示しない。D-02の要件変更も自動採択しない。

## 6. 変更禁止・HOLD・STOP

```text
G01_SCOPE_R1 = OWNER_ADOPTED
G01_CANDIDATE = G01-CAND-01 / PROPOSED_NOT_ADOPTED
G01_CANDIDATE_P0 = NOT_SATISFIED
G01_TECHNICAL_ASSESSMENT = NOT_AUTHORIZED
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

**今後の個別権限なしに実施しない:** 候補の技術評価、OpenCodeソース追加追跡、Host/Runtime/OS実行、PoC・テスト・ビルド、コード・設定・GitHub書込、Gate変更、実装計画の実行可能化、G-02〜G-05への自動移行、Option A再実行、別候補探索。

**本資料の完了状態:** `DOCUMENT_CANDIDATE_DEFINITION_COMPLETE / SINGLE_CANDIDATE_ONLY / P0_NOT_SATISFIED / STOP`。

## 7. 参照資料（今回読んだ既存証拠）

1. [固定HEAD 要件](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/REQUIREMENTS.md) — Blob `490e4d38d022c5ba3c748131775035b0f33f7b21`。
2. [固定HEAD 設計書R3.1](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md) — Blob `ce3dccb99e607e74b608c636f4146a65c1671f34`。
3. [固定HEAD 条件付き計画R1](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/docs/superpowers/plans/2026-10-28-chronos-skills-mvp-implementation-plan-R1-gated.md) — Blob `64e888009e46586a572dde98dc6a7e1ec127c701`。
4. `ChronosSkills_Q8_Bounded_Static_Verification_Report_R0.md` — 既存ローカル資料。特に§3〜6（過去の報告内容であり新たな独立検証ではない）。
5. `ChronosSkills_G01_Bounded_Technical_Feasibility_Scope_R1_20261029.md` — Human Owner採択済み検討スコープ。
6. `ChronosSkills_G01_G05_Owner_Technical_Decision_Brief_R1_20261029.md` — Owner判断資料。

