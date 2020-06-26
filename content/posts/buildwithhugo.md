---
title: "Hugo/GitHubPagesでブログ再作成"
date: 2020-01-16T00:00:00+09:00
draft: false
tags:
  - etc
---
一瞬、LivedoorBlogで作った時期もあるんですけど、やっぱり技術ブログなので…。

* Markdownで書きたい
* シンタックスハイライトが欲しい
* ブログパーツとかいらない

やはり、上記3条件は必須ですかねぇ。

___

毎度忘れてしまうので、hugoの操作メモ

新規記事作成

```sh
hugo new posts/article.md
```

ローカルサーバーで確認

```sh
hugo server
```

記事書き出し

```sh
hugo -D
```

___

以下、参考にした記事

* [Hugo + GitHub Pages + 無料で洒落たブログを30分で作る - Qiita](https://qiita.com/yotsak83/items/017734d5f873f4f194d4 "Hugo + GitHub Pages + 無料で洒落たブログを30分で作る - Qiita")
* [【2018年版】Hugoとgithub pagesでブログ作る方法【Circle CIも回します】 - Qiita](https://qiita.com/ryoma-tokushige/items/eba3e6cd415e9755af87 "【2018年版】Hugoとgithub pagesでブログ作る方法【Circle CIも回します】 - Qiita")
* [Hugoのテーマ「Theer」を作成しました – qqhann](https://qqhann.dev/blog/theer-stroy/ "Hugoのテーマ「Theer」を作成しました – qqhann")
    * すっきり見やすいHugoテーマ。 ありがたく使ってます
