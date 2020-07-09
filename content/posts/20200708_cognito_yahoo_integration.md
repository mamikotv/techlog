---
title: "2020年7月 Amazon Cognito経由でYahoo!Japanのユーザ認証をやりたかっただけだった。"
date: 2020-07-08T00:00:00+09:00
draft: false
tags:
  - aws
---

### TL;DR

> **できません**

> Amazon Cognitoの外部フェデレーテッドIDプロバイダとして、Yahoo!Japanを登録できますが、認証が通りません。 
> stateプロパティの値が長過ぎて、Yahoo!Japanからエラーで返ってきます。

___

* [Amazon Cognito とは - Amazon Cognito](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/what-is-amazon-cognito.html "Amazon Cognito とは - Amazon Cognito")

AWSって、以前、S3をちょっと触ったレベルだったので、全く浦島太郎状態だったんですね。 最新技術のキャッチアップを兼ねて、ちょっと小さいプロジェクトで試すかな。 それなら、初っ端に必要になるログインとか認証である、Amazon Cognitoから触ってみるか～、って触ってみたんです。

「…なるほど!! 全く分からん!!」

* [[Amazon Cognito] Facebook / Google / Amazon だけじゃない！独自の認証システムも利用可能になりました！ | Developers.IO](https://dev.classmethod.jp/articles/amazon-cognito-developer-authenticated-identities/ "[Amazon Cognito] Facebook / Google / Amazon だけじゃない！独自の認証システムも利用可能になりました！ | Developers.IO")
* [【サーバーレスなユーザ管理基盤】Amazon Cognito ユーザープールにOpenID Connectを使ってLINEアカウントを連携させてみる | Developers.IO](https://dev.classmethod.jp/articles/cognito-userpool-openid-connect-line/ "【サーバーレスなユーザ管理基盤】Amazon Cognito ユーザープールにOpenID Connectを使ってLINEアカウントを連携させてみる | Developers.IO")
* [AWS CognitoにGoogleとYahooとLINEアカウントを連携させる - Qiita](https://qiita.com/poruruba/items/189945dc64edfe1f2464 "AWS CognitoにGoogleとYahooとLINEアカウントを連携させる - Qiita")

上記記事を参考に、ちまちまいじっていて、なんとなく分かった!! **「Amazon Cognitoってアカウント管理してくれて、外部プロバイダとの連携とも整合をとってくれて、一意なIDを払い出してくれる。」** **「Amazon Cognitoそれ自体は、OpenID Connect/OAuth2のプロバイダとして振る舞ってくれる」** なんですね!!

上記記事を参考に、試しにYahoo!JapanとのID連携やってみるかー、って思って、IDプロバイダの設定までは、サクサクできたんです。

でも、実際のログイン画面が出てきて、クリックすると、コールバックにエラーが返ってきます。 **"state is too long value"** って何???

Chromeのdev-toolで、リクエストとかを見ていると、確かにAmazon Cognitoが生成する[state](https://www.buildinsider.net/enterprise/openid/oauth20 "OAuth 2.0の代表的な利用パターンを仕様から理解しよう - Build Insider")って、1,200文字程度あります。 自分では、どっちが妥当か判断できないし、しゃーないので、Yahoo!デベロッパーネットワークのお問合せフォームから、質問投げちゃいましたよ…。

結局「stateの長さは1024文字までの仕様となっており、長さを伸ばすことができません。(大意)」という回答でしたね。

よくあるOpenID Connect/OAuth2のクライアントライブラリは、そこまで長いstateを生成しないのでしょう。 ですが、全世界で広く使われているAWSだと、そうもいかないのでしょうね…。

久々、[Yak Shaving](http://0xcc.net/blog/archives/000196.html "yak shaving で人生の問題の80%が説明できる問題 - bkブログ")で不毛な時間を過ごしちゃったよなー、でも、これやらないとダメなのが、我々の人生だよなー、と言う感想でしたね。