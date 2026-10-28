# ChronosSkills — G-02〜G-05 契約候補・Owner判断条件 R1

**作成基準日:** 2026-10-29（JST）  
**対象:** `yohi/chronos-skills`  
**固定参照HEAD:** `b778b20856613f9c50649347cad2628520ced5b4`  
**位置づけ:** Human Owner承認の1回限りの `DOCUMENT_ONLY / EXISTING_APPROVED_SOURCES / CONTRACT_CANDIDATE_PREPARATION_ONLY`  
**明示的な非許可:** `NO_NEW_FEASIBILITY_RESEARCH / NO_HOST_VERIFICATION / NO_TEST_EXECUTION / NO_SOURCE_MODIFICATION / NO_GIT_OR_GITHUB_WRITE / NO_ARCHITECTURE_ADOPTION / NO_GATE_AUTHORITY / NO_AUTO_NEXT_PHASE`

> **判定:** `DOCUMENT_CANDIDATES_PREPARED / OWNER_SELECTION_PENDING / TECHNICAL_EVIDENCE_UNCHANGED`。本資料は**実装契約の確定版ではなく、条件付き契約候補と不足入力の台帳**。仮の操作名、属性、選択肢は設計決定・実装命令ではない。既存要件・採択済み方針を変更せず、技術的未証明事項をPASS扱いしない。

## 0. 本書の最短結論

- **元のゴール:** 既存Skills・ChronosGraphを先に利用し、必要な場合だけ新規の一時Skillを**自律生成→静的検証→生成元Task限定利用→軽量評価→Human採択候補提示**するOpenCode v2のMVP。検索機能だけでD-02/AC-02を置き換えない。
- **設計書:** R3.1は受入済みの論理設計。**実装計画書:** R1は保存済みの条件付き計画で`NOT_EXECUTABLE`。I-01〜I-09は追跡用スロットで実行命令ではない。
- **既存判断:** D-01〜D-10とQ1〜Q7、Q10を維持。Q8は`OWNER_ADOPTED_CONDITIONAL / FEASIBILITY_NOT_VERIFIED`。MCP優先・STDIO・最小CLI・共通Core・HTTP後回しの論理方針を維持するが、API・言語・ストアは未採択。
- **G-01:** Root発行起点・Caller真正性・完全仲介は`OPEN / NOT_ESTABLISHED`。本書では解かない。元の棚卸しは`CLOSED`、Option Aは`OWNER_ACCEPTED_WITH_LIMITATIONS / CLOSED`、その独立レビュー`BLOCKED`は維持。
- **今回の成果:** G-05→G-04→G-03→G-02の順で、各Gateの**固定要求、採択前の契約候補、反例、必要証拠、Owner判断項目**を一度だけ記録。ここに記した順序は文書整理の便宜であり、技術的な依存グラフや採択順の確定ではない。

## 1. 契約候補の読み方・全Gate共通原則

| 記号 | 意味 | 何を意味しないか |
|---|---|---|
| **FIXED** | 既存R3.1/条件付き計画にある採択済みの論理要求 | Runtimeで実証済みとは限らない |
| **CANDIDATE** | 本書で判断可能な形へ展開した**未採択の**契約案 | API、サービス、データ構造、方式の正式決定ではない |
| **OWNER_DECISION_REQUIRED** | Human Ownerが後日、選択肢・具体方式・スコープを採択する事項 | この文書の作成承認による採択ではない |
| **EVIDENCE_REQUIRED** | 実効性を判定するには独立の技術的根拠が必要な事項 | 証拠取得・Host/コード解析の実施許可ではない |
| **STOP** | 必須前提がない場合は技術判断と実行をそこで止める | 全開発業務を止めることではない |

**不変の権限分離（FIXED）:** ①Root新規発行、②既存Context利用、③子Agent委譲、④Attempt予約・消費は別権限。Session ID、Metadata、MCP接続、一般Server認証、Agent自己申告、署名のないProject名を権威のある根拠へ昇格しない。Caller A（MCP-only）・B（Shell等の一般Tool）を防御対象とし、実効権限がHost相当ならCとして評価する。Cを除外する証拠なしにプロンプトによる禁止を強制分離とみなさない。

**失敗時の一般原則（FIXED）:** 生成・一時利用の認可不明はfail-closed、通常の開発タスクは可能な限り継続。未承認Skills・グローバル設定・既存Skillを上書きしない。Humanによる正式採用判断とGit/配布の実施権限は別。

## 2. G-05 — Project/Task識別と参照強制の契約候補

**Finding:** `CS-R1-005 / OPEN / UNDECIDED / NOT_VERIFIED`。参照：R3.1 §3.1、§7.4、計画R1 §1、I-01/I-02/I-05/I-07/I-08。G-01によるRoot真正性が未成立なので、**RootやSessionから受け取った自称Project/Taskをそのまま信頼しない**。

### 2.1 FIXED — 守るべき契約

- `project`ラベル、ローカルパス、ChronosGraph検索フィルタ、Agentの説明は**認可そのものではない**。
- 既存Skill・ChronosGraph検索結果、生成Draft、保存済み候補、評価記録について、リクエスト権限と**返却する項目ごと**の帰属・公開可否を照合する。
- Project/Taskの信頼元不明、越境項目、失効・親子関係不明なら**当該データの提供・利用を拒否**する。元の開発タスクまで一律停止しない。
- 同一Projectの次Taskでの候補の**明示的参照・再評価**と、**未承認Skillの自動ロード**は別物。

### 2.2 CANDIDATE — 単一の認可判断契約（概念形、未採択）

下表は**要否をOwnerが決める項目一覧**であり、実装するフィールド名・型・トークン形式を指定しない。

| 論理入力 | 必須の意味 | 不信任・不足時の反応 |
|---|---|---|
| Callerの検証済み根拠 | Agent自己申告と区別できる根拠。誰が検証するかは未決定 | `DENY/UNDETERMINED`相当。自称の権限で継続しない |
| Project/Taskの検証済み帰属 | 正規のスコープとRoot/子委譲の関係（G-01依存） | 帰属が不明な項目を返却しない |
| 要求する操作 | 検索、候補の参照、評価記録参照、一時利用、更新・失効を分離 | 読取許可を利用・更新許可へ拡張しない |
| 対象リソースの帰属 | 検索結果・Draft・評価記録それぞれの所有と公開属性 | 一括検索許可を項目の許可へ自動転用しない |
| 使用時点と有効期限 | Task終了・候補期限・明示再評価か自動利用かの別 | 古い認可を使い回さない |
| 判定結果と理由 | Allow/Deny/Unknownの意味、秘匿すべき理由を分離 | Unknownは生成・利用について拒否。機密をエラーへ露出しない |

**境界配置は未選択:** (a) Coreが最終的な強制点、(b) AdapterとCoreによる二段検査、(c) 連携先の検査と自分の項目別再確認、などを比較可能な選択肢として残す。いずれも単に「チェックを置く」と書くだけでは成立せず、**迂回可能なすべての経路**と権限材料の保護を別途証明する必要がある。

### 2.3 反例・拒否条件（テスト計画への入力であり実行許可ではない）

| 反例 | 必須の結果 |
|---|---|
| 別Projectの結果が検索応答に紛れる | その項目を提供しない。フィルタ指定だけに依存しない |
| CallerがProject名・Task IDを偽装 | 確認済み権限のない対象を返さない |
| 子Agentが親Rootの外にある候補を参照 | 拒否、親の予算や権限を拡大しない |
| 同Projectの次Taskが保存候補を検索 | 明示再評価の可否を別判断し、自動使用は拒否 |
| 元Task終了・期限切れ・帰属検証不能 | 当該候補の利用を拒否し、必要なら通常タスクへ戻す |

### 2.4 Owner判断・必要証拠

- **OWNER_DECISION_REQUIRED:** 正規Project/Task識別の発行・照合主体、各リソースの所有とACLの意味、誰が最終拒否を執行するか、別Task明示再評価を許可する条件、監査上保持すべき判定根拠。
- **EVIDENCE_REQUIRED:** 資格・帰属の改ざん／越境への反例、ChronosGraph検索結果とDraft保管の到達経路、実効的な強制点、終了後の拒否、G-01との信頼境界整合。**未取得。**
- **STOP:** 信頼元がAgent自己申告だけ、あるいは検索ラベルを認可と扱うなら本Gateは`OPEN`のまま。

## 3. G-04 — Adapter/Core公開契約・型と失敗伝播の契約候補

**Finding:** `CS-R1-004 / OPEN / UNDECIDED / NOT_IMPLEMENTED`。参照：R3.1 §4.1.2、§5.1、計画R1 §1、I-01〜I-09。Q2の`MCP_PRIMARY / STDIO / CLI_MINIMAL / SHARED_CORE / HTTP_DEFERRED`はFIXED。**公開API数、具体名、型、version、シリアライズは未採択。**

### 3.1 FIXED — 境界の役割

- Agentは不足候補・必要性・既存スキルとの差・新規ドラフト本文の意味判断を担うが、Root発行・予算増額・正式採択を自己宣言できない。
- Coreは論理上の検索検査・ポリシー・Attempt・候補状態を担う。AdapterがCoreの認可拒否を許可に変えない。
- **新規Root発行は、既存Context利用やAttempt予約と別権限**。G-01の実効境界未成立の間は、Root発行を「公開可能な操作」として確定しない。

### 3.2 CANDIDATE — 操作群（概念名は仮称。公開可否は未決定）

| 候補の論理操作群 | 入力に必要な意味 | 出力・拒否の意味 | 他Gateに依存する点 |
|---|---|---|---|
| 既存Skill・記憶の検索 | 検索意図、検証済みProject/Task、照会先 | 項目別に許可された結果、越境は返さない | G-05 |
| 新規生成の必要性判定 | 不足の根拠、既存候補、再利用価値、危険度 | `SKIP` / `UNDETERMINED` / 生成候補（許可ではない） | G-05 |
| **既存**Contextの検証・状態参照 | 権限材料、操作範囲、Task/Project | Valid/Denied/Unknown。Root新規発行と別 | **G-01、G-05** |
| Attemptの予約・開始許可 | 有効Root、予算、idempotency、要求Fingerprint | Reserved/Started/Conflict/Denied等（物理LLM実行とは別） | **G-01、G-03、G-05** |
| 新規Skill候補の登録・静的検証 | Draft、帰属、検査結果、秘匿要件 | Verified/Rejected/Unknown。実行安全性PASSではない | G-05、G-03（生成経路） |
| 当該Task限定の一時利用の認可・撤回 | 検証済みTask、候補、期限、ロード状態 | 利用許可／拒否／失効状況 | **G-01、G-02、G-05** |
| 評価・保存候補の記録・参照 | Task結果、測定可能/不明な値、候補帰属 | 評価済み／未評価を区別し、次Task自動利用なし | G-05、G-02 |
| Human昇格候補の提示 | 評価記録、候補、理由 | 提案のみ。正式採択/Git書込/配布は別 | Owner権限 |

**注:** 上記の操作名や項目名を**型名／関数名／MCP Tool名として使用する決定はしていない**。特に信頼起点が未確定なRoot新規発行操作は定義・公開・実装しない。読み取り系もProject/Taskの真正性が未成立なら越境する照会を許可しない。

### 3.3 CANDIDATE — 失敗・不明状態の伝播表

| 意味 | 既存の固定語／概念候補 | 期待する境界応答 | 禁止する誤処理 |
|---|---|---|---|
| 判断不能 | `UNDETERMINED`（FIXED） | 生成へ進まない。通常タスクへ戻せる | `SKIP`や成功と混同して新規生成 |
| 冪等性衝突 | `CONFLICT`（FIXED） | 新しい開始許可と予算消費なし | 衝突を再送成功へ変換 |
| 認可拒否 | 候補分類（型・コード未決定） | 生成・利用等の特権操作を拒否 | デフォルトAllowへの変換 |
| 予算不足 | 候補分類（型・コード未決定） | 新たなAttemptを開始しない | 上限超過を再試行で回避 |
| 状態未確定・応答逸失 | `UNCERTAIN`（FIXED） | 枠を保持、証拠なき返却と開始許可再発行なし | タイムアウトを「未消費」に変換 |
| 外部依存不可 | 候補分類（型・コード未決定） | 許可不能な操作を安全停止、元タスク通常復帰 | 照会失敗を認可成功と解釈 |
| 期限切れ／帰属不明 | 候補分類（型・コード未決定） | 一時利用／候補参照を拒否 | 最終認可の使い回し |

### 3.4 Owner判断・必要証拠

- **OWNER_DECISION_REQUIRED:** 各操作が公開MCP、最小CLI、Core内部のどこに属するか、認可材料を境界越しにどう検証するか、採択する具体的なtype shape・version・serialisation、監査記録者・保持先・秘密除去、エラーのAgent伝播契約。
- **EVIDENCE_REQUIRED:** G-01の真正Root、G-05のResource認可、G-03の状態所有とG-02の一時提供方式が整合すること。後日の契約テスト・失敗伝播テストを別承認で行う。**未取得。**
- **STOP:** 未解決Gateを仮のAPI型で埋め、実装者が独断で採択する、拒否を成功扱いする、認可素材をそのまま記録するなら`OPEN`。

## 4. G-03 — Root共有予算・Attempt状態管理の契約候補

**Finding:** `CS-R1-003 / OPEN / UNDECIDED / NOT_VERIFIED`。参照：R3.1 §6.2〜§6.2.1、計画R1 §1・I-04。Q4〜Q7の論理契約はFIXED。ただし**実際のauthoritative owner、DB/Store、排他/トランザクション、耐久性は未決定**。

### 4.1 FIXED — 見落としてはいけない不変条件

- 同じRoot Taskの親子Agent合計で**生成・修正Attemptは最大2**。複数の予約・消費・不明枠を二重計上しない。
- `STARTED`は**Coreによる開始許可と消費の永続確定**であり、LLMが実際に走ったことの測定結果ではない。
- `UNCERTAIN`は判別不能な枠の保持。`RELEASED`は**開始許可を発行していないことを証明できる予約だけ**に適用。
- 同一Root＋Key＋Fingerprintの再送は既存Attempt照会。同一Key・異なるFingerprintは`CONFLICT`。応答逸失後に開始許可を再発行しない。
- トークン計測不能は`UNKNOWN`であり、消費ゼロとみなさず、測定可能な別上限で保守的に制御する。

### 4.2 CANDIDATE — 状態所有・更新の契約（方式未選択）

| 論理操作 | 必要な前提・原子性 | 結果と失敗時の必須契約 |
|---|---|---|
| 既存Rootに対する残予算確認 | G-01で発行された正規Root、同じauthoritative状態の読取 | 不明なら新規開始を認めない |
| Attempt予約 | Root単位上限、Key/Fingerprint衝突確認、同時実行を同一原子境界で確定 | 2枠超過・競合を拒否。二重予約しない |
| 開始許可＋消費確定 | 永続化と状態遷移が失われない原子境界 | `STARTED`確定後の応答再送で二重開始を許さない |
| 既存Attempt状態照会 | 紐づくRoot・Caller・Project権限確認 | 同一Key再送は参照に収束 |
| 不明応答・中断の復旧 | 確定ログ・保留状態・tombstone・終了期限を復元 | 不明時は`UNCERTAIN`保持、復元不可なら生成停止 |
| 未開始証明に基づく返却 | 開始許可未発行を検証可能 | 証拠がある予約に限り`RELEASED`、枠復活・後発開始なし |
| Root終了・期限切れ | 終了・期限の信頼根拠 | 新規Attempt拒否、許可された状態照会のみ |

**実装候補の選択は保留:** 単一Coreプロセス、永続DB、その他の共有状態所有形態をここで選択しない。プロセス内カウンタのみでマルチAgent共有を満たすと宣言しない。

### 4.3 反例・拒否条件（テスト未実行）

| 反例 | 要求結果 |
|---|---|
| 親と子から同時に3回目予約 | 最大2を越えず拒否 |
| 同一Key・異Fingerprintで再送 | `CONFLICT`、新規開始許可なし |
| `STARTED`永続化直後に応答喪失 | 既存Attempt参照または`UNCERTAIN`保持。別の開始許可を発行しない |
| `RESERVED`の開始未許可を証明できない | `RELEASED`禁止 |
| クラッシュ後に台帳が復元できない | 新規生成fail-closed |
| トークンが観測不能 | `UNKNOWN`、ゼロ扱いせず保守的制限 |

### 4.4 Owner判断・必要証拠

- **OWNER_DECISION_REQUIRED:** Root共有状態を所有する唯一のauthority、永続ストア方式、原子的更新境界、障害時復元、Time/Tokenの上限初期値、tombstone/保持期間、権限検査の担当。
- **EVIDENCE_REQUIRED:** Atomic性・競合・再送・クラッシュ・復旧・期限・Root共有の反例と成功条件。G-01の正規Root、G-04の失敗表現との整合。**未取得。**
- **STOP:** 同じAgent内だけのカウンタ、部分確定、証拠なき枠の返却、応答喪失後の再許可を認める案は不適合。Gateは`OPEN`。

## 5. G-02 — OpenCode v2.1.4 Task限定Skill提供・失効の契約候補

**Finding:** `CS-R1-002 / OPEN / UNDECIDED / NOT_VERIFIED`。参照：R3.1 §4.1.3、§6.3、計画R1 §1・I-06/I-07。**OpenCode v2.1.4に具体的なTask限定SkillロードAPIがあるとは確認していない。**

### 5.1 FIXED — 守るべきライフサイクル

1. Agentが必要性・既存資産を評価し、生成不必要なら`SKIP`。不明なら`UNDETERMINED`で生成不可。
2. 正規RootおよびAttempt許可後にのみ**新規**一時Skillドラフトを生成（既存Skillの自動改変は禁止）。
3. **静的検証に合格した候補だけ**を生成元Taskで利用可能にする。静的検証合格は実行安全性・効果の保証ではない。
4. Task終了・失効・認可不明・キャッシュ残存時に別Taskで自動利用できない。グローバルSkill一覧や未承認の既存Skill配置には混入させない。
5. 候補を同一Project内で期限付き保持し、次Taskの**明示的再評価**と**自動ロード**を分離する。
6. Human昇格の承認とGit/配布権限は別。

### 5.2 CANDIDATE — ライフサイクル境界と責務（責任主体未選択）

| 段階 | 必須の成立契約 | Ownerが後で選ぶ事項 |
|---|---|---|
| Draft保持 | Project/Task帰属、未承認状態、期限、隔離 | 保持先、容量、更新・削除権限 |
| 静的検証 | 形式・参照・秘密・危険指示・名称衝突の拒否 | 検査規則と検証主体 |
| 現在Task向け提供 | **認可済みTaskのみ**利用。発見経路も認可の対象 | Core/Adapter/Host/SkillPort等の提供主体、公開範囲 |
| OpenCodeへのロード | Global/別Taskへ未承認混入なし | API/Host拡張/限定ファイル等の候補選定（**本書未採択**） |
| Task終了/失効 | 終了通知逸失・キャッシュ残存でも別Task自動利用拒否 | 有効期限判定主体、解除・再確認の強制点 |
| 保管と再評価 | 保存候補の明示参照と自動利用を区別 | 次Taskからの再評価承認条件、保持日数 |
| 正式昇格候補提示 | 評価記録とHumanレビュー要求のみ | 正式保存先・Git/配布の別途承認手順 |

### 5.3 反例・拒否条件（v2.1.4での成立は未検証）

| 反例 | 必須の結果 |
|---|---|
| Task終了イベントが届かない | 次Taskが候補を自動ロードできない |
| OpenCodeの発見キャッシュに候補が残る | 期限切れ／別Taskでは利用不可 |
| 非正規のAgentが配置パスを指定 | 自動ロードやグローバル混入を許さない |
| 静的検証前・不合格のDraft | 利用拒否 |
| 同じProjectの次Taskで候補を照会 | 明示参照・再評価は別認可、自動利用は拒否 |
| Root/Taskの真正性が不明 | 一時Skill提供を行わない |

### 5.4 Owner判断・必要証拠

- **OWNER_DECISION_REQUIRED:** Draft配置、提供/ロードの最終責任主体、認可を毎回強制する点、失効の起点とキャッシュ対処、保存TTL・容量、静的検証規則、形式と評価契約。
- **EVIDENCE_REQUIRED:** 固定OpenCode v2.1.4での発見・ロード・キャッシュ・Task境界・失効の挙動、上記反例への防御根拠、G-01/G-05のスコープ認可との整合。**未取得。**
- **STOP:** 未承認Skillをグローバルへ配置、別Taskに継続して自動提供、失効の強制点が不明、v2.1.4成立根拠なしの場合、Gateは`OPEN`。

## 6. Gate依存マトリクス（契約上の入力、技術方式の採択ではない）

| 対象 | 確定前に整合させる入力 | まだ許可されないこと |
|---|---|---|
| **G-05** | G-01の検証済みCaller/Root/子委譲、認可するResourceの帰属 | 信頼元未証明のまま全検索/参照を許可 |
| **G-04** | G-01 Root権限、G-05 scope、G-03台帳状態、G-02提供/失効責務 | 仮操作名/仮フィールドのAPI確定・公開 |
| **G-03** | G-01正規Root、G-04失敗伝播、G-05 Project/Task scope | 台帳の開始許可発行、G-01未証明下の生成 |
| **G-02** | G-01認可、G-05 scope、G-04操作、G-03開始予算、OpenCode v2.1.4の実証 | Task限定ロードの成功宣言、未承認Draft利用 |

**依存の扱い:** G-01がHOLDでも各Gateの論理上の必要条件を文書にできる。しかし具体的な方式採択・型確定・成立PASS・実装開始はできない。G-04は他Gateを参照する「境界の接着剤」であるため、**最終的な公開API/型の確定は前提境界のOwner採択後**に限定する。

## 7. Ownerが後日判断する項目（今回の採択対象ではない）

| 判断ID | 論点 | 決定に必要な入力 | 今回の状態 |
|---|---|---|---|
| `OD-G05-01` | Project/Taskの信頼元と最終参照認可主体 | G-01境界、返却/保存リソース、拒否ケース | `UNDECIDED` |
| `OD-G05-02` | 再評価・失効・越境項目のOwner条件 | 帰属・期限・アクセス分類 | `UNDECIDED` |
| `OD-G04-01` | MCP/CLI/内部Coreに公開する操作と型 | G-01/G-05/G-03/G-02の採択済み境界 | `UNDECIDED` |
| `OD-G04-02` | 失敗伝播・version/監査/秘密境界 | Operationと権限別の応答仕様 | `UNDECIDED` |
| `OD-G03-01` | 共有予算のauthoritative owner/永続Store/排他 | 正規Rootと並行・復旧要件 | `UNDECIDED` |
| `OD-G03-02` | Budget初期値・期限・tombstone運用 | 測定可否、失敗・クラッシュ時の制約 | `UNDECIDED` |
| `OD-G02-01` | Task限定提供/ロード/失効の方式 | v2.1.4での証拠、G-01/G-05真正性 | `UNDECIDED` |
| `OD-G02-02` | 静的検証・保存TTL・次Task再評価方式 | 状態遷移、検証規則、アクセス契約 | `UNDECIDED` |
| `OD-ENV-01` | 実装言語/依存/テスト実行環境/実在パス | Git HEAD内の実在構成確認と採択 | `UNDECIDED` |
| `OD-G01-01` | 新しい技術成立性作業を行うか、HOLD継続か | **Option Aを再実行せず**別途境界/STOPを定めたOwner判断 | `NOT_AUTHORIZED / HOLD` |

**今回Ownerが承認したのは、この判断表の作成だけ。** 表に記載したいずれの案・方式・値についても採択していない。ここから新たな調査依頼・Codex実行指示・Independent Gate依頼を自動発行しない。

## 8. 実行可能計画の入力欠損表と終了判定

| 実行可能な`writing-plans`後続版に必要なもの | 既存R1計画での到達点 | 現在の欠損 | 今回増えたのは何か |
|---|---|---|---|
| D-01〜D-10 / FR-01〜12 / AC-01〜11追跡 | 概念マッピング済み | Runtimeの合格証拠なし | 既存範囲を維持した契約候補 |
| G-01真正Root・完全仲介 | 要求とHOLD記録済み | F-01/F-02/F-05 `NOT_VERIFIED` | **証拠は増えていない** |
| G-02 Task限定提供 | 要件・反例あり | 方式の選択とv2.1.4実証 | 対象責務・拒否条件の具体化 |
| G-03予算・復旧 | 状態と不変条件あり | authoritative owner/Store/Atomic性 | 操作・反例・Owner判断点 |
| G-04 API/型/エラー | 概念責務と失敗分類あり | 確定署名/形/プロトコル | 未採択操作群・失敗伝播表 |
| G-05 Project認可 | 境界・fail-closed要求あり | 信頼元/参照強制点/越境実証 | 項目別認可入力・拒否条件 |
| 開発基盤・実在ファイル | 候補ルートパスの限定確認あり | 言語/構成/テスト環境のOwner採択 | **決定なし** |
| I-01〜I-09の実行単位 | 追跡用スロットのみ | 実パス、型署名、RED/GREEN、assert、コミット | **作成・実行していない** |
| 計画と設計の独立整合確認 | 概念上の自己点検あり | 後続最終計画の独立確認 | **独立レビューしていない** |

**判定:** 文書具体化タスク＝`COMPLETED`（本書の提示時点）。G-02〜G-05＝`OPEN / UNDECIDED`、実行可能版計画＝`NOT_PREPARED / BLOCKED`。Gateチェック数を変更しない。

## 9. 引継ぎ・STOP（自動移行しない）

```text
DESIGN_R3_1 = OWNER_ACCEPTED_LOGICAL_DESIGN
CONDITIONAL_PLAN_R1 = PREPARED_AND_SAVED / NOT_EXECUTABLE
G01_EVIDENCE_INVENTORY_AND_REMEDIATION = OWNER_ACCEPTED / CLOSED
OPTION_A = OWNER_ACCEPTED_WITH_LIMITATIONS / CLOSED
OPTION_A_INDEPENDENT_REVIEW = BLOCKED

G01_TECHNICAL_BLOCKER = OPEN
G02_G03_G04_G05 = OPEN / UNDECIDED
F01_F02_F05 = NOT_VERIFIED
FEASIBILITY_NOT_VERIFIED
ROOT_AUTHORITY_NOT_ESTABLISHED
AUTONOMOUS_GENERATION_HOLD
IMPLEMENTATION_NOT_AUTHORIZED
EXECUTABLE_PLAN_BLOCKED
NO_AUTO_R2
NO_NEW_GITHUB_WRITE_AUTHORIZED
```

- **今回実施:** 固定文書と前工程のOwner判断資料から、未採択の契約候補・反例・依存・判断入力を**1回限り**で作成した。
- **今回未実施:** G-01の成立性調査・Option A再開・新しいコード/Host検証・方式採択・実装/API確定・Git/GitHub書込・独立レビュー・Gate操作。
- **次に判断する人:** Human Owner。選択肢は、(a)この候補文書を判断材料として受領して保留、(b)特定Gateの方式選択または技術成立性作業を**別の明示権限**で限定承認、(c)全体をHOLD継続。いずれもこの文書では選択しない。
- **STOP:** 本資料の受渡しをもって、D1の1回限りのDOCUMENT-ONLY作業は終了。自動的なD2研究、Codexへの指示、独立レビュー、GitHub書込、Gate昇格を行わない。

## 10. 固定根拠と取扱い

1. `REQUIREMENTS.md`（固定HEAD、blob `490e4d38d022c5ba3c748131775035b0f33f7b21`） — [read-only](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/REQUIREMENTS.md)
2. `docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md`（blob `ce3dccb99e607e74b608c636f4146a65c1671f34`） — [read-only](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/docs/superpowers/specs/2026-10-28-chronos-skills-mvp-design.md)
3. `docs/superpowers/plans/2026-10-28-chronos-skills-mvp-implementation-plan-R1-gated.md`（blob `64e888009e46586a572dde98dc6a7e1ec127c701`） — [read-only](https://github.com/yohi/chronos-skills/blob/b778b20856613f9c50649347cad2628520ced5b4/docs/superpowers/plans/2026-10-28-chronos-skills-mvp-implementation-plan-R1-gated.md)
4. `ChronosSkills_G01_G05_Owner_Technical_Decision_Brief_R1_20261029.md` — 先行Owner判断資料（作業コンテナ内、固定GitHub文書とは異なる）。

**固定文書内のOption A「未実施」、R3.1受入待ちなどの表現は作成時点の記録であり、現時点のOwner判断により更新された履歴と区別する。** 本書はその履歴の取り扱いを整理するだけで、固定HEADの文書自体は変更しない。
