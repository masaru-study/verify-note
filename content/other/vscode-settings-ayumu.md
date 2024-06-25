+++
#テンプレート：ナレッジ
title = "VSCodeの設定紹介-あゆむ編-"
date = 2024-06-22T01:08:05+09:00
draft = false
#weight = 99 # 順番を意図的に変更したい場合は変更する
categories = ["Other"]
tags = ["Author:あゆむ", "Level:中級", "Type:Knowledge", "VSCODE"]
+++

- [概要](#概要)
- [構成図](#構成図)
- [拡張機能](#拡張機能)
  - [Windows/Linux 両方にインストール](#windowslinux-両方にインストール)
  - [Windows 側のみインストール](#windows-側のみインストール)
- [settings.json](#settingsjson)

## 概要

インフラエンジニアでも利用するケースが多くなってきた VS Code の設定を紹介します。

## 構成図

{{< figure src="/img/vscode-settings-ayumu/fig-diagram.drawio.svg" title="fig-diagram" class="center" width="100%" height="100%" >}}

## 拡張機能

### Windows/Linux 両方にインストール

```bash
code --list-extensions

# 設定系
alefragnani.project-manager
editorconfig.editorconfig
gruntfuggly.todo-tree
ms-ceintl.vscode-language-pack-ja
spencerwmiles.vscode-task-buttons
vscode-icons-team.vscode-icons

# 全言語共通系
esbenp.prettier-vscode
formulahendry.auto-close-tag
formulahendry.auto-rename-tag
formulahendry.code-runner
ionutvmi.path-autocomplete
oderwat.indent-rainbow
usernamehw.errorlens
streetsidesoftware.code-spell-checker
jgclark.vscode-todo-highlight

# ユーティリティ系
amazonwebservices.aws-toolkit-vscode
aykutsarac.jsoncrack-vscode
corschenzi.mermaid-graphical-editor
grapecity.gc-excelviewer
hediet.vscode-drawio
github.vscode-github-actions
github.vscode-pull-request-github
mhutchie.git-graph
tyriar.luna-paint

# bash
foxundermoon.shell-format
timonwong.shellcheck
rogalmic.bash-debug

# python
ms-python.black-formatter
ms-python.debugpy
ms-python.python
ms-python.vscode-pylance

# PowerShell
ms-vscode.powershell

# Sphinx
swyddfa.esbonio
trond-snekvik.simple-rst
lextudio.restructuredtext

# Markdown
yzhang.markdown-all-in-one
zaaack.markdown-editor
shd101wyy.markdown-preview-enhanced
pkief.markdown-checkbox

# Terraform
hashicorp.terraform

# CloudFormation
kddejong.vscode-cfn-lint
dannysteenman.cloudformation-yaml-snippets

# その他
hrst.yamaha-rt
jamiewoodio.cisco
redhat.vscode-yaml
remcohaszing.schemastore
```

### Windows 側のみインストール

```bash
code --list-extensions

# Remote SSH拡張機能３つ
ms-vscode-remote.remote-ssh
ms-vscode-remote.remote-ssh-edit
ms-vscode.remote-explorer
```

## settings.json

```json
{
  "window.title": "${dirty}\\${activeEditorMedium}${separator}${rootName}",
  "workbench.colorTheme": "Default Light+", // VSCODEのテーマをDefault Light+にする。
  "files.insertFinalNewline": true, // ファイルの末尾を改行で終わらせる
  "files.encoding": "utf8", // 文字コード
  "files.eol": "auto", // 改行コード
  "files.autoGuessEncoding": true, // 文字コードの自動判別
  "editor.insertSpaces": true, // tabキー押したときのスペースにするか
  "editor.tabSize": 4, // tabキー押したときのスペース数
  "editor.autoIndent": "keep", // 改行時のインデント維持
  "editor.wordWrap": "on", // ページ折り返し
  "editor.snippetSuggestions": "top", // サジェスト時のスペニット表示場所
  "editor.fontFamily": "Firple,'ＭＳ ゴシック'", // フォント設定
  "editor.renderControlCharacters": true, // 制御文字を表示する
  "editor.suggestSelection": "first", // サジェスト一覧の初期表示項目 設定
  "editor.renderLineHighlight": "all", // 選択行の行番号をハイライトする
  "editor.cursorBlinking": "smooth", // カーソルが滑らかに点滅するように
  "editor.cursorStyle": "line", // エディターのカーソル種類
  "editor.cursorSmoothCaretAnimation": "on", // ファイルの自動エンコードを実施
  "editor.bracketPairColorization.enabled": true, // 括弧の対応を色付け る
  "editor.guides.bracketPairs": true, // 括弧を線でつなげる
  "editor.guides.bracketPairsHorizontal": "active", // 括弧を線でつなげる_横
  "editor.formatOnSave": true, // 保存時にフォーマット
  "editor.defaultFormatter": "esbenp.prettier-vscode", // フォーマッター
  "editor.quickSuggestions": {
    // サジェストの自動表示
    "strings": true,
    "other": true,
    "comments": false
  },
  "diffEditor.ignoreTrimWhitespace": false, // 差分エディタの先頭末尾空白無効化
  "workbench.iconTheme": "vscode-icons", //テーマアイコン設定
  "terminal.integrated.cursorStyle": "block", // ターミナルのカーソル 種類
  "breadcrumbs.enabled": true, // ファイルのパンくずリストを表示する
  "markdown.preview.scrollEditorWithPreview": false, // MDのプレビューをスクロール連動しない
  "code-runner.runInTerminal": true, // Code Runnerをターミナルで開く
  "VsCodeTaskButtons.showCounter": false, // タスクボタン数の表示を無効化
  "aws.telemetry": false, // AWS Toolkitの情報収集を無効化
  "redhat.telemetry.enabled": false, // redhatの情報収集を無効化
  "yaml.format.enable": true, // yamlのフォーマットを有効化
  "git.autofetch": true, // 自動でgit fetchする
  "markdown.extension.toc.updateOnSave": true, //MD保存時にTOCを更新する
  "markdown.extension.toc.levels": "2..4", //MDのTOC作成段落を２～４にする
  "markdown-preview-enhanced.previewTheme": "github-light.css", // MD EnhancedプレビューのテーマをGithub Lightにする
  "markdown-preview-enhanced.scrollSync": false, // MD Enhanced プレビューをスクロール連動しない
  "markdown-checkbox.withBulletPoint": true, // MDチェックボックスの前を箇条書きにする
  "markdown-checkbox.typeOfBulletPoint": "+", // MDチェックボックスの前の箇条書きを＋にする
  "markdown-checkbox.strikeThroughWhenChecked": true, // MDチェックボックスがオンなら取り消し線にする
  "remote.SSH.remotePlatform": {
    // SSHリモートデバッグのOS
    "VSCODE-SSH": "linux"
  },
  "workbench.editorAssociations": {
    // エディタの選択
    "*.png": "luna.editor",
    "*.bmp": "luna.editor",
    "*.jpg": "luna.editor",
    "*.jpeg": "luna.editor",
    "*.ico": "luna.editor"
  },
  "code-runner.executorMap": {
    // Code Runnerのインタープリタ
    "python": "python -u",
    "powershell": "pwsh -File",
    "bat": "cmd /c",
    "shellscript": "bash"
  },
  "[python]": {
    // Pythonの固有設定
    "editor.defaultFormatter": "ms-python.black-formatter" // フォーマッター
  },
  "[shellscript]": {
    // シェルスクリプトの固有設定
    "editor.defaultFormatter": "foxundermoon.shell-format" // フォーマッター
  },
  "[dockerfile]": {
    // Dockerfileの固有設定
    "editor.defaultFormatter": "foxundermoon.shell-format" // フォーマッター
  },
  "[properties]": {
    // .propertiesファイルの固有設定
    "editor.defaultFormatter": "foxundermoon.shell-format" // フォーマッター
  },
  "[host]": {
    // hostsファイルの固有設定
    "editor.defaultFormatter": "foxundermoon.shell-format" // フォーマッター
  },
  "[powershell]": {
    // PowerShellの固有設定
    "editor.defaultFormatter": "ms-vscode.powershell", // フォーマッター
    "files.encoding": "utf8bom", // 文字コード
    "files.eol": "\r\n" // 改行コード
  },
  "[bat]": {
    // batファイルの固有設定
    "files.encoding": "shiftjis", // 文字コード
    "files.eol": "\r\n" // 改行コード
  },
  "[dockercompose]": {
    // dockercomposeファイルの固有設定
    "editor.tabSize": 2 // tabキー押したときのスペース数
  },
  "[yaml]": {
    // yamlファイルの固有設定
    "editor.tabSize": 2 // tabキー押したときのスペース数
  },
  "[json][jsonc]": {
    // jsonファイルの固有設定
    "editor.defaultFormatter": "vscode.json-language-features",
    "editor.tabSize": 2 // tabキー押したときのスペース数
  },
  "[markdown]": {
    // Markdownの固有設定
    "editor.tabSize": 2, // tabキー押したときのスペース数
    "editor.wordSeparators": "./\\()\"'-:,.;<>~!@# ○$%^&*|+=[]{}`~?．。，、（）「」［］｛｝《》てにをはがのともへでや"
  },
  "[terraform]": {
    // Terraformの固有設定
    "editor.defaultFormatter": "hashicorp.terraform"
  },
  "[terraform-vars]": {
    // Terraform-varsの固有設定
    "editor.defaultFormatter": "hashicorp.terraform"
  },
  "yaml.customTags": [
    // CloudFormation用タグ
    "!Ref",
    "!Sub scalar",
    "!Sub sequence",
    "!Join sequence",
    "!FindInMap sequence",
    "!GetAtt scalar",
    "!GetAtt sequence",
    "!Base64 mapping",
    "!GetAZs",
    "!Select scalar",
    "!Select sequence",
    "!Split sequence",
    "!ImportValue",
    "!Condition",
    "!Equals sequence",
    "!And",
    "!If",
    "!Not",
    "!Or",
    "!And sequence",
    "!If sequence",
    "!Not sequence",
    "!Equals",
    "!Or sequence",
    "!FindInMap",
    "!Base64",
    "!Join",
    "!Cidr",
    "!Sub",
    "!GetAtt",
    "!ImportValue sequence",
    "!Select",
    "!Split"
  ],
  "json.schemas": [
    {
      // IAMポリシー
      "fileMatch": ["*.iam.json"],
      "url": "https://gist.githubusercontent.com/jstewmon/ee5d4b7ec0d8d60cbc303cb515272f8a/raw/4e381019a7804b2cc186699ff171f25b06f06542/aws-iam-poilcy-schema.json"
    }
  ]
}
```
