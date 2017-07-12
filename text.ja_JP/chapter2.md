# 開発環境の準備

## この章の目標

この章の目標は、作業用のPureScript開発環境を準備し、最初のPureScriptプログラムを書くことです。

これから書く最初のコードは、単純なライブラ。の例です。このライブラリは直角三角形の対角線の長さを計算する関数ひとつだけを提供します。

## 導入

PureScript開発環境を準備するために、次のツールを使います。

- [`psc`](http://purescript.org) - PureScriptコンパイラ本体
- [`npm`](http://npmjs.org) - 残りの開発ツールをインストールできるようにする、Nodeパッケージマネージャ
- [Pulp](https://github.com/bodil/pulp) - PureScriptプロジェクトの管理タスクを自動化するコマンドラインツール

この章ではこれらのツールのインストール方法と設定を説明します。

## PureScriptのインストール

PureScriptコンパイラをインストールするときに推奨される方法は、[PureScriptのウェブサイト](http://purescript.org) から適切なバイナリリリースをダウンロードすることです。

PureScriptコンパイラの実行ファイルがあなたのパス上で利用できるか確認しましょう。確認のために、コマンドラインでPureScriptコンパイラを実行してみましょう。

```text
$ psc
```

PureScriptコンパイラをインストールする上記以外の方法は、

- NPMやMacOS上のHomebrewのようなパッケージマネージャを使う。
- ソースからコンパイラを作成する。作成方法はPureScriptのWebサイトに書かれています。

## 各ツールのインストール

もし[NodeJS](http://nodejs.org/) がインストールされていないなら、NodeJSをインストールする必要があります。そうするとシステムに `npm`パッケージマネージャもインストールされるはずです。 `npm`がインストールされ、パス上で利用可能であることを確認してください。

PulpコマンドラインツールとBowerパッケージマネージャも `npm`を使ってインストールする必要があります。

```text
$ npm install -g pulp bower
```

これで、 `pulp`と `bower`コマンドラインツールがパス上に配置されます。最初のPureScriptプロジェクトを作成するために必要なすべてのツールの用意ができたことになります。

## Hello, PureScript!

まずはシンプルに始めましょう。Pulpを使って、基本的なHello World! プログラムをコンパイルして実行します。

空ディレクトリに `pulp init`コマンドでプロジェクトを作ることから始めましょう。

```text
$ mkdir my-project
$ cd my-project
$ pulp init

* Generating project skeleton in ~/my-project

$ ls

bower.json	src		test
```

pulpは `src`と `test`の二つのディレクトリと `bower.json`コンフィグレーションファイルを作成します。 `src`はソースファイルを格納するディレクトリで、 `test`はテストを格納するディレクトリです。本書の後の章で `test`ディレクトリを使用します。

`src/Main.purs`を下記のように修正してください。

```haskell
module Main where

import Control.Monad.Eff.Console

main = log "Hello, World!"
```

これは小さなサンプルコードですが、​​いくつかの重要な概念を示しています。

- すべてのソースファイルはモジュールヘッダから始まります。モジュール名は、ドットで区切られた大文字で始まる1つ以上の単語から構成されています。ここではモジュール名としてひとつの単語だけが使用されていますが、`My.First.Module`というようなモジュール名も有効です。
- モジュールは、モジュール名の各部分を区切るためのドットを含めた、完全な名前を使用してインポートされます。ここでは `trace`関数を提供する `Debug.Trace`モジュールをインポートしています。
- この `main`プログラムの定義本体は、関数適用の式になっています。PureScriptでは、関数適用は関数名の後に引数を空白で区切って書くことで示されます。

それではこのコードをビルドして実行してみましょう。次のコマンドを実行します。

```text
$ pulp run

* Building project in ~/my-project
* Build successful.
Hello, World!
```

おめでとうございます! 最初のPureScriptプログラムのコンパイルと実行ができました。

## ブラウザのためのコンパイル

Pulpは`pulp browserify`コマンドでPureScriptコードをWebブラウザに適したJavaScriptに変換できます。

```text
$ pulp browserify

* Browserifying project in ~/my-project
* Building project in ~/my-project
* Build successful.
* Browserifying...
```

うまくいくと、大量のJavaScriptがコンソールに出力されるのを目にするはずです。これは、Prelude_という名前の標準PureScriptライブラリとsrcディレクトリ内のコードに、[Browserify](http://browserify.org/)ツールを適用したアウトプットです。Javascriptコードはファイルにセーブでき、HTML書類に含めることもできます。これを試すと、ブラウザのコンソールに"、Hello, World!"が出力されます。

## 使用されていないコードを取り除く

Pulpは`pulp build`という別のコマンドを提供します。このコマンドは_使用されないコードを削除する_-Oオプションを付けて、アウトプットから不要なJavascriptコードを取り除くことができます。結果はずっと小さくなります。

```text
$ pulp build -O --to output.js

* Building project in ~/my-project
* Build successful.
* Bundling Javascript...
* Bundled.
```

もう一度書きますが、生成されたコードはHTMLドキュメントで使用できます。`output.js`をオープンすると、下記のようなコンパイルされたモジュールが見つかるはずです。

```javascript
(function(exports) {
  "use strict";

  var Control_Monad_Eff_Console = PS["Control.Monad.Eff.Console"];

  var main = Control_Monad_Eff_Console.log("Hello, World!");
  exports["main"] = main;
})(PS["Main"] = PS["Main"] || {});
```

ここでPureScriptコンパイラがJavascriptコードを生成する方法の要点が示されています。

- すべてのモジュールはオブジェクトに変換され、そのオブジェクトにはそのモジュールのエクスポートされたメンバが含まれています。
- PureScriptは可能な限り変数の名前をそのまま使おうとします
- PureScriptにおける関数適用は、そのままJavaScriptの関数適用に変換されます。
- 引数のない単純な呼び出しとしてメインメソッド呼び出しが生成され、すべてのモジュールが定義された後に実行されます。
- PureScriptコードはどんな実行時ライブラリにも依存しません。コンパイラによって生成されるすべてのコードは、あなたのコードが依存するいずれかのPureScriptモジュールをもとに出力されているものです。

PureScriptはシンプルで理解しやすいコードを生成すること重視しているので、これらの点は大切です。実際に、ほとんどのコード生成処理はごく軽い変換です。PureScriptについての理解が比較的浅くても、ある入力からどのようなJavaScriptコードが生成されるかを予測することは難しくありません。

## CommonJSモジュールのコンパイル

PulpはPureScriptのコードからCommonJSモジュールを生成することもできます。これはNodeJSで使うときやCommonJSを使った大規模プロジェクトでコードを小さなコンポーネントに分割するときに役に立ちます。

CommonJSモジュールを作成するためには、`pulp build`コマンドを`-O`オプションなしで使います。

```text
$ pulp build

* Building project in ~/my-project
* Build successful.
```

生成されたモジュールは、デフォルトで`output`ディレクトリに置かれます。各PureScriptモジュールは、それぞれのサブディレクトリに個別のCommonJSモジュールとしてコンパイルされます。

## Bowerによる依存関係の追跡

この章の目的となっている `diagonal`関数を書くためには、平方根を計算できるようにする必要があります。 `purescript-math`パッケージにはJavaScriptの `Math`オブジェクトのプロパティとして定義されている関数の型定義が含まれていますので、 `purescript-math`パッケージをインストールしてみましょう。

```text
$ bower install purescript-math --save
```

`--save`オプションは、依存関係を`bower.json`コンフィギュレーションファイルに追加します。

`purescript-math`ライブラリのソースは、`bower_components`サブディレクトリにインストールされ、あなたのプロジェクトをコンパイルするときに一緒にコンパイルされます。

## 対角線の長さの計算

それでは外部ライブラリの関数を使用する例として  `diagonal`関数を書いてみましょう。

まず、 `src/Main.purs`ファイルの先頭に次の行を追加し、`Math`モジュールをインポートします。

```haskell
import Math (sqrt)
```

数値の足し算や掛け算などの基本的な操作を定義した`Prelude`モジュールも、インポートする必要があります。

```haskell
import Prelude
```

そして、次のように `diagonal`関数を定義します。

```haskell
diagonal w h = sqrt (w * w + h * h)
```

この関数の型を定義する必要はないことに注意してください。 `diagonal`は2つの数値を取り数を返す関数である とコンパイラは推論することができます。しかし、ドキュメントとしても役立つので、通常は型注釈を提供しておくことをお勧めします。

それでは、新しい`diagonal`関数を使うように `main`関数も変更してみましょう。

```haskell
main = logShow (diagonal 3.0 4.0)
```

`pulp run`を使用して、モジュールを再コンパイルして実行します。

```text
$ pulp run

* Building project in ~/my-project
* Build successful.
5.0
```

## 対話式処理系を使用したコードのテスト

PureScriptコンパイラには `psci`と呼ばれる対話式のREPL(Read-eval-print loop)が付属しています。 `psci`はコードをテストしたり思いついたことを試すのにとても便利です。それでは、 `psci`を使って `diagonal`関数をテストしてみましょう。

Pulpは、`pulp psci`コマンドで自動的にソースモジュールを読み込むことができます。

```text
$ pulp psci
>
```

コマンドの一覧を見るには、 `:?`と入力します。

```text
> :?
The following commands are available:

    :?                        Show this help menu
    :quit                     Quit PSCi
    :reset                    Reset
    :browse      <module>     Browse <module>
    :type        <expr>       Show the type of <expr>
    :kind        <type>       Show the kind of <type>
    :show        import       Show imported modules
    :show        loaded       Show loaded modules
    :paste       paste        Enter multiple lines, terminated by ^D
```

Tabキーを押すと、自分のコードで利用可能なすべての関数、及びBowerの依存関係とプレリュードモジュールのリストをすべて見ることができるはずです。

`Prelude`モジュールをインポートすることから始めましょう。

```text
> import Prelude
```

幾つか数式を評価してみてください。

```text
> 1 + 2
3

> "Hello, " <> "World!"
"Hello, World!"
```

それでは `psci`で `diagonal`関数を試してみましょう。

```text
> import Main
> diagonal 5.0 12.0

13.0
```

また、 `psci`で関数を定義することもできます。

```text
> let double x = x * 2

> double 10
20
```

コード例の構文がまだよくわからなくても心配はいりません。 この本を読み進めるうちにわかるようになっていきます。

最後に、 `:type`コマンドを使うと式の型を確認することができます。

```text
> :type true
Boolean

> :type [1, 2, 3]
Array Int
```

インタラクティブモードで試してみてください。もしどこかでつまづいた場合は、メモリ内にあるコンパイル済みのすべてのモジュールをアンロードするリセットコマンド `:reset`を使用してみてください。

## 演習

1. (簡単)　`Math.pi`定数を使って、与えられた半径の円の面積を求める`circleArea`関数を書いてください。書いた関数をPSCiを使ってテストしてください。(_ヒント_: `import Math`文を`pi`をインポートするように変更することを忘れないでください。)
2. (やや難しい) `bower install`を使って、依存するパッケージとして`purescript-globals`をインストールしてください。その関数をPSCiでテストしてください。(_ヒント_: PSCiの`:browse`コマンドでモジュールの内容をブラウズできます。)

## まとめ

この章では、Pulpツールを使用して簡単なPureScriptプロジェクトをセットアップしました。

また最初のPureScript関数と、ブラウザやNodeJSで実行できるJavaScriptプログラムにコンパイルしました。

以降の章では、コードをコンパイルやデバッグ、テストするためにこの開発設定を使用しますので、これらのツールや使用手順に十分習熟しておくとよいでしょう。
