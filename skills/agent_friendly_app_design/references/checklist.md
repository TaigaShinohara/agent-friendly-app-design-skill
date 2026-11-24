# Agent-Friendly チェックリスト

監査と実装の両方で使う。項目ごとに 済 / 未 / 対象外 を記録し、対象外には理由を 1 行書く。

## 1. 監査（現状把握）

- [ ] データの保存場所と形式が特定できている（ファイル、DB、SharedPreferences、localStorage、サーバー）
- [ ] 業務ロジックが UI から分離されているか、混在箇所が特定できている
- [ ] 外部から触れる経路の有無（CLI、API、ディープリンク、Intent、ファイル受け渡し）
- [ ] ID の安定性（再起動、読み込み、並び替えで変わらないか）
- [ ] 実行履歴と失敗理由が残っているか
- [ ] テスト基盤とスクリーンショット取得経路の有無
- [ ] 現在の到達レベル（L0 から L4）の判定と根拠

## 2. 操作カタログ

- [ ] 主要操作が全件リスト化されている
- [ ] 各操作に機械経路か人間専用の宣言がある
- [ ] 破壊的操作に印が付いている
- [ ] 副作用（通知、外部送信、課金、ファイル変更）が書かれている

## 3. CLI / API 契約

- [ ] 主要操作にサブコマンドがある
- [ ] 全コマンドに `--help` があり例が載っている
- [ ] 共通フラグ `--json` `--dry-run` `--yes` `--idempotency-key` `--timeout` の対応状況が明示されている
- [ ] 終了コードが統一されている（0 成功、1 入力誤り、2 アプリ側失敗、3 権限不足、4 対象なし）
- [ ] 破壊的操作は `--yes` なしで実行されない
- [ ] 冪等キーで再実行しても二重に作られない
- [ ] 長い処理はジョブ ID か進捗を返し `status` で追える

## 4. 入出力

- [ ] 成功と失敗の JSON の包みが統一されている
- [ ] エラーコードが文字列定数で安定している
- [ ] ID が安定し再利用されない
- [ ] 日時が ISO 8601 とタイムゾーン付きである
- [ ] スキーマにバージョンがある
- [ ] 秘密情報の値が出力に含まれない

## 5. 読み取りと検証

- [ ] `status` でアプリ情報、版数、前提条件が分かる
- [ ] `list --json` と `get <id> --json` がある
- [ ] `history --json` で成功と失敗理由が見られる
- [ ] `doctor` で権限不足と未設定が一覧になる
- [ ] 書き込み系に `--dry-run` がある
- [ ] `verify` が合格か不合格かと理由を返す
- [ ] テスト用の `seed` と `reset --test-data` に本番ガードがある
- [ ] 決定的モード（時刻、シード、応答の固定）がある
- [ ] 画面検証の経路がある（スクリーンショットテスト、golden、`data-testid`）

## 6. ドキュメント

- [ ] `docs/AGENT_GUIDE.md` がある
- [ ] `docs/agent-manifest.json` がある
- [ ] ガイドと実装のコマンド一覧が一致している
- [ ] 人間専用操作の一覧と理由がガイドにある

## 7. MCP / SDK 層

- [ ] MCP 対応の要否が決まっている（AI が日常的に触るアプリだけ対応する）
- [ ] コマンドとツールの対応表があり、ツール名がコマンド名と一致している
- [ ] ツール定義が CLI のスキーマ正本から生成か変換されている（二重管理になっていない）
- [ ] 読み取りがリソース、書き込みと実行がツールに分かれている
- [ ] 破壊的ツールにドライラン相当の引数があり、既定では実行されない
- [ ] MCP サーバーの認証と公開範囲が決まっている
- [ ] 許可ツールが列挙され、未確認の追加ツールは使えない扱いになっている
- [ ] MCP があっても CLI が残っている
- [ ] SDK の要否が判断されている（既定は作らない。作る場合は主言語 1 つだけ）

## agent-manifest.json の様式

```json
{
  "app": "<app-name>",
  "app_version": "0.0.0",
  "schema_version": 1,
  "agent_level": 2,
  "guide": "docs/AGENT_GUIDE.md",
  "commands": [
    {"name": "status", "kind": "read", "json": true},
    {"name": "list", "kind": "read", "json": true},
    {"name": "create", "kind": "write", "json": true, "dry_run": true, "idempotent": true}
  ],
  "human_only": [
    {"operation": "os-permission-grant", "reason": "OS dialog requires the user"}
  ],
  "mcp": {
    "available": false,
    "transport": null,
    "endpoint": null,
    "tools_generated_from": "cli-schema"
  },
  "sdk": {
    "available": false,
    "language": null
  }
}
```
