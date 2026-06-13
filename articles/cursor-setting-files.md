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
この記事は2026年6月時点での情報を基に執筆しています。
また、見落としや間違いがあるかもしれないので、その際はご了承ください。
:::

# .cursorignore

CursorのAIがアクセスできるディレクトリやファイルを制御するものです。
以下のような機能からのファイルアクセスをブロックすることができます。

- セマンティック検索
- Tab、Agent、Inline Edit
- @によるメンション

セマンティック検索は、自然言語クエリに対応するコードを取得できる機能で、コード全体にインデックスを貼り、AIの検索能力の向上を図ります。

https://cursor.com/ja/blog/semsearch

ただし注意点として、**TerminalやMCPツール経由だと、.cursorignoreに記載したファイルをブロックすることはできません。**

https://cursor.com/ja/docs/reference/ignore-file

# .cursorindexingignore

特定のディレクトリやファイルに対して、セマンティック検索のインデックス作成を除外するものです。
logファイルなど、メンションで呼び出す可能性はあるものの、インデックスを貼ってしまうと検索のノイズになってしまうようなファイルに有効であると考えられます。

https://cursor.com/ja/docs/reference/ignore-file

# .cursor/settings.json

公式ドキュメントにこのファイルに関する記載はありませんでしたが、Cursorのプラグインを導入すると、このファイルに導入したプラグインに関する設定が書き込まれます。
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

https://cursor.com/ja/docs/mcp

# .cursor/sandbox.json

ネットワークアクセスやファイルシステムパスなどを制御することができます。
設定でサンドボックスが有効になっている場合にのみ機能します。
ネットワークアクセスでは、ドメイン名などを指定して制御することができます。
ファイルシステムパスでは、単なるアクセス可or不可だけでなく、Read Onlyなど細かく制御することが可能です。
.envなどは、.cursorignoreと.cursor/sandbox.jsonを併用すると良いと考えられます。

https://cursor.com/ja/docs/reference/sandbox

# .cursor/hooks.json

hooksは、任意のタイミングでシェルスクリプトやプロンプトなどを実行することができる機能です。
活用例としては、編集後にフォーマッターを実行させたり、シェルスクリプト実行前にAIに安全かどうか確認させたりすることができます。
実行タイミングや内容については、以下のリンクから詳細を確認できます。

https://cursor.com/ja/docs/hooks

# .cursor/environment.json

Cloud Agentの環境を設定するためのファイルです。
Dockerfileを指定したり、マシンが起動した後に実行するスクリプトを記述したりします。

https://cursor.com/ja/docs/cloud-agent/setup

# .cursor/worktrees.json

git worktreeの設定を記述するためのファイルです。
新しいworktreeを作成する時に実行するコマンド（npm ciや、cpで.envをコピーするなど）を記述したり、worktreeの最大数を指定できたりします。

https://cursor.com/ja/docs/configuration/worktrees

# .cursor/permissions.json

任意のMCPやTerminalコマンドを、ユーザーの承認なしで実行させるかどうかを制御するためのファイルです。
Auto-review modeを有効にすることによって、Shell、MCP、Fetchのツール呼び出しを許可するかどうかをLLM分類器によって判定させます。
Auto-review modeでは、このファイルのautoRunオブジェクト内に判定用のプロンプトを記述します。
Auto-review modeはセキュリティ境界ではありません。

https://cursor.com/ja/docs/reference/permissions

# .cursor/cli-config.json & .cursor/cli.json

Cursor CLIに関する設定などを書きます。
vimModeやパーミッション（rmなどのコマンド許可）などを記述します。

https://cursor.com/ja/docs/cli/reference/configuration

# .cursor/rules/**

プロジェクトのルールを.mdcで記述します。
mdcはMarkdown for Cursorの略で、通常のMarkdownとは異なり、フロントマターにルールを適用するスコープを記述します。
**.mdにすると無視されるので注意してください。**

https://cursor.com/ja/docs/rules

# .cursor/skills/** & .agents/skills/**

エージェントスキルを配置するためのディレクトリです。

https://cursor.com/ja/docs/skills

# .cursor/agents/** & .claude/agents/** & .codex/agents/

カスタムサブエージェントを記述するためのディレクトリです。
サブエージェントを用いることで、メインのエージェントから特定のタスクを別のエージェントに委任できるようになります。

https://cursor.com/ja/docs/subagents

# .cursor/commands/**

実行させたいプロンプトを定型化して保存するためのディレクトリです。

※ 公式ドキュメントからは記述が消えていましたが、Cursor CLIにコマンドが存在し、実際に動作させることができたのでここに載せます。

# おすすめ

プラグインやルールなどは、以下のようなリンクから探すことができます。

https://cursor.com/marketplace
https://cursor.directory/

# 注意事項

## 間違えやすいもの

### .vscode/settings.jsonと.cursor/settings.json

.vscode/settings.jsonはエディタの設定で、.cursor/settings.jsonはプラグインの設定です。

### .vscode/extensions.jsonと.cursor/extensions.json

.vscode/extensions.jsonは推奨拡張機能を指定するもので、.cursor/extensions.jsonは存在しません。

## 存在しないもの

以下に記載されているものは少なくとも公式ドキュメントには書かれていません。

- project-rules.json
- automations.json
- .cursor/automations/**

## 古いもの

.cursorrulesは.cursor/rules/**への移行が推奨されています。

# おわりに

この記事を書いて、まだまだCursorを使いこなせていないなと感じました。
しっかりと使いこなせるようになりたいです。
