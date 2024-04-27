---
title: Kotlinのnull安全
tags:
  - Kotlin
  - null安全
private: false
updated_at: '2024-01-10T00:30:38+09:00'
id: 88bc2ca151a809ebbc3b
organization_url_name: null
slide: false
ignorePublish: false
---
## null安全とは
変数がnull値を持つことができるかどうかを表す概念のことを表します。
「null＝値がない」になります。
JavaやKotlinはnull参照を行うと、NullPointerExceptionという例外が発生します。

## null値非許容型とnull値許容型
* null値非許容型は、nullを保持できないです
* null値許容型は、nullを保持できます
変数をnull許容型にしたいときは、クエスチョンマーク（`?`）をつける。

```kotlin
// null値非許容型 nullを代入するとコンパイルエラー発生
var helloWorld: String = null

// null値許容型 nullを代入することが可能
var helloWorld: String? = null
```

## null値許容型変数のプロパティにアクセス
変数の後ろにセーフコール演算子(`?.`)を付与することで、null許容型変数のメソッドやプロパティにアクセスできます。この記法を使用すると、変数がnullでない時は、通常通りにメンバアクセスして、変数がnullの時は、メンバアクセスしないでnullを返します。
```kotlin
// helloWorldにnullが代入される可能性があり、
// その考慮をしていないためコンパイルエラー発生
var helloWorld: String? = "hello world"
println(helloWorld.length)

// セーフコール演算子を付与することで、null許容をしてメンバアクセスが可能になっている
var helloWorld: String? = "hello world"
println(helloWorld?.length)
```

## 非nullアサーション演算子（!!）を使用
変数の後ろに非nullアサーション演算子(`!!`)を付与することで、null許容型変数を強制的にnull非許容型変数として扱う。もしも変数にnullが代入されると例外が発生します。例外をcatchする文やif文やエルビス演算子(`?:`)などを使って、アプリの挙動が止まらないように回避する必要がある。基本的には、非nullアサーション演算子(`!!`)ではなくセーフコール演算子(`?.`)を使用することが望ましい。

```kotlin
// 非nullアサーション演算子を使用することでhelloWorldをnull非許容変数として扱う
var helloWorld: String? = "hello world"
println(helloWorld!!.length)

// NullPointerExceptionが発生
var helloWorld: String? = null
println(helloWorld!!.length)
```

## エルビス演算子（?:）を使用
エルビス演算子(`?:`)はこの演算子の左にある第1項の変数がnullでない時は、第1項の値を返し、nullの時は演算子の右にある第2項の値を返します。変数がnullの時にある特定のデフォルト値を設定したいときにエルビス演算子(`?:`)を使用します。

```kotlin
// 出力：11
// helloWorldの値がnullではないので、helloWorld?.lengthを返す
var helloWorld: String? = "hello world"
var helloWorldLength = helloWorld?.length ?: 0
println(helloWorldLength)

// 出力：0
// helloWorldの値がnullなので、0を返す
var helloWorld: String? = null
var helloWorldLength = helloWorld?.length ?: 0
println(helloWorldLength)
```

## おわりに
セーフコール演算子(`?.`) : nullを許容する
非nullアサーション演算子(`!!`) ：変数をnull非許容型にする
エルビス演算子(`?:`) : 変数がnullかどうかで値が変化する
