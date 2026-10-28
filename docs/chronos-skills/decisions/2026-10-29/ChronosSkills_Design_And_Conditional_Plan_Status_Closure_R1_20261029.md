# ChronosSkills — 設計書・条件付き実装計画の到達点確定記録 R1

**基準日:** 2026-10-29（JST）  
**プロジェクト:** `yohi/chronos-skills`  
**対象固定HEAD:** `b778b20856613f9c50649347cad2628520ced5b4`  
**決定:** Human Ownerが選択肢A「文書の到達点を正式に確定する」を本対話で承認  
**権限:** `DOCUMENT_STATUS_RECONCILIATION_ONLY / EXISTING_EVIDENCE_ONLY / NO_NEW_FEASIBILITY_RESEARCH / NO_TEST / NO_HOST_VERIFICATION / NO_SOURCE_MODIFICATION / NO_GITHUB_WRITE / NO_ARCHITECTURE_ADOPTION / NO_GATE_AUTHORITY`

> **本記録の効力:** 既存の設計文書と条件付き計画の「どこまで完成し、何が未完成か」を整理・確定する。新しい設計方式の採択、実行可能な計画の承認、技術的成立認定、実装開始、GitHub更新は行わない。本記録はChatGPT作業領域内の独立成果物であり、GitHubの固定文書を変更しない。

## 1. 結論（文書完成と技術成立を分離）

| 成果物・工程 | 到達点 | 成立しない主張 |
|---|---|---|
| 要件 `REQUIREMENTS.md` | D-01〜D-10のHuman Owner採択要件を維持。FR-01〜12、NFR-01〜07、AC-01〜11を基準とする | MVPの機能実装・受入達成を意味しない |
| **MVP設計書R3.1** | **Human Owner受入済みの論理設計文書**。MVP範囲、責務、状態遷移、認可要件、要件対応、未解決Gateを明示 | 技術的実現可能性やHost実証、G-01〜05の解消を意味しない |
| **条件付き実装計画R1** | **作成済み・GitHub保存済みの条件付き計画**。Gate G-01〜05、I-01〜09追跡スロット、実装禁止条件を明記 | **実行可能な詳細実装計画ではない**。実装権限もない |
| G-01既存証拠棚卸し・文書是正 | Human Owner受入で`CLOSED`。限定文書是正の独立R3レビューは`PASS` | F-01/F-02/F-05の技術証明ではない |
| Option A証拠所在確認 | `OWNER_ACCEPTED_WITH_LIMITATIONS / CLOSED`（工程上の制約付き終了） | 独立レビュー`BLOCKED`の解消や原本バイト同一性の証明ではない |

**文書としての到達点:** 論理設計書と条件付き計画の文書作成・到達点整理はここで区切る。  
**未完成の成果物:** `superpowers:writing-plans`相当の、実行可能な最終版実装計画。  
**技術・実装:** G-01〜05の必要判断と成立根拠は未充足。自律Skill生成・実装はHOLD。

## 2. 本来のゴールと維持する範囲

ChronosSkillsの目的は、OpenCode v2の通常開発中に既存Skill／SkillPort／ChronosGraph等を先に活用し、不足と再利用価値が認められたときに限り、**新規のプロジェクト内一時Skillを自律生成・静的検証・元のTask内で利用・軽量評価**し、正式採用と配布はHuman Ownerの別承認に委ねること。

- 採択済みD-01〜D-10を維持する。**D-02の自律的な新規Skill生成を検索・提案だけに置き換えない**。
- Q1はAgentの意味判断と決定論的Coreの責務分離。Q2は`MCP_PRIMARY / STDIO / CLI_MINIMAL / SHARED_CORE / HTTP_DEFERRED`。Q3は保守的な生成判定。
- Q4〜Q7はRoot Task共有予算、最大2 Attempt、原子的な開始許可・消費、冪等性を論理方針として維持。`STARTED`は実際のLLM呼出し観測ではなく、開始許可と消費の永続確定を指す。
- Q8は信頼済みRoot発行起点とCore管理Contextを**条件付き設計採択**。実効的な成立は未証明。
- Q10はCaller A（MCP-only）およびB（通常Agent Tool）を基準にし、実効Cに達する能力はCとして評価。C除外には強制分離証拠が必要。
- 未承認Skillは元Project・元Taskの範囲を越えて自動利用・自動配布しない。既存Skillを自動改変しない。
- Skill生成の失敗や不明な認可は生成側fail-closedとし、元の通常開発タスクまで自動的に失敗扱いにしない。

## 3. 未解決Gateの正確な種類

| Gate / Finding | 未解決事項 | 既存文書で確定していること | 実行可能計画への移行に不足するもの | 現在状態 |
|---|---|---|---|---|
| **G-01 / CS-R1-001 (Blocker)** | Trusted Root新規発行の認可、Caller真正性、A/B/実効Cの完全仲介 | 四つの権限分離、無権限発行拒否、未証明時HOLD | 信頼できる発行元と防御境界の成立根拠、Owner判断 | `OPEN / NOT_ESTABLISHED / NOT_VERIFIED` |
| **G-02 / CS-R1-002** | OpenCode v2.1.4の一時Skillロード・失効・非持越し | 元Task限定、Global混入禁止、次Task自動利用禁止 | 責任主体と方式の選択、v2.1.4における成立確認 | `OPEN / UNDECIDED / NOT_VERIFIED` |
| **G-03 / CS-R1-003** | 共有予算の状態所有者、永続化、原子性、クラッシュ復旧 | 最大2、`STARTED/UNCERTAIN/RELEASED`の不変条件、再送冪等性 | 唯一の状態所有境界、永続化・排他・復元方式、成立確認 | `OPEN / UNDECIDED / NOT_VERIFIED` |
| **G-04 / CS-R1-004** | Adapter/Coreの公開操作、型、エラー、監査境界 | 認可失敗・予算不足・`CONFLICT`・`UNDETERMINED`を混同しない論理契約 | 具体的API・type shape・シリアライズ・責任分界のOwner採択 | `OPEN / UNDECIDED / NOT_IMPLEMENTED` |
| **G-05 / CS-R1-005** | Project/Taskの信頼元と参照アクセスの強制点 | Projectラベルは認可証拠ではない。越境時fail-closed | 検証主体・結果帰属・Draft参照権限と有効な強制点、成立確認 | `OPEN / UNDECIDED / NOT_VERIFIED` |

区別すべき3概念：`UNDECIDED`＝具体方式や責任者が未採択、`NOT_VERIFIED`＝成立証拠が不足、`NOT_AUTHORIZED`＝調査や操作を実施する権限がない。いずれも「文書が未完成だから自動調査する」という意味にはならない。

## 4. 実行可能な実装計画が未完成である理由

固定R1計画のI-01〜I-09は**要件追跡のための条件付きスロット**であり、実施用Taskではない。後続の実行可能版には最低限、各Taskについて以下の確定情報を必要とする。

1. 実装言語・依存関係・パッケージ構成・実在する正確なCreate/Modify/Testパス。
2. Adapter/Coreおよび認可・予算・Skill提供の確定したAPI、型・署名、入出力と失敗条件。
3. Gate G-01〜05を満たす採択済み技術方式と、必要な技術的成立根拠。
4. RED/GREENテスト名・assert・実行コマンド・期待結果、回帰範囲、依存順序・コミット単位。
5. 設計書との独立した整合性確認、および計画受入・実装着手の**それぞれ別のHuman Owner承認**。

現行の固定R1計画では判断チェックリストは**0件**、計画状態は`NOT_EXECUTABLE`。この記録によりチェック数を増やさない。実装言語、ストア（SQLite等）、新しい認可サービスは今回決定しない。

## 5. G-01棚卸しおよびOption Aの終了記録

- **G-01棚卸し・限定文書是正:** 前回独立レビューR2でMajor 4件とMinor 1件は`RESOLVED`、R3限定レビューで残りの`G01-INV-R1-006`と`R2-OBS-001/002`が`RESOLVED`。Human Ownerが文書工程を`CLOSED`として受入済み。これは文書品質の判断である。
- **Option A:** Codexによる既存静的証拠の所在確認は`COMPLETED_REPORTED`。後続の独立レビューは主要報告書の原本バイト列未取得等で`BLOCKED`のまま。Human Ownerは再試行ループを打ち切り、**`OWNER_ACCEPTED_WITH_LIMITATIONS / CLOSED`**と判断済み。
- **制約として残すもの:** Option A報告書の13,549 bytesおよびSHA-256 `05ad9c9e4b96aa9fa9509f06a1304ca5aaf60b1c13da1af797795da7dc95df9d`は**Codex申告値で独立未検証**。Codex探索経路・網羅性、未追跡ファイルの存在と由来、報告書のバイト同一性も独立未検証。
- **歴史的な文書記載:** 固定R3.1/R1の`Option A = NOT_EXECUTED`、R3.1のOwner再受入待ち表示は、それぞれ文書作成時点のスナップショット。後続のHuman Owner判断を反映した**現在の工程管理状態**はこの記録と会話の決定履歴で区別する。固定文書そのものを無断で書き換えない。

## 6. 次の判断対象（本記録では承認・開始しない）

- **次の目的:** 実行可能な最終実装計画へ進むために、G-01〜05の個別方式・証拠取得の必要性・順序をHuman Ownerが判断する。G-01のBlockerを解決せず自律生成実装へ進まない。
- **判断と実行を分離:** 方針整理を承認しても、新規技術調査、OpenCodeコード解析、Host実験、方式採択、実装、Git/GitHub書込の権限は自動付与されない。
- **文書レビュー反復防止:** 既に終了したG-01棚卸し、Option A、R3.1/R1の到達点を、原本取得が難しいという理由だけで際限なく再開しない。新規の実質的な根拠と別のOwner承認がある場合のみ新工程を定義する。
- **スコープ維持:** D-02の自律生成を勝手に除外したり、ChronosGate/ChronosGraph等の全面改修や新規サービスを無承認でMVPに追加したりしない。

**今回の作業の終了条件:** この1文書の作成・提示により`DOCUMENT_STATUS_RECONCILIATION_COMPLETE`。追加の独立レビューやCodex工程は自動開始しない。

## 7. 状態台帳（本記録作成時点）

```text
DOCUMENT_STATUS_RECONCILIATION = COMPLETE
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

## 8. 固定根拠文書

1. [REQUIREMENTS.md — fixed HEAD](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/REQUIREMENTS.md) — Blob `490e4d38d022c5ba3c748131775035b0f33f7b21`。
2. [MVP Design R3.1 — fixed HEAD](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md) — Blob `ce3dccb99e607e74b608c636f4146a65c1671f34`。
3. [Conditional Implementation Plan R1 — fixed HEAD](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/docs/superpowers/plans/2026-10-28-chronos-skills-mvp-implementation-plan-R1-gated.md) — Blob `64e888009e46586a572dde98dc6a7e1ec127c701`。
4. 本対話で明示されたHuman Owner判断：R3.1文書受入、G-01棚卸し・是正工程CLOSED、Option Aの制約付きCLOSED、および今回の選択肢A承認。これらは固定HEADのGit blobとは**異なる時点・異なる種類の根拠**である。

---

**結論:** ChronosSkillsの設計・条件付き計画の「文書到達点」の整理を終了する。将来の実装可能性、Gate PASS、実行可能計画完成、MVP実装完了を宣言しない。
