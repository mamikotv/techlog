---
title: "Windows10/VSCode/WSL2/Docker/Hugoでblogを書いています"
date: 2021-10-27T00:00:00+09:00
draft: false
tags:
  - etc
---
[bitter*smooth](https://bittersmooth.halflite.net/ "bitter*smooth") と言う音楽レビューサイトを作っています。

この環境、サーバーサイドはNetlify/Hugo、ローカルではVSCodeでMarkdownを書く、と言う環境で構築しているのですが、あれ？そもそもVSCodeを使うなら、WSL2/DockerDesktop/Remote-Containersでローカル確認もすれば良いんじゃない？と思って、やったらサクッと出来ました！

_____

- [Windows Terminalインストール](https://atmarkit.itmedia.co.jp/ait/articles/2104/01/news019.html "ついにWindows Terminalの設定がGUIで可能に　プレビュー版v1.7が公開【Windows Terminal完全マスター】：Windows 10 The Latest（1/2 ページ） - ＠IT")

- WSL2インストール
    - Windows Terminal を管理者権限で起動

```powershell
wsl --install
wsl --set-default-version 2
wsl --install -d Ubuntu
```

- [VSCodeインストール](https://azure.microsoft.com/ja-jp/products/visual-studio-code/ "Visual Studio Code – コード エディター | Microsoft Azure")

- [Windows に Docker Desktop をインストール](https://docs.docker.jp/docker-for-windows/install.html "Windows に Docker Desktop をインストール — Docker-docs-ja 19.03 ドキュメント")

_____

まぁ、ここら辺までは、Windows10で開発している人ならフツーに出来るんでないでしょうか？

で、プロジェクトのトップで、 `.devcontainer` ディレクトリを作り、その配下に、 `Dockerfile` `devcontainer.json` の2つのファイルを作ります。

- `.devcontainer/Dockerfile` [^1]

```docker
FROM klakegg/hugo:0.88.0-alpine
RUN apk update && apk --no-cache add git
```

- `.devcontainer/devcontainer.json` [^2]

```json
{
	"name": "bittersmooth",
	"build": {
		"dockerfile": "Dockerfile",
	},
	"extensions": [
		"mhutchie.git-graph"
	]
}
```

- [Visual Studio Code を使用して Docker コンテナーを開発環境として使用する - Learn | Microsoft Docs](https://docs.microsoft.com/ja-jp/learn/modules/use-docker-container-dev-env-vs-code/ "Visual Studio Code を使用して Docker コンテナーを開発環境として使用する - Learn | Microsoft Docs")

_____

簡単に `Hugo` の環境が手に入るのですが、コレひとつだけ落とし穴があって、 `hugo server -w` でライブローディングが効かないんですね。 まぁ、VSCodeだとMarkdownのプレビューも簡単ですし、最終確認だけ `hugo server -w` で確認すれば良いのかな？と言う感じでやっています。

[^1]: alpineベースのイメージだと、Gitすら入っていないので `apk --no-cache add git` が、必要なのです。
[^2]: 拡張機能、 [Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph "Git Graph - Visual Studio Marketplace") 位は欲しいですよね…。