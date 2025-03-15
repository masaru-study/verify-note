+++
#テンプレート：ナレッジ
title = "VSCodeの設定紹介-だるま編-"
date = 2025-03-15T01:08:05+09:00
draft = false
#weight = 100 # 順番を意図的に変更したい場合は変更する
categories = ["Other"]
tags = ["Author:だるま", "Level:中級", "Type:Knowledge", "VSCODE"]
+++

## 概要

真似をして記載しました。今後追記する予定

## 目的

備忘のため、何が入っているか自分でもよくわからなくなってしまっていたので（笑）

## 拡張機能

```code
code --list-extensions
PS C:\vscode-test> code --list-extensions
bierner.markdown-mermaid
christian-kohler.path-intellisense
corschenzi.mermaid-graphical-editor
cweijan.vscode-typora
davidanson.vscode-markdownlint
eamodio.gitlens
formulahendry.auto-rename-tag
github.codespaces
github.copilot
github.copilot-chat
github.vscode-pull-request-github
goessner.mdmath
gxl.git-graph-3
hediet.vscode-drawio
hnw.vscode-auto-open-markdown-preview
jakeboone02.mediawiki
jakobhoeg.vscode-pokemon
jamiewoodio.cisco
jeblad-3.wmfsnippets
kevinrose.vsc-python-indent
marp-team.marp-vscode
mechatroner.rainbow-csv
meezilla.json
mhutchie.git-graph
mohsen1.prettify-json
ms-azuretools.vscode-docker
ms-ceintl.vscode-language-pack-ja
ms-edgedevtools.vscode-edge-devtools
ms-python.debugpy
ms-python.python
ms-python.vscode-pylance
ms-toolsai.jupyter
ms-toolsai.jupyter-keymap
ms-toolsai.jupyter-renderers
ms-toolsai.prompty
ms-toolsai.vscode-jupyter-cell-tags
ms-toolsai.vscode-jupyter-slideshow
ms-vscode-remote.remote-containers
ms-vscode-remote.remote-ssh
ms-vscode-remote.remote-ssh-edit
ms-vscode-remote.remote-wsl
ms-vscode.remote-explorer
ms-vsliveshare.vsliveshare
njpwerner.autodocstring
oderwat.indent-rainbow
redhat.vscode-yaml
rifi2k.format-html-in-php
saikou9901.evilinspector
shd101wyy.markdown-preview-enhanced
streetsidesoftware.code-spell-checker
taiyofujii.novel-writer
tmlo.nagios
tonybaloney.vscode-pets
unifiedjs.vscode-remark
visualstudioexptteam.intellicode-api-usage-examples
visualstudioexptteam.vscodeintellicode
xavierguarch.auto-markdown-toc
yzane.markdown-pdf
yzhang.markdown-all-in-one
zainchen.json
```

## settings.json

```settings.json
{
    "window.restoreWindows": "none",
    "window.openFoldersInNewWindow": "on",
    "security.workspace.trust.untrustedFiles": "open",
    "php-cs-fixer.executablePath": "${extensionPath}/php-cs-fixer.phar",
    "php-cs-fixer.lastDownload": 1709043198954,
    "editor.largeFileOptimizations": false,
    "[python]": {
        "editor.formatOnType": true
    },
    "python.defaultInterpreterPath": "G:\\Python\\Python310\\python.exe",
    "git.openRepositoryInParentFolders": "never",
    "editor.fontLigatures": false,
    "editor.fontSize": 16,
    "workbench.editor.enablePreview": false,
    "hediet.vscode-drawio.resizeImages": null,
    "markdown-pdf.mermaidServer": "https://unpkg.com/mermaid@10.4.0/dist/mermaid.min.js",
    "remote.SSH.remotePlatform": {
        "XXX.XXX.XXX.XX": "linux"
    },
    "git.autofetch": true,
    "hediet.vscode-drawio.theme": "dark",
    "redhat.telemetry.enabled": true
}
```
