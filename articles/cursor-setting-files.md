---
title: "Cursorの設定ファイルまとめ"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cursor"]
published: true
---

# はじめに

Cursorをより効率的に使っていきたいと考え、設定ファイルに関する情報をざっくりとまとめました。
Cursorの使い方に関する記事はたくさんありますが、設定ファイルごとにまとめた方が分かりやすいと感じたため、本記事を作成しました。
詳しい内容については、ファイルごとにCursorの公式ドキュメントへのリンクを配置したので、そちらをご参照ください。
なお、`AGENTS.md`と`BUGBOT.md`については割愛します。

:::message
この記事は2026年10月時点での情報を基に更新しています（初版は2026年6月）。
また、見落としや間違いがあるかもしれないので、その際はご了承ください。
:::

# .cursorignore

CursorのAIがアクセスできるディレクトリやファイルを制御するものです。
以下のような機能からのファイルアクセスをブロックすることができます。

- Tab、Agent、Inline Edit
- @によるメンション

また、Cursorのコード検索で使用されるInstant Grepでも除外対象として扱われます。
`.gitignore`に記載されたファイルも、基本的に除外対象になります。

ただし注意点として、**TerminalやMCPツール経由だと、`.cursorignore`に記載したファイルへのアクセスをブロックすることはできません。**

https://cursor.com/ja/docs/reference/ignore-file
https://cursor.com/docs/agent/tools/search

# .cursor/settings.json

公式ドキュメントにこのファイルの詳細な仕様は見つかりませんでしたが、Cursorのプラグインを導入すると、このファイルに導入したプラグインに関する設定が書き込まれることがあります。
例えばFigmaのプラグインを導入すると、以下のような形で記載されます。

```json:settings.json
{
  "plugins": {
    "figma": {
      "enabled": true
    }
  }
}
```

プラグインの作成方法については、以下のURLに記載されています。

https://cursor.com/ja/docs/plugins

# .cursor/mcp.json

MCPを設定するためのファイルです。
プロジェクトごとの`.cursor/mcp.json`に加えて、ユーザー全体に適用される`~/.cursor/mcp.json`も利用できます。

https://cursor.com/ja/docs/mcp

# .cursor/sandbox.json

Agentがサンドボックス内で実行するコマンドについて、ネットワークアクセスやファイルシステムへのアクセスなどを制御することができます。
ネットワークアクセスでは、ドメイン名などを指定して制御することができます。
ファイルシステムでは、読み取り専用の範囲や書き込み可能なパスなどを設定できます。

ユーザー全体に適用する`~/.cursor/sandbox.json`も用意されています。

なお、`.cursorignore`と併用することはできますが、**これらの設定だけで`.env`などの秘密情報へのあらゆるアクセスを防げるわけではありません。**

https://cursor.com/ja/docs/reference/sandbox

# .cursor/hooks.json

hooksは、任意のタイミングでシェルスクリプトやプロンプトなどを実行することができる機能です。
活用例としては、編集後にフォーマッターを実行させたり、シェルスクリプト実行前にAIに安全かどうか確認させたりすることができます。
Agentの処理だけでなく、Tabやワークスペースのイベントなどにも対応しています。

実行タイミングや内容については、以下のリンクから詳細を確認できます。

https://cursor.com/ja/docs/hooks

# .cursor/environment.json

Cloud Agentの環境を設定するためのファイルです。
Dockerfileを指定したり、依存関係をインストールするスクリプトを記述したりします。

Cloud Agentの環境はダッシュボードから設定することもできます。
保存済みの環境設定が優先される場合があり、必ずしも`.cursor/environment.json`が読み込まれるわけではありません。

https://cursor.com/ja/docs/cloud-agent/setup

# .cursor/worktrees.json

git worktreeを新しく作成する時に実行するコマンドを記述するためのファイルです。
`npm ci`の実行や、`cp`で`.env`をコピーするなど、worktree作成後のセットアップを自動化できます。
OSごとに異なるコマンドを指定することも可能です。

なお、worktreeの最大保持数はこのファイルではなく、エディタ設定の`cursor.worktreeMaxCount`で指定します。

https://cursor.com/ja/docs/configuration/worktrees

# .cursor/permissions.json

任意のMCPやTerminalコマンドを、ユーザーの承認なしで実行させるかどうかを制御するためのファイルです。
`mcpAllowlist`や`terminalAllowlist`で許可するツールやコマンドを指定できます。

Auto-review modeを有効にすることによって、Shell、MCP、Fetchのツール呼び出しを許可するかどうかをLLM分類器によって判定させます。
Auto-review modeでは、このファイルの`autoRun`オブジェクト内に判定用の指示を記述します。

**Auto-review modeはセキュリティ境界ではありません。**

https://cursor.com/ja/docs/reference/permissions

# ~/.cursor/cli-config.json & .cursor/cli.json

Cursor CLIに関する設定を記述するためのファイルです。
`~/.cursor/cli-config.json`はグローバル設定で、vimModeやパーミッション（`rm`などのコマンド許可）などを記述します。
一方、プロジェクトごとの`.cursor/cli.json`で設定できるのはパーミッションのみです。

https://cursor.com/ja/docs/cli/reference/configuration

# .cursor/rules/**

プロジェクトのルールを`.mdc`で記述します。
`.mdc`は通常のMarkdownとは異なり、フロントマターにルールの適用条件を記述できます。
**`.md`にすると無視されるので注意してください。**

https://cursor.com/ja/docs/rules

# .cursor/skills/** & .agents/skills/** & .claude/skills/** & .codex/skills/**

エージェントスキルを配置するためのディレクトリです。
各スキルは`SKILL.md`を含むディレクトリとして作成し、必要に応じてスクリプトや参考資料なども配置できます。

https://cursor.com/ja/docs/skills

# .cursor/agents/** & .claude/agents/** & .codex/agents/**

カスタムサブエージェントを記述するためのディレクトリです。
サブエージェントを用いることで、メインのエージェントから特定のタスクを別のエージェントに委任できるようになります。

https://cursor.com/ja/docs/subagents

# .cursor/commands/**

実行させたいプロンプトを定型化して保存するためのディレクトリです。
`.md`ファイルを配置し、Agentの入力欄で`/`から呼び出すことができます。

※ 現行の公式ドキュメントでは独立した説明ページが見つけにくいため、公式の変更履歴を掲載しています。

https://cursor.com/ja/changelog/1-6

# .cursor/config/bugbot.yaml

BugbotによるGitHubのPRレビューの動作を、リポジトリ単位で設定するためのファイルです。
レビューの実行タイミングや詳細度、PRの要約、Autofixの動作などを設定できます。
ただし、このファイルからAutofixを新たに有効化することはできません。

`BUGBOT.md`がレビュー時の指示を記述するものであるのに対し、こちらはBugbotの動作を設定するものです。

https://cursor.com/docs/bugbot

# .cursor-plugin/plugin.json

Cursorプラグインを開発・配布する際に使用するマニフェストファイルです。
プラグインの情報や、ルール・スキル・コマンドなどの構成要素を定義します。
通常のプロジェクト設定というより、プラグインの作成者向けのファイルです。

なお、Agent Plugins形式では、プラグインのルートにある`plugin.json`を使用します。

https://cursor.com/ja/docs/plugins

# おすすめ

プラグインやルールなどは、以下のようなリンクから探すことができます。

https://cursor.com/marketplace
https://cursor.directory/

# 注意事項

## 間違えやすいもの

### .vscode/settings.jsonと.cursor/settings.json

`.vscode/settings.json`はエディタの設定で、`.cursor/settings.json`はプラグインに関する設定が記録されることがあるファイルです。

### .vscode/extensions.jsonと.cursor/extensions.json

`.vscode/extensions.json`は推奨拡張機能を指定するもので、`.cursor/extensions.json`は公式ドキュメントに記載されていません。

## 存在しないもの

以下に記載されているものは、少なくとも公式ドキュメントには書かれていません。

- `project-rules.json`
- `.cursor/automations/**`

※ CursorのAutomations機能自体は存在しますが、上記のディレクトリを使って設定するという公式の記載は見つかりませんでした。

## 古いもの

`.cursorrules`は`.cursor/rules/**`への移行が推奨されています。

`.cursorindexingignore`は、従来のセマンティック検索用インデックスからファイルを除外するためのものでした。
しかし、Cursorのコード検索はInstant Grepへ移行しており、現在は従来のような「AIから参照はできるが、インデックスの対象からは除外する」という用途には使えません。

https://forum.cursor.com/t/how-to-reindexing-my-codebase/170407/8

# おわりに

この記事を書いて、まだまだCursorを使いこなせていないなと感じました。
しっかりと使いこなせるようになりたいです。
