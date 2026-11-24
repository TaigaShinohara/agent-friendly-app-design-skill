# Agent-Friendly App Design Skill

自作アプリを AI エージェントが GUI を叩かずに確実に操作・検証できるようにするための、AI エージェント用スキルです。

「サービスは CLI 経由を第一経路にする」という運用の考え方を、自作アプリ側の設計にも広げるための正本です。GUI と同じ芯（サービス層）を CLI / JSON / MCP にも開くことで、AI が「読み取れる、操作できる、結果を確認できる、失敗しても壊れない」状態を作ります。

## 特徴

- **到達レベル L0〜L4**：GUI のみ（L0）から自律運用（L4）まで。既定の目標は L2（操作可能）で、全アプリを最初から最高レベルにしない
- **操作カタログが正本**：全操作を棚卸しし、機械経路か人間専用かを先に決めてから実装する
- **CLI 契約**：サブコマンド構成、`--help`、共通フラグ（`--json` / `--dry-run` / `--yes` / `--idempotency-key`）、統一した終了コードと JSON の成功・失敗形式、安定 ID
- **検証可能性**：ドライラン、`verify`、テスト用シードとリセット、決定的モード、履歴による結果回収
- **MCP 層**：CLI の上に被せる AI 専用の玄関。コマンド＝ツール、読み取り＝リソース、スキーマは CLI 側を単一の正本にして二重管理しない。L4 の条件の 1 つ
- **SDK 層は作らないのが既定**：作る条件を 3 つに限定し、作る場合も主言語 1 つだけ
- **GUI しかない機能の正直な仕分け**：機械化できる（A）／画面まで案内（B）／人間専用（C）。権限ダイアログや身体感覚の判定は無理に自動化しない
- **後付け手順**：既存アプリは読み取り（`status` → `list` / `get` → `history` / `doctor`）から段階的に

## 構成

| ファイル | 内容 |
| --- | --- |
| `skills/agent_friendly_app_design/SKILL.md` | スキル本体（核、到達レベル、CLI 契約、MCP 層、SDK 層、進め方） |
| `skills/agent_friendly_app_design/references/checklist.md` | 監査〜実装のチェックリストと `agent-manifest.json` の様式 |
| `skills/agent_friendly_app_design/references/platform-patterns.md` | Android / Flutter / Web・Space / スクリプト別の実現方法と MCP の置き方 |
| `skills/agent_friendly_app_design/references/kikkake-worked-example.md` | 実アプリへの当てはめ例（監査と段階計画） |
| `skills/agent_friendly_app_design/templates/agent-guide-template.md` | アプリに同梱する AI 向けガイド（AGENT_GUIDE）の雛形 |
| `skills/agent_friendly_app_design/skill.yaml` | Muse 用スキル定義 |
| `skills/agent_friendly_app_design/agents/openai.yaml` | Codex 用インターフェース定義 |

## 使い方

スキル対応の AI エージェント（Claude / Codex 等）に `skills/agent_friendly_app_design/` を読み込ませ、「CLI対応にして」「AIが使えるようにして」「MCP対応にして」などと依頼するだけです。新規アプリの設計段階でも、既存アプリへの後付け監査でも使えます。

## 実証

Android アプリ「きっかけ」（TaigaShinohara/kikkake、自動化アプリ）へ実際に当てはめ、現状レベル判定（L0）と弱点の特定（ID の振り直し、履歴の非構造化、結果の Toast 依存）、L1→L2→L3→MCP 橋渡しの段階計画までを作成して検証しています。詳細は `references/kikkake-worked-example.md` を参照してください。

## License

MIT License
