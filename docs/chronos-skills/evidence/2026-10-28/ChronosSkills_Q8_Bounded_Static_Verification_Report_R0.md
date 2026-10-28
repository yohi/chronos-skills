# ChronosSkills — Q8 F-01 / F-02 / F-05 限定静的検証報告 R0

**実施基準日:** 2026-10-28（JST）  
**権限:** `OWNER_AUTHORIZED / BOUNDED_READ_ONLY_STATIC_EVIDENCE_CHECK / F01_F02_F05_ONLY / NO_Q9_R2_INDEPENDENT_REVIEW / NO_HOST_EXECUTION / NO_TESTS / NO_NEW_ARCHITECTURE / NO_IMPLEMENTATION / NO_GITHUB_WRITE / NO_GATE_AUTHORITY`  
**基準文書:** `ChronosSkills_DESIGN_OWNER_ADOPTED_R2.md`、`ChronosSkills_Q8_Minimal_Feasibility_Verification_PLAN_R0.md`（双方は変更なし）  
**固定ソース:** `anomalyco/opencode` commit `68bc26b7da1990ce10ffecfbc1e09eb13aa5d824`（採択計画ではv2.1.4として特定）  
**総合判定:** **`NOT_VERIFIED / FEASIBILITY_NOT_VERIFIED / ROOT_AUTHORITY_NOT_ESTABLISHED / STOP`**

## 1. 判定の要約

**F-01／F-02／F-05を組み合わせた、非認可CallerからのRoot Task Context新規発行の完全仲介・Caller識別・迂回防止は、この静的証拠だけでは成立を示せない。** これは**「あらゆる設計方式が不可能」との判断ではない**。今回確認できたのは、OpenCodeの一般Session機構と公開Tool経路において、安易に信頼根拠へ昇格してはいけない入口の存在、およびChronosSkills Root発行についての未提供の実装・環境情報である。

| 項目 | 判定 | 確認できた事実と不足 |
|---|---|---|
| F-01 Root発行の完全仲介 | `NOT_VERIFIED` | Session Create、Import、Fork、Update、Plugin Session入口は確認。ChronosSkillsのRoot発行効果・すべての呼出辺・必須認可点は未提示／未実装の設計対象であり、経路閉包を証明できない |
| F-02 信頼されたCaller識別 | `NOT_VERIFIED` | Session Metadata/ID、Parent、一般Server認証にRoot固有の発行権限は確認できない。Root権限の発行主体、保持先、検証点、権限の非共有・非循環の実証資料がない |
| F-05 A/B/Cの実効経路 | `NOT_VERIFIED` | MCP・Shellの通常permissionチェック、HTTP認証、Plugin Session APIは確認。これらを横断するRoot発行への完全仲介、Host同等権限の可否、資格情報／モジュールアクセス制約が未証明 |
| Q8総合 | **`FEASIBILITY_NOT_VERIFIED`** | 静的に成立する具体的なRoot発行構成を証明する材料がないため、`STATIC_FEASIBLE`とはできない |

既知の代替設計に対する**条件付き静的反例**はある（後述CE-01、CE-02）が、未知の実装を含む全方式の不可能性ではない。Host側の構成がないまま`STATIC_COUNTEREXAMPLE`をQ8全体の結論にもしていない。

## 2. SOURCE_LOCK（読み取り専用の固定証拠）

GitHub接続のファイル取得操作で、次の各ファイルを**すべて同一の固定commit**を指定して再取得した。各Blob SHAはAPIが返した値であり、ローカルで独立再ハッシュした値ではない。

| パス（`anomalyco/opencode`内） | Git Blob SHA-1 | 該当行 |
|---|---|---|
| `packages/protocol/src/groups/session.ts` | `95683c846c3cc69b56fb29a42389059c1ae97327` | 156–190、249–258、298–314 |
| `packages/server/src/handlers/session.ts` | `0386bd44931a8c4b8d74bdcc414024552e88abae` | 93–130、193–202、226–251 |
| `packages/core/src/session.ts` | `cb21826e1c21724cd9c4d3302ed8cd9976bbebf4` | 275–324、326–365 |
| `packages/core/src/session/transfer.ts` | `1c0cd94f9a6be85cc147d0892e5ea6fc745d7095` | 64–113 |
| `packages/core/src/plugin/host.ts` | `1107dd665bb2682b3a6586d9dc39832f6569a2d6` | 547–583 |
| `packages/plugin/src/promise/adapter.ts` | `34e8c908657ddae9b098182cf2236993677d9ede` | 関連APIアダプタ（部分確認） |
| `packages/core/src/tool/mcp.ts` | `6d1b8cc9753a36643bebd0eb71e6f45ee183723e` | 46–79 |
| `packages/core/src/mcp/client.ts` | `55ef10dd207cb992a81e87e33cdb7b965b240128` | 388–398 |
| `packages/core/src/tool/plugin/shell.ts` | `af454a7d63391473d939436c87fff5c4b73eefb5` | 117–150、198–219 |
| `packages/server/src/middleware/authorization.ts` | `67dbd9947816bf7a78c50ef729002188dd5473c1` | 74–101 |
| `packages/core/src/plugin/hooks.ts` | `1325837f9d5864b2619b795c2cd1493d291786d1` | 23–29 |

固定commitはGitHub接続で取得可能と確認した。**タグ `v2.1.4` からこのcommitへの独立したタグ参照解決は今回得られていない**ため、当該対応は採択済み計画の固定前提に依拠する。タグの対応未照合を、別commitの証拠で補完していない。

**コードの根拠（immutable URL）**：
- [Session Protocol](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/protocol/src/groups/session.ts#L156-L190)
- [Session Handler](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/server/src/handlers/session.ts#L93-L130)
- [Core Session](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/session.ts#L275-L324)
- [Session Transfer](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/session/transfer.ts#L64-L113)
- [Plugin Host](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/plugin/host.ts#L547-L583)
- [MCP Tool](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/tool/mcp.ts#L46-L79)
- [MCP Client](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/mcp/client.ts#L388-L398)
- [Shell Tool](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/tool/plugin/shell.ts#L117-L150)
- [Server Authorization](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/server/src/middleware/authorization.ts#L74-L101)
- [Plugin Hooks](https://github.com/anomalyco/opencode/blob/68bc26b7da1990ce10ffecfbc1e09eb13aa5d824/packages/core/src/plugin/hooks.ts#L23-L29)

## 3. ENTRYPOINT_MATRIX — Root発行候補と一般Session操作を分離

**記号**：`S`＝固定ソースで経路を確認、`U`＝Root発行への結び付き・認可根拠は未確認。ここでの発行効果は**OpenCode Session**であって、ChronosSkills Rootではない。

| 入口 → 中間処理 → 既知の効果 | 主なCaller | 既知の仲介／拒否 | ChronosSkills Rootとの関係 |
|---|---|---|---|
| HTTP `POST /api/session` → Handler `session.create` → Core `Session.create` → `SessionEvent.Created` | 公開APIクライアント（有効認証やアクセス前提） | API通常の認証・schema。Root専用の権限チェックは提示されていない | S: Session作成。U: Root発行への接続・保護 |
| HTTP `/api/experimental/session/import` → `SessionTransfer.import` → `SessionEvent.Created` | 同上 | 存在するSession IDの競合検査、Parent参照。Root認可とは異なる | S: Import由来Created。U: Root発行可否 |
| HTTP `/api/session/:sessionID/fork` → `Session.fork` → `SessionEvent.Forked` | 同上 | 元Session/境界の存在確認。Root発行の仲介ではない | S: Session fork。U: Root発行可否 |
| HTTP `PATCH /api/session/:sessionID` → `setMetadata` / `setPermissions` | 同上 | Session存在確認。値のRoot発行主体に関する証明なし | S: Metadata等変更可。U: Root権限の非偽装性 |
| Plugin Host `session.create` → Core `Session.create` | Plugin Host APIを利用できる主体 | Plugin HostからCore Sessionへ直接委譲する経路。Root固有チェックは観測されない | S: Session作成。U: Plugin Callerの真正性・Root権限 |
| Plugin Hook | Pluginイベント利用者 | Hookのエラー経路は限定的。`tool.execute.before`のみ失敗可能と記述 | U: 直接Session APIを含むRoot発行の完全仲介にはならない |
| 通常MCP Tool → `permission.assert` → `mcp.callTool` | A、B | 通常ツール許可判定 | S: MCP tool経路。U: 発行専用資格・迂回防止 |
| 通常Shell Tool → parser/permission → shell spawn | B（実効能力は環境次第） | Shell解析・外部ディレクトリ認可・通常許可 | S: Shell実行経路。U: Host API、IPC、モジュール、資格情報への到達性 |
| ChronosSkills CoreでのRoot Task Context新規発行 | 信頼済みHostのみを意図したQ8の設計上の主体 | **発行効果・認可点・Caller証明の実装は未提示** | **U: 現段階で完全仲介PASSを付けられない** |

この表は今回読み取った**限定的な入口一覧**であり、OpenCode v2.1.4全体の全API・Plugin・プロセスの完全集合を列挙したとの主張ではない。Root処理の実体が未特定である以上、Root発行に関する呼出グラフの完全性は確定できない。

## 4. CALLER_MATRIX — Q10の実効能力ベース

| Caller | 静的に確認できた入口 | 分離を保証できない要素 | 判定 |
|---|---|---|---|
| A: MCP-only | OpenCode MCP Toolに通常`permission.assert`。`_meta["ai.opencode/sessionID"]`はMCP呼出へ転送される | Root専用Caller認可・Session IDの発行者認証・発行操作の非公開性 | `NOT_VERIFIED` |
| B: Agent Tool / Shell | Shellは通常許可のもとspawn可能。HTTP APIとPlugin APIは別経路として存在 | Shellの実効OS ID・権限、ポート/IPC/ファイル/Moduleへ到達可能か、Server認証情報共有の有無 | `NOT_VERIFIED` |
| C: Host相当の任意コード | Plugin Host APIはCore Session呼出を持つ | C相当の能力をAgent Bが実際に持つか、その分離の強制証拠 | `NOT_VERIFIED` |

**Caller B＝Cとは断定しない。一方、Cの除外はできない**。ShellからHost認可対象に到達できるという攻撃は現実のHost構成が未提示のため**条件付きの懸念**であり、実証済みの迂回経路ではない。

### 認可の意味を取り違えないための固定ソース観察

- `Session.create`は入力Metadataや親Metadata・permissionsを利用し`SessionEvent.Created`を発行する。`SessionTransfer.import`もCreatedを発行する。イベントだけで「承認済みRoot」を識別できない。
- Session ProtocolとHandlerはSession作成時／更新時のMetadataやPermissionsの指定を受け取る。**それらの値はCallerに由来し得る**ため、Root発行専用の主体証明として使えない。
- Plugin Hostは`session.create`をCoreへ直接委譲する。Pluginの存在だけでは独立したRoot発行者を証明できない。
- MCP Toolの`permission.assert`は通常Toolの実行可否であり、`_meta`に入るSession IDは発行権限用の非偽装証票としては扱えない。
- Server Authorizationには、構成により認証不要となる分岐、および認証が必要な場合の一般的なリクエスト認証がある。**一般Server認証とRoot専用発行認可は別**。Ticketed接続等の例外が見えても、それ自体がRoot認可の迂回を実証するわけではない。
- Shell Toolは解析・許可の後に起動するが、それがRoot発行の全Host経路を遮断する仕組みであるとは確認できない。

## 5. 反例候補と成立条件

| ID | 判定 | 前提・証拠／留保 |
|---|---|---|
| CE-01: `SessionEvent.Created`だけでRootを許可 | **`STATIC_COUNTEREXAMPLE`（その認可案に限定）** | CreateとImportの双方がCreatedを発行する固定ソース。イベントの発生自体にRoot発行Caller証明が付随することは確認できない。実在ChronosSkills実装の脆弱性とは言わない |
| CE-02: Caller提供Metadataの`trusted`をRoot証明に利用 | **`STATIC_COUNTEREXAMPLE`（その認可案に限定）** | Create/Update・Plugin HostにはMetadata入力経路。**追加の真正性検証を行わず利用する**という条件でのみ成立。暗号的検証付きの別設計の不可能性は示さない |
| CE-03: 通常MCPアクセスだけで新Rootを発行 | `HYPOTHESIS / NOT_VERIFIED` | 当該発行ツールを公開し、発行者認可を追加しないという未提示実装が必要 |
| CE-04: BのShellからHost API/IPCに到達 | `HYPOTHESIS / NOT_VERIFIED` | 実効Hostアクセスと資格情報／公開APIの証拠が必要 |
| CE-05: CがRoot発行の内部権限を共有 | `HYPOTHESIS / NOT_VERIFIED` | Host同等権限と認可素材への到達の証拠が必要。単なるESM importの存在では成立を断言しない |

## 6. STOPと残存する証拠ギャップ

**適用したSTOP条件：**

1. **F-01**：ChronosSkills Root発行の実体・認可点・全入口を示す固定実装が未提供。既存OpenCodeのSession経路と混同して完全仲介を証明してはいけない（計画§2 F-01、§5.2-2/5）。
2. **F-02**：Root発行の権限発行元、強制境界、検証点、認可素材の非共有を示す静的なHost／OS構成資料が未提供（計画§2 F-02、§5.2-3）。
3. **F-05**：BのShell等がC相当の実効アクセスを持たないこと、全API/IPC/Module経路が保護されることを示す材料が未提供。環境未確定のためCを除外できない（計画§2 F-05、§5.2-3/5）。

**ここで停止する。** 未調査入口が存在しないと推定したり、追加の認可アーキテクチャを設計して「解決済み」としたり、Host検証へ自動移行しない。Q9-R2の正式独立レビューに格上げもしない。

### Owner判断に必要な不足証拠（新しい設計要求ではなく提出項目）

- **E-01（発行効果）**：Root Task Contextの新規発行を起こす主体・インターフェース・処理・永続化効果を示す、固定した仕様またはソース。存在しない場合は「未実装」と明示して、実装済みの仲介証明を求めない。
- **E-02（認可由来）**：Caller由来でないRoot発行者資格の発行主体・保持場所・受信側での検証、Context利用/委譲との区別の静的な契約資料。
- **E-03（環境境界）**：OpenCode Host、Agent Shell、MCP Server、Pluginの実効OS主体、アクセス可能なポート/IPC/ファイル/Moduleと、認証素材共有の有無を示す**非秘密の構成定義**。実機の能力測定は別許可。
- **E-04（完全仲介）**：E-01〜03を基にした全入口を閉じた呼出経路・認可・拒否表。現時点の限定調査表を完全性証明に代用しない。

## 7. Human Ownerへの判断返却

**推奨：`STOP / HOLD_CONTINUE / ADDITIONAL_EVIDENCE_ONLY_IF_SEPARATELY_AUTHORIZED`。**

選択肢は次のとおり。いずれも自動実行しない。

- **A（推奨）**：今回の限定調査を`NOT_VERIFIED`で閉じ、E-01〜E-03の静的証拠の所在と取得可能性だけを次の独立した判断対象とする。証拠がない限りHOLDを維持する。
- **B**：追加の調査を許可せずHOLDを継続する。
- **C**：別途スコープ・脅威モデル・設計方針の見直しをHuman Ownerが指示する（今回の調査では実施しない）。

Root発行の実現可能性未証明を理由としてD-02の自律生成要件を削除せず、検索専用をMVP完成扱いにしない。Q4〜Q7およびQ10の採択内容、R2設計書、採択済みPLAN R0は変更しない。

## 8. 最終状態と未実施事項

```
F01_NOT_VERIFIED
F02_NOT_VERIFIED
F05_NOT_VERIFIED
Q08_FEASIBILITY_NOT_VERIFIED
ROOT_AUTHORITY_NOT_ESTABLISHED
AUTONOMOUS_GENERATION_HOLD
IMPLEMENTATION_NOT_AUTHORIZED
GITHUB_WRITE_NOT_AUTHORIZED
NO_AUTO_R2
BOUNDED_STATIC_VERIFICATION_STOPPED
```

**実施していないこと：** テスト／ビルド、OpenCodeやHostのプロセス実行、MCPツール実行、資格情報参照、OS分離の実測、Q9-R2正式独立レビュー、代替認可方式の設計、ソース・設定・GitHub書き込み、正式Gate判断、MVP完了宣言。
