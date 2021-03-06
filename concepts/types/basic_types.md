# 基本型 - [Basic types](https://kotlinlang.org/docs/basic-types.html)
この項目では整数、浮動小数点数、ブール型、文字列、配列といった基本的な型を扱っています。

## 基本型について
Kotlinの特徴的な点として、整数や浮動小数点数、文字列といった基本型もすべてオブジェクトとして扱われる点があります。

JavaやC#などのオブジェクト指向型プログラミング言語の多くにはプリミティブ型とオブジェクト型の2種類の型があります。

オブジェクト型の値は恒久的、あるいは一時的に確保された領域に値を保持して、オブジェクト型の変数や引数には領域のアドレスが保持されます。

このため、オブジェクト型の値を使用するには変数や引数からアドレスを取り出し、その指している領域のオブジェクトから値を取り出すという2段階の操作が必要となります。

整数や浮動小数点数ではデータサイズが決まっていて、かつ足し算などあらかじめ定義された演算が頻繁に使用されるため、変数や引数に値をそのまま保持するプリミティブ型を使用して、
オブジェクト型で必要となるオブジェクトからの値の取り出しといった操作を省略して性能を確保していました。

その代償として整数型や浮動小数点型に対応するオブジェクト型 (ラッパークラス) を定義し、必要な場合にはオブジェクト型に変換 (ボクシング変換) できるようにしています。

Kotlinはすべてをオブジェクト型に統一しているためボクシング変換を意識する必要はないのですが、上で示したような性能上の問題を回避するため、
コンパイラがオブジェクト型である必要がないと判断した場合にはオブジェクトを使用せずに値だけで計算を行う最適化を行うとのことです。

通常はこの最適化の影響を受けることはありません (影響があればそれは不具合です) が、オブジェクト参照の同一性を判定する演算を行った場合、最適化が適用されているかどうかで
結果が変わることがあります。
```
val a = 1000
val b = a
val c = 1000
```
ここで`val`は代入不可の変数を宣言するキーワードで、`val a = 1000`は代入不可の変数`a`を`1000`という整数オブジェクトで初期化することを示しています。
変数`a`は明示的に型が指定されていないので、この初期化によってInt (整数) 型の変数とみなされてコンパイルが行われます。
変数宣言については変数の項を参照してください。また、Kotlinでは文末を示す`;`のような記号は必要ありません。

`a`と`b`は同じオブジェクトを指している (同じアドレスを保持している) ので、`a`と`b`が同じオブジェクトかを判定する演算`a === b`は「真」になります。
整数値は原則として出現するごとに別のオブジェクトが生成される (異なるアドレスを保持している) ため、上の1行目の`1000`と3行目の`1000`は別のオブジェクトとなり、
演算`a === c`は「偽」となります。
ただし、最適化により`a`と`c`がともに値のまま処理されている場合には`a === c`は「真」となります[^1]。

[^1]:Int型同士の`===`による比較は現時点で非推奨となっているようですので将来のバージョンではエラーとなるかもしれません。

また、基本型は不変オブジェクトとして定義されています。

不変オブジェクトと可変オブジェクトは別の項で触れます。

## 整数型 - [Integer types](https://kotlinlang.org/docs/basic-types.html#integer-types)
Kotlinは8ビット符号付整数型である**Byte**型、16ビット符号付整数型である**Short**型、32ビット符号付整数型である**Int**型、64ビット符号付整数型である**Long**型の
4種の符号付整数型を用意しています。

整数型同士にクラスとしての親子関係はなく、変数は宣言時に型付けされているため、例えばInt型の変数にShort型の値を代入するといったことはできません。

なお、異なる型の間での演算は演算子オーバーロードにより定義されているので、Long型の値にInt型の値を足すことは可能です。

以下にコンパイル時にエラーとなる例を示します。
```
var a = 1000 // Int型の変数として初期化される
var b = 4000000000 // Int型の最大値 (2147483647) より大きいのでLong型の変数として初期化される

b = a // Long型の変数にInt型を代入できないのでコンパイルエラーになる
```
ここで`var`は代入可能な変数を宣言するキーワードです。
整数リテラルの型は明示的に指定されていなければ値によって決まり、32ビット符号付整数で表現可能な範囲であればInt型、それを超えていればLong型となります。
変数`b`はInt型で表現可能な値を越えている整数値で初期化されているため、自動的にLong型となります。

### 整数リテラルの表現と整数変数の初期化、代入
符号付整数の整数リテラルにはInt型に相当する整数リテラルとLong型に相当する整数リテラルの2種類があります。

32ビット符号付整数の範囲 (-2147483648以上、2147483647以下) の整数リテラルはInt型に相当する整数リテラルとして扱われ、
32ビット符号付整数の範囲を越える整数リテラルはLong型に相当する整数リテラルとして扱われます (もちろん64ビット符号付整数で表現できないリテラル値はエラーになります)。

32ビット符号付整数の範囲の数値をLong型として扱いたい場合には`1L`のように末尾に「L」を付けてLong型であることを示します。

Byte型、Short型に相当する整数リテラルは定義されていませんが、Byte型、Short型変数への初期化、代入の際には整数リテラルが対応する型の整数リテラルとみなされて処理されます。
```
val b0: Byte = 1 // Byte型変数が「1」で初期化されます
val b1: Byte = 200 // Byte型変数は-128以上127以下の整数値しか保持できないため、コンパイルエラーとなります
val s0: Short = 1 // Short型変数が「1」で初期化されます
val s1: Short = 40000 // Short型変数は-32768以上32767以下の整数値しか保持できないため、コンパイルエラーとなります
val i0: Int = 1L // Long型に相当する整数リテラルでInt型 (およびByte型、Short型) を初期化することはできず、コンパイルエラーとなります
val l0: Long = 1 // Long型変数が「1」で初期化されます
val l1 = 1L // Long型に相当する整数リテラル「1」で初期化されるのでLong型の変数となります
```
上記のように変数宣言では変数名の後に「:」を置いて型名を書くことで変数の型を宣言することができます。

整数リテラルでは10進表現のほかに16進表現と2進表現が用意されています。16進表現は頭に「0x」を付け`0x12FE`のように記述し、
2進表現は頭に「0b」を付けて`0b0001001011111110`のように表記します。
当然ですが16進表現、2進表現にもLong型に相当する整数リテラルを示す「L」を付加することができます。

おまけ的な機能として整数リテラルの途中に「\_」(下線) を好きな位置に入れることができます。例えば`1_234_567`のように10進数の3桁区切りに使用する、
`0x1234_5678`や`0b00010010_11111110`のように16進数の4桁区切り、2進数の8桁区切りに利用するなどが可能です。

### 符号なし整数
Kotlinでは符号付整数と同様に4種類の符号なし整数型**UByte**型、**UShort**型、**UInt**型、**ULong**型を用意しています。

符号なし整数リテラルは`1u`または`1U`のように末尾に「u」または「U」を付けて表現します。ULong型に相当する整数リテラルには「uL」または「UL」を付けます。

符号なし整数の初期化や代入に`1`のようなInt型に相当する整数リテラル、`1L`のようなLong型に相当する整数リテラルを使用することはできません。
`1u`や`1uL`のように符号なし整数であることを明示的に示した整数リテラルが必要です。

同様に符号なし整数変数と符号付整数リテラルとの間の演算もコンパイル時にエラーとなります。

### 整数型の演算
符号付整数型同士、符号なし整数型同士の演算は可能ですが、符号付整数型と符号なし整数型の間の演算はコンパイルエラーになります。

```
var i0: Int = 1
val s0: Short = 2
var ui0: UInt = 3u
val us0: UShort = 4u

i0 += s0 // Int型とShort型の演算は可能
ui0 += us0 // UInt型とUShort型の間の演算は可能
i0 += us0 // Int型とUShort型の間の演算はコンパイルエラー
ui0 += s0 // UInt型とShort型の間の演算もコンパイルエラー
ui0 += 1 // 「1」もInt型として扱われるためコンパイルエラー
```
ただし、比較演算子は符号付整数型同士、符号なし整数型同士でも型を越えて適用することはできず、以下の例はコンパイルエラーになります。
```
var i0: Int = 1
var l0: Long = 1L

i0 == l0
```
この違いは単純に型ごとの比較演算子のオーバーロードが提供されていないからだと思いますが、なぜ提供していないのかはまだ調べていません。

### ビット演算
Kotlinは以下のビット演算をサポートしています。
| 演算子表記| メソッド表記 | 機能 |
|:-:|:-:|:-|
| x shl y | x.shl(y) | 左シフト |
| x shr y | x.shr(y) | 符号付右シフト |
| x ushr y | x.ushr(y) | 符号なし右シフト |
| x and y | x.and(y) | 論理積 |
| x or y | x.or(y) | 論理和 |
| x xor y | x.xor(y) | 排他的論理和 |
| | x.inv() | ビット反転 |

ビット演算は同じ整数型同士の演算だけが定義されており、型が異なる場合はコンパイルエラーとなります。

演算子の優先度については後日記載の予定です。

## 浮動小数点数型
Kotlinの浮動小数点型はIEEE標準に準じた32ビット浮動小数点数型である**Float**型と64ビット浮動小数点数型である**Double**型とがあります。
これらの浮動小数点数型の表現範囲などはIEEE標準と同等ですが、一部非互換な点があるため注意が必要です。

### 浮動小数点数リテラルの表現と浮動小数点数変数の初期化、代入
浮動小数点数リテラルにはDouble型に相当する浮動小数点数リテラルとFloat型に相当する浮動小数点数リテラルの2種類があります。

浮動小数点数リテラルは多くのプログラミング言語と同様に数値に小数点「.」が含まれているか、数値の後に「e」と数値で指数部が指定されていれば
Double型に相当する浮動小数点数リテラルとして扱われ、Float型に相当する浮動小数点数リテラルには末尾に「f」を付けます。
浮動小数点数リテラルでは値によって型が選択されることはありません。

浮動小数点数リテラルの自動的な型変換はありません。Float型変数の初期化、代入、演算には末尾に「f」を付加した明示的なFloat型に相当する
浮動小数点数リテラルを使用する必要があります。

### 浮動小数点数でのNaNと-0.0の扱い
IEEE標準ではNaNはNaN自身を含めていかなる値とも比較できない、-0.0は0.0と等しいと決められていますが、浮動小数点数をキーとしてソートする場合など
順序が決まっていないと困る場合があるので、最適化により浮動小数点数型の値として処理されず、オブジェクトとして処理される場合にはNaNと-0.0が
IEEE標準とは異なる扱いとなります。

具体的にはNaNはNaNと等しく、正の無限大を含む他のすべての値より大きいと判定され、-0.0は0.0より小さいと判定されます。

比較においてこれらの値が使用される可能性がある場合は注意が必要です。

## 数値型の型変換
整数型および浮動小数点型は親子関係を持たないオブジェクト型なので、キャストによる型変換はできません。コンパイルエラーにならず実行時エラー (例外) になる可能性があります。

このため数値型には型変換用のメソッドが用意されていて、`toByte()`、`toULong()`、`toDouble()`のように「to」+型名がメソッド名になっています。

型変換用メソッドは変換先でオーバーフローが発生しても実行時エラーとはなりません。
整数型から整数型への型変換は符号拡張したうえで変換先のビット数だけビットをコピーする操作となり、変換先の数値範囲を越える値を変換しても
エラーとはならずに必ずしも期待していない結果となります。

浮動小数点数型からの変換で変換先の数値範囲を越える値を変換した場合には、整数型に変換した場合は変換先の型の最大値または最小値、
Float型への変換では正負の無限大となるようです。

## ブール型 (Boolean)
ブール型は**true**または**false**の値を持ちます。

ブール型の演算には「||」(論理和)、「&&」(論理積)、「!」(否定)の3種があり、論理和と論理積は遅延評価されます。

ブール型演算を実装するand()メソッドとor()メソッドの説明に「&&」、「||」演算子と異なり対象となる両方の引数が評価されるとあります。これに関しては後程追記する予定です。

## 文字型 (Characters)
文字型 (Char) は16ビットユニコード文字1文字を表現するオブジェクト型で、文字リテラルは`'a'`のようにシングルクォートで囲みます。

文字リテラルでは他の言語と同様に「\\」を使用した記法が可能で、「\\t」(タブ)、「\\b」(バックスペース)、「\\n」(改行)、「\\r」(復帰)、「\'」(シングルクォーテーション)、
「\\"」(ダブルクォーテーション)、「\\\\」(バックスラッシュ)、「\$」($記号)と「\uNNNN」(4桁の16進数NNNNで示されるユニコード文字)が使用できます。

0から9の数字が文字型変数に入っている場合、digitToInt()関数によりInt型の数値に変換することができます。数字以外の文字だった場合には例外が発生します。
