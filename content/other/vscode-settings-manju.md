+++
#テンプレート：ナレッジ
title = "VSCODEの設定紹介-DD Erikson編-"
date = 2024-06-22T01:08:05+09:00
draft = false
#weight = 99 # 順番を意図的に変更したい場合は変更する
categories = ["Other"]
tags = ["Author:DD Erikson", "Level:中級", "Type:Knowledge", "VSCODE"]
+++

## 概要

真似をして記載しました。今後追記します。

## 拡張機能

```code
code --list-extensions
bierner.markdown-mermaid
bmewburn.vscode-intelephense-client
corschenzi.mermaid-graphical-editor
esbenp.prettier-vscode
goessner.mdmath
hediet.vscode-drawio
hnw.vscode-auto-open-markdown-preview
hrst.yamaha-rt
jakeboone02.mediawiki
jamiewoodio.cisco
jeblad-3.wmfsnippets
mechatroner.rainbow-csv
meezilla.json
mohsen1.prettify-json
ms-azuretools.vscode-docker
ms-ceintl.vscode-language-pack-ja
ms-python.debugpy
ms-python.python
ms-python.vscode-pylance
ms-toolsai.jupyter
ms-toolsai.jupyter-keymap
ms-toolsai.jupyter-renderers
ms-toolsai.vscode-jupyter-cell-tags
ms-toolsai.vscode-jupyter-slideshow
ms-vscode-remote.remote-containers
ms-vscode-remote.remote-ssh
ms-vscode-remote.remote-ssh-edit
ms-vscode-remote.remote-wsl
ms-vscode.remote-explorer
ms-vsliveshare.vsliveshare
redhat.vscode-yaml
rifi2k.format-html-in-php
shd101wyy.markdown-preview-enhanced
tmlo.nagios
tonybaloney.vscode-pets
unifiedjs.vscode-remark
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
        "10.50.249.95": "linux"
    },
    "git.autofetch": true,
    "hediet.vscode-drawio.theme": "dark",
    "redhat.telemetry.enabled": true
}
```