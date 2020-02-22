---
title: "Java NIO2 で一時ファイルを作成、処理後に削除"
date: 2020-02-22T00:00:00+09:00
draft: false
tags:
  - java
---
```java
  public void doSomething() {
    try {
      Path tempFilePath = Files.createTempFile("id", ".tmp");
      try (Writer writer = Files.newBufferedWriter(tempFilePath)) {
        // do something.
        // 終わった後に一時ファイルを削除したい
      }
    } catch (IOException e) {
      // ログとか出したり、または、別の例外再スロー
    }
  }
```

上記のような処理で、「一時ファイルを作成、処理後に削除」って、どうやるんだっけ、って、思い出すのに四苦八苦してしまったので…。[^1]

_____

```java
  public static Closeable deleteOnClose(final Path tempFilePath) {
    return new Closeable() {
      @Override
      public void close() throws IOException {
        Files.deleteIfExists(tempFilePath);
      }
    };
  }

  public void doSomething() {
    try {
      Path tempFilePath = Files.createTempFile("id", ".tmp");
      try (Closeable closeable = deleteOnClose(tempFilePath);
          Writer writer = Files.newBufferedWriter(tempFilePath)) {
        // do something.
      }
    } catch (IOException e) {
      // ...
    }
  }
```

以前やったのは、 `Closeable` の匿名クラス作って返すようなメソッドを一つ用意しておいて、 "try-with-resources" 構文の中で開放させる、というもの。 "try-with-resources" では、 `try` のカッコ内の最後の方からクローズしていくので、 `Closeable` を返すメソッドは一番最初に書くのが、注意するポイントでしょうか。

`Closeable` の実装は自分でやることになるので、 Java SE 7 以前の `java.io.File.createTempFile` メソッドで作った一時ファイル等にも、対応できるのが良いところですね。

_____

```java
  public void doSomething() {
    try {
      Path tempFilePath = Files.createTempFile("id", ".temp");
      try (Writer writer = Files.newBufferedWriter(tempFilePath, StandardOpenOption.DELETE_ON_CLOSE)) {
        // do something.
      }
    } catch (IOException e) {
      // ...
    }
  }
```

Java NIO2 以降だと、ファイルを `newBufferedWriter` メソッドで開く時に、オプションで `DELETE_ON_CLOSE` [^2] を付けると、処理終了後に、そのファイルは削除されるのですね。

もとねた

- [FIO03-J. 一時ファイルはプログラムの終了前に削除する](https://www.jpcert.or.jp/java-rules/fio03-j.html "FIO03-J. 一時ファイルはプログラムの終了前に削除する") 

[^1]: Java NIO2 なので、 Java SE 7 以降の話です。 [ファイルI/O（NIO.2を含む）（Java チュートリアル > 重要なクラス > 基本的なI/O）](https://docs.oracle.com/cd/E26537_01/tutorial/essential/io/fileio.html)

[^2]: https://docs.oracle.com/javase/jp/8/docs/api/java/nio/file/StandardOpenOption.html#DELETE_ON_CLOSE