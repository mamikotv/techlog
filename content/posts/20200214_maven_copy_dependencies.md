---
title: "Mavenで依存関係あるライブラリをダウンロードしたいだけだった。"
date: 2020-02-13T00:00:00+09:00
draft: false
tags:
  - java
---

また、以前blogに書いていたのに、すっかり忘れてググってしまったので。

```sh
mvn dependency:copy-dependencies -DincludeScope=test
```

上記を実行すると、プロジェクト配下の `target/dependency` に、テストスコープのものも含めて、 `*.jar` ファイルがダウンロードされます。[^1]

[^1]: 書いている時点のmavenバージョン 3.6.3