---
title: "2020年 Javaベースのバッチを作るなら"
date: 2020-06-25T12:00:00+09:00
draft: false
tags:
  - java
---
前々回位の現場で、Javaベースのバッチ処理を作ることがあって。 **「オンプレ環境で、そこまで厳格な条件ではないけど、ある程度安定して動いて欲しい」** **「DBに接続して、データを取得後加工、CSVに出力して、それをメールに添付して送付」** 位の要件でしたね。[^1]

そこでは、アーキテクチャを選択できる立場になかったので、以前からあるバッチの仕組みを、ちょろっといじってサーバに置いたんですが。[^2] でも、今、自分が作るとしたら、どんな仕組みにするかなぁ、って、ちょっと考えてみました。

* ある程度ポータブルなものにしたいよね
  * オンプレとは言え、不定期にサーバーの統廃合があったりする
  * サーバーの統廃合時に、cronタブの設定忘れたり
  * サーバーごとに **秘伝のタレ** の設定/シェルがあったり
* ある程度複雑なロジックを伴うから、TDDとかやりたいよね
  * ちょっとした変更だって、やっぱり確認しながらやりたい
* デプロイ時に漏れとか防ぎたいよね
* ソース管理もGitとかのVCSでやりたいよね
  * たまーに機能追加しようとすると、もういない担当者のPCの中にしかソースが…

…あれ? 結構条件ありますね。 

今、自分で作るとするなら、こんな感じかな

* ある程度枯れた、よく使われる軽量ライブラリの組合せで作る
  * GraalVMとか、まだ人類には早すぎるのでは…
  * [Jdbi](https://jdbi.org/ "Jdbi 3 Developer Guide") のような軽量OR/M
* 変数を、環境変数や実行時変数から注入できる仕組みを組み込む
  * VCSで管理する以上、DB接続情報、メールサーバー接続情報等々をコミットするのは、アンチパターンでしょう
  * [Typesafe Config](https://github.com/lightbend/config "lightbend/config: configuration library for JVM languages using HOCON files")
  * [MicroProfile Configuration](https://github.com/eclipse/microprofile-config "eclipse/microprofile-config: MicroProfile Configuration Feature")
* ビジネスロジックをTDDで作るために、モックとか差し替えしたい
  * 軽量DIコンテナを採用する
  * [Dagger](https://github.com/google/dagger "google/dagger: A fast dependency injector for Android and Java.")
* Maven/Gradleで、依存関係やバージョン情報を明示
* 実行可能なFatJarを作成して `java --jar batch.jar` だけで実行可能
* 実行は、cronではなく、バッチ実行の仕組みを別に用意する
  * [Rundeck](https://docs.rundeck.com/docs/ "Rundeck Documentation | Rundeck Docs")
* そんなに頻繁な更新でなくとも、CI/CDの仕組みに乗せる

意外とやらないといけないこと多いですわ。

* 参考 [Serverless時代のJavaについて](https://www.slideshare.net/AmazonWebServicesJapan/serverlessjava-199195000 "Serverless時代のJavaについて")

[^1]: あるある案件
[^2]: 惰性とも言う
