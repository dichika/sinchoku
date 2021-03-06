---
title: 06_関数(Functions)
output:
  html_document:
    keep_md: yes
---

# 関数(Functions)

```
Functions are a fundamental building block of R: to master many of the more advanced techniques in this book, you need a solid foundation in how functions work. You've probably already created many R functions, and you're familiar with the basics of how they work. The focus of this chapter is to turn your existing, informal knowledge of functions into a rigorous understanding of what functions are and how they work. You'll see some interesting tricks and techniques in this chapter, but most of what you'll learn will be more important as the building blocks for more advanced techniques. \index{functions}
```

関数はRの基礎をなす部品の一つである。この本で紹介している多くの発展的テクニックをマスターするには関数の理解が必須である。読者は既にR上で多くの関数をつくってきており、関数の挙動について基本的な部分についてはおさえているものと想定している。本章の目的は読者が実務で身につけてきた知識を、関数とは何か、関数とはどのように動くのかという点で強固な理解に導くことにある。本章ではいくつかの面白いトリックやテクニックを紹介している。だが、多くは発展的テクニックを身につける上での基礎として重要なものである。

```
The most important thing to understand about R is that functions are objects in their own right. You can work with them exactly the same way you work with any other type of object. This theme will be explored in depth in [functional programming](#functional-programming).
```

Rを理解する上でもっとも大切なことは関数そのものがオブジェクトということである。他のオブジェクトと全く同じやり方で関数を扱うことができる。この話題については[functional programming](#functional-programming)で深堀りする。

##### クイズ(Quiz)

```
Answer the following questions to see if you can safely skip this chapter. You can find the answers at the end of the chapter in [answers](#function-answers).
```

以下のテストは本章を読む必要があるかどうかを判定するテストである。答えがすぐに頭に浮かぶようであれば本章はとばしても良い。答えは[answers](#function-answers)を参照のこと。

```
1.  What are the three components of a function?
```

関数を構成する3つの要素とはなにか?

```
1.  What does the following code return?
```

以下のコードはどのような結果を返すか?

    
    ```r
    x <- 10
    f1 <- function(x) {
      function() {
        x + 10
      }
    }
    f1(1)()
    ```
    
```
1.  How would you more typically write this code?
```

以下のコードはどのようにしたらより典型的な形に書き直せるだろうか?

    
    ```r
    `+`(1, `*`(2, 3))
    ```
```    
1.  How could you make this call easier to read?
```

以下のコードはどのようにしたら読みやすくなるだろうか?

    
    ```r
    mean(, TRUE, x = c(1:10, NA))
    ```

```
1.  Does the following function throw an error when called? Why/why not?
```

以下の関数は呼び出し時にエラーとなるだろうか?その理由を述べよ。

    
    ```r
    f2 <- function(a, b) {
      a * 10
    }
    f2(10, stop("This is an error!"))
    ```

```
1.  What is an infix function? How do you write it? What's a replacement function? How do you write it?
```

infix 関数とはなにか? それはどのように書けるか? replacement 関数とはなにか? それはどのように書けるか?

```
1.  What function do you use to ensure that a cleanup action occurs 
    regardless of how a function terminates?
```

関数がどのように終了するかにかかわらず、cleanup アクションが起きるようにするにはどのような関数を使えばよいか?

##### 概要(Outline)

```
* [Function components](#function-components) describes the three main components of a function.
```

* [関数の構成要素(Function components)](#function-components):この項では関数を構成する3つの要素について説明する。

```
* [Lexical scoping](#lexical-scoping) teaches you how R finds values from names, the process of lexical scoping.
```

* [レキシカルスコープ(Lexical scoping)](#lexical-scoping):この項ではRが名前から値を見つける仕組み、レキシカルスコープについて説明する。

```
* [Every operation is a function call](#all-calls) shows you that everything that happens in R is a result of a function call, even if it doesn't look like it.
```

* [全ての操作は関数呼び出しである(Every operation is a function call)](#all-calls):この項ではRにおいて全ては関数呼び出しの結果であること(一見そうはみえなくても)を示す。

```
* [Function arguments](#function-arguments) discusses the three ways of supplying arguments to a function, how to call a function given a list of arguments, and the impact of lazy evaluation.
```

* [関数の引数(Function arguments)](#function-arguments) :この項では関数に引数を渡す3つの方法、引数リストを渡した関数の呼び出し方、そして遅延評価の威力について議論する。

```
* [Special calls](#special-calls) describes two special types of function: infix and replacement functions.
```  

* [特殊な関数呼び出し(Special calls)](#special-calls):この項では、infix関数およびreplacement関数という2つの特殊な関数について説明する。

```
* [Return values](#return-values) discusses how and when functions return values, and how you can ensure that a function does something before it exits.
```

* [関数の返り値(Return values)](#return-values):この項では関数がどのように、そしていつ値を返すかについて議論する。また関数がその処理を終える際に一定の処理を実行する方法についても示す。

##### 本章のコードを実行するにあたり必要なもの(Prerequisites)

```
The only package you'll need is `pryr`, which is used to explore what happens when modifying vectors in place. Install it with `install.packages("pryr")`.
```

本章のコードを実行するために必要なパッケージとして`pryr`がある。これはベクトルを変更した際に何が起きているかを把握するために必要なものである。`install.packages("pryr")`でインストールしておくこと。

## 関数の構成要素(Function components) {#function-components}

```
All R functions have three parts: \index{functions!body} \index{functions!formals} \index{functions!environment}
```

Rの関数は以下の3つの要素で構成されている。\index{functions!body} \index{functions!formals} \index{functions!environment}

```
* the `body()`, the code inside the function.
```

* `body()`:関数内のコード

```
* the `formals()`, the list of arguments which controls how you can call the function.
```

* `formals()`:関数の呼び出しをコントロールする引数のリスト

```
* the `environment()`, the "map" of the location of the function's variables.
```

* `environment()`:関数における変数の位置を示す"地図"

```
When you print a function in R, it shows you these three important components. If the environment isn't displayed, it means that the function was created in the global environment. \indexc{formals()} \indexc{body()} \index{environments!of a function}
```

Rにおいて関数をプリントすると、以上の3つの要素を表示する。`environment()`が表示されない時は、大域的環境(global environment)において関数が生成されていることを示す。 \indexc{formals()} \indexc{body()} \index{environments!of a function}


```r
f <- function(x) x^2
f
#> function(x) x^2

formals(f)
#> $x
body(f)
#> x^2
environment(f)
#> <environment: R_GlobalEnv>
```

```
The assignment forms of `body()`, `formals()`, and `environment()` can also be used to modify functions.
```

`body()`、`formals()`、`environment()`への代入形は関数を変更する際にも用いられる。

```
Like all objects in R, functions can also possess any number of additional `attributes()`. One attribute used by base R is "srcref", short for source reference, which points to the source code used to create the function. Unlike `body()`, this contains code comments and other formatting. You can also add attributes to a function. For example, you can set the `class()` and add a custom `print()` method. \index{functions!attributes}
```

Rにおける他のオブジェクトと同様、関数もまた追加の`attributes`を持つことができる。base Rにおける属性の一つとして"srcref"がある。これは"source reference"の短縮形であり、関数が生成されるのに用いられたソースコードを指し示す。`body()`とは異なり、そこにはコードのコメント及び他の書式も含んでいる。関数には他の属性も追加できる。例えば、`class()`をセットして、任意の`print()`メソッドを追加できる。

### プリミティブ関数(Primitive functions)

```
There is one exception to the rule that functions have three components. Primitive functions, like `sum()`, call C code directly with `.Primitive()` and contain no R code. Therefore their `formals()`, `body()`, and `environment()` are all `NULL`:  \index{primitive functions} \index{functions!primitive|see{primitive functions}} \index{.Primitive@\texttt{.Primitive()}|see{Primitive functions}}
```

関数は3つの要素を持つというルールには1つ例外がある。`sum()`のようなプリミティブ関数は`.Primitive()`でもってC言語のコードを直接呼び出し、Rのコードをその中に持たない。そのため、formals()`、`body()`および`environment()`は全て`NULL`を返す。 \index{primitive functions} \index{functions!primitive|see{primitive functions}} \index{.Primitive@\texttt{.Primitive()}|see{Primitive functions}}



```r
sum
```

```
## function (..., na.rm = FALSE)  .Primitive("sum")
```

```r
formals(sum)
```

```
## NULL
```

```r
body(sum)
```

```
## NULL
```

```r
environment(sum)
```

```
## NULL
```

```
Primitive functions are only found in the `base` package, and since they operate at a low level, they can be more efficient (primitive replacement functions don't have to make copies), and can have different rules for argument matching (e.g., `switch` and `call`).  This, however, comes at a cost of behaving differently from all other functions in R. Hence the R core team generally avoids creating them unless there is no other option.
```

プリミティブ関数は`base`パッケージの中にのみ含まれており、低レベル層において挙動するため、コピーを作る必要がないという点で通常のRコードよりも効率的であり、引数のマッチングにおいても通常とは異なるルールをもつ(例:`switch()`、`call()`)。しかしこれはRにおける他の関数とは異なる動作コストにつながる。したがってRの開発コアチームは他に選択肢がない場合を除いてそれらの関数を通常使っていない。

### エクササイズ(Exercises)

```
1.  What function allows you to tell if an object is a function? What function allows you to tell if a function is a primitive function?
```

オブジェクトが関数であるかどうかを判定する関数はなにか? またプリミティブ関数かどうかを判定する関数はなにか?

```
1.  This code makes a list of all functions in the base package. 
```

以下のコードを実行するとbaseパッケージに含まれる関数のリストを取得する。
    
    
    ```r
    objs <- mget(ls("package:base"), inherits = TRUE)
    funs <- Filter(is.function, objs)
    ```
```
    Use it to answer the following questions:

    a. Which base function has the most arguments?
    
    a. How many base functions have no arguments? What's special about those functions?
       
    a. How could you adapt the code to find all primitive functions?
```
これを使って以下の質問にこたえよ。

a. もっとも引数の多い関数はどれか?

a. 引数を全く持たない関数はいくつあるか? これらの関数の特殊な点はなにか?

a. 全てのプリミティブ関数を探すには上記コードをどのように変更すればよいか?

```
1. What are the three important components of a function?
```

1. 関数を構成する3つの重要な要素とはなにか?

```
1. When does printing a function not show what environment it was created in?
```

1. 関数をプリントした際、自身が生成された環境を返さないのはどういうときか?

## レキシカルスコープ(Lexical scoping) {#lexical-scoping}

```
Scoping is the set of rules that govern how R looks up the value of a symbol. In the example below, scoping is the set of rules that R applies to go from the symbol `x` to its value `10`: \index{scoping!lexical|see{lexical scoping}} \index{lexical scoping}
```

スコープはRがシンボルに紐づけられた値を探索する際のルールを指す。以下に示す例の場合、スコープはRがシンボル`x`に紐づけられた値`10`を探すために適用されるルールを指す。


```r
x <- 10
x
```

```
## [1] 10
```

```
Understanding scoping allows you to:
```

スコープを理解することで以下が可能になる。

```
* build tools by composing functions, as described in 
  [functional programming](#functional-programming).
```

* 関数を組み合わせてツールを開発することができる(詳細は[functional programming](#functional-programming)で述べる)。

```
* overrule the usual evaluation rules and do non-standard evaluation, as described in [non-standard evaluation](#nse).
```

通常の評価ルールを範囲を超えて、non-standard evalution を用いることができる。これについては[non-standard evaluation](#nse)で述べる。

```
R has two types of scoping: __lexical scoping__, implemented automatically at the language level, and __dynamic scoping__, used in select functions to save typing during interactive analysis. We discuss lexical scoping here because it is intimately tied to function creation. Dynamic scoping is described in more detail in [scoping issues](#scoping-issues).
```

Rには2種類のスコープがある。__レキシカルスコープ(lexical scoping)__ と __ダイナミックスコープ()dynamic scoping)__ である。前者は言語レベルで自動的に実装されており、後者は対話型分析を進める際にタイピングの量を減らせるよう関数の中で用いられている。レキシカルスコープは関数生成に深く関係しているため、ここではレキシカルスコープについて話を進める。ダイナミックスコープについては[scoping issues](#scoping-issues)で詳細を述べる。

```
Lexical scoping looks up symbol values based on how functions were nested when they were created, not how they are nested when they are called. With lexical scoping, you don't need to know how the function is called to figure out where the value of a variable will be looked up. You just need to look at the function's definition.
```

レキシカルスコープはシンボルに紐づけられた値を探す際、関数が生成された時どのようにネストされていたかに基づいて探す。関数が呼び出された時のネストに基づくわけではない。レキシカルスコープの上では、変数の値が探される場所を理解するために関数がどのように呼ばれるかを知る必要はない。関数の定義のみに注意すればよい。

```
The "lexical" in lexical scoping doesn't correspond to the usual English definition ("of or relating to words or the vocabulary of a language as distinguished from its grammar and construction") but comes from the computer science term "lexing", which is part of the process that converts code represented as text to meaningful pieces that the programming language understands.
```

レキシカルスコープにおける"レキシカル"は、通常の英単語の定義(「文法や構文とは区別される言語における単語群」)ではなく、コンピュータサイエンスの用語"字句解析(lexing)"に由来している。字句解析とはテキストとして表現されているコードをプログラミング言語が理解できるような意味をもつ単位に変換するプロセスを意味する。

```
There are four basic principles behind R's implementation of lexical scoping:

* name masking
* functions vs. variables
* a fresh start
* dynamic lookup
```

Rにおけるレキシカルスコープの実装については以下の4つの原則がある。

* ネームマスキング(name masking)
* 関数と変数(functions vs. variables)
* フレッシュスタート(a fresh start)
* ダイナミックルックアップ(dynamic lookup)

```
You probably know many of these principles already, although you might not have thought about them explicitly. Test your knowledge by mentally running through the code in each block before looking at the answers.
```

読者はこれらの多くについて既にご存知かもしれないが、明示的には把握していないだろう。以下に示す解説を読む前に各ブロックのコードを頭の中で実行して、自身の知識をテストしてみてほしい。

### ネームマスキング(Name masking)

```
The following example illustrates the most basic principle of lexical scoping, and you should have no problem predicting the output.
```

以下に示す例はレキシカルスコープの最も基本的な原則を示している。このコードの実行例については容易に結果が予想できるだろう。


```r
f <- function() {
  x <- 1
  y <- 2
  c(x, y)
}
f()
rm(f)
```

```
If a name isn't defined inside a function, R will look one level up.
```

関数内で名前が定義されていない場合、Rは1つ上のレベルで名前を探す。


```r
x <- 2
g <- function() {
  y <- 1
  c(x, y)
}
g()
rm(x, g)
```

```
The same rules apply if a function is defined inside another function: look inside the current function, then where that function was defined, and so on, all the way up to the global environment, and then on to other loaded packages. Run the following code in your head, then confirm the output by running the R code.
```

関数が別の関数内で定義された時も同じルールが適用される。まずは関数内で名前を探し、そこになければその関数が定義された関数内で名前を探し、といった形で大域的環境に到達するまで探し続ける。以下のコードを頭の中で実行した上で、実際にRで実行してその結果を確認してほしい。



```r
x <- 1
h <- function() {
  y <- 2
  i <- function() {
    z <- 3
    c(x, y, z)
  }
  i()
}
h()
rm(x, h)
```

```
The same rules apply to closures, functions created by other functions. Closures will be described in more detail in [functional programming](#functional-programming); here we'll just look at how they interact with scoping. The following function, `j()`, returns a function.  What do you think this function will return when we call it? \index{closures!scoping}
```

クロージャ、すなわち関数によって生成された関数についても同じルールが適用される。クロージャの詳細については[functional programming](#functional-programming)で解説する。ここではクロージャがスコープとどう関係してくるのかについて見るにとどめる。以下の関数`j()`は関数を返す。`j()`が返す関数を呼び出す時、この呼び出された関数は何を返すだろうか? \index{closures!scoping}


```r
j <- function(x) {
  y <- 2
  function() {
    c(x, y)
  }
}
k <- j(1)
k()
rm(j, k)
```

```
This seems a little magical (how does R know what the value of `y` is after the function has been called). It works because `k` preserves the environment in which it was defined and because the environment includes the value of `y`. [Environments](#environments) gives some pointers on how you can dive in and figure out what values are stored in the environment associated with each function.
```

これはちょっとばかり魔法のようにみえる。どうやってRは関数が呼び出された後に`y`の値を知ったのだろうか。これは`k`がそれが定義された環境を保存していたからであり、その環境の中に`y`の値が含まれていたからである。[Environments](#environments)では、関数に結びつけられた環境の中に値がどのようにして保存されているかについて深く理解するための指針を示す。

### 関数と変数(Functions vs. variables)

```
The same principles apply regardless of the type of associated value --- finding functions works exactly the same way as finding variables:
```

これまでと同じ原則は、シンボルに紐づけられた値の型によらず適用される、つまり変数の値を探す時のルールは、関数にも当てはまる。


```r
l <- function(x) x + 1
m <- function() {
  l <- function(x) x * 2
  l(10)
}
m()
```

```
## [1] 20
```

```r
rm(l, m)
```

```
For functions, there is one small tweak to the rule. If you are using a name in a context where it's obvious that you want a function (e.g., `f(3)`), R will ignore objects that are not functions while it is searching. In the following example `n` takes on a different value depending on whether R is looking for a function or a variable.
```

ただし、関数の場合は一つだけ小さな違いがある。明らかに関数を呼び出したいとわかっている局面(例えば`f(3)`のような)では、関数ではないオブジェクトはその探索の対象から外れる。以下の例では`n`はRが関数を呼び出したいか変数を呼び出したいかによって異なる値に結びつけられる。


```r
n <- function(x) x / 2
o <- function() {
  n <- 10
  n(n)
}
o()
```

```
## [1] 5
```

```r
rm(n, o)
```
```
However, using the same name for functions and other objects will make for confusing code, and is generally best avoided.
```

しかし、関数とその他のオブジェクトで同じ名前をつけることはコード内で混乱を招くので、一般的には避けた方が良い。

### フレッシュスタート(A fresh start) {#fresh-start}

```
What happens to the values in between invocations of a function? What will happen the first time you run this function? What will happen the second time? (If you haven't seen `exists()` before: it returns `TRUE` if there's a variable of that name, otherwise it returns `FALSE`.)
```

関数を複数回呼び出す間にそれに紐づけられた値には何が起きているのだろうか? 下記コード内の関数を最初に呼び出した際には何が起きるのだろうか? さらに二度目に呼び出した時は? (なお、下記コード内で用いられている`exists()`は、指定された名前に紐づけられた変数が存在する場合は`TRUE`を返し、存在しない場合は`FALSE`を返す関数である。)


```r
j <- function() {
  if (!exists("a")) {
    a <- 1
  } else {
    a <- a + 1
  }
  print(a)
}
j()
rm(j)
```
```
You might be surprised that it returns the same value, `1`, every time. This is because every time a function is called, a new environment is created to host execution. A function has no way to tell what happened the last time it was run; each invocation is completely independent. (We'll see some ways to get around this in [mutable state](#mutable-state).)
```

読者は、上記関数が初回も2回目以降も同じ値、つまり`1`を返すことに驚いたかもしれない。これは関数が呼び出されるたびに、その呼び出しに対して新しい環境が生成されるからである。関数は前回と今回の呼び出しを区別することはできない。つまり関数呼び出しは完全に独立しているのである。(この話題の取り扱いについては[mutable state](#mutable-state)でも触れる。)


### ダイナミックルックアップ(Dynamic lookup)

```
Lexical scoping determines where to look for values, not when to look for them. R looks for values when the function is run, not when it's created. This means that the output of a function can be different depending on objects outside its environment: 
```

レキシカルスコープは値をどこから探すかは決定するが、いつ探すかは決定しない。Rは関数が実行された際に値を探す、関数が生成された時ではない。これは関数の]外部環境の状態に依存して、関数の出力結果が異なることを意味する。


```r
f <- function() x
x <- 15
f()
```

```
## [1] 15
```

```r
x <- 20
f()
```

```
## [1] 20
```

```
You generally want to avoid this behaviour because it means the function is no longer self-contained. This is a common error --- if you make a spelling mistake in your code, you won't get an error when you create the function, and you might not even get one when you run the function, depending on what variables are defined in the global environment.
```

一般的にこの挙動は避けたいものだろう。なぜならこの場合、関数はそれのみで完結するものではなくなるからだ。コードを書いている時にスペルミスをした場合、関数を生成した時にはエラーは出ない。大域的環境に定義されている変数によっては関数を実行した時にすらエラーは出ないかもしれない。これはよくあるエラーの原因である。

```
One way to detect this problem is the `findGlobals()` function from `codetools`. This function lists all the external dependencies of a function: \indexc{findGlobals()}
```

この問題を検出するために、`codetools`パッケージの`findGlobals()`という関数を用いるという方法がある。この関数は、関数が依存する外部環境を全てリストアップする。 \indexc{findGlobals()}


```r
f <- function() x + 1
codetools::findGlobals(f)
```

```
## [1] "+" "x"
```

```
Another way to try and solve the problem would be to manually change the environment of the function to the `emptyenv()`, an environment which contains absolutely nothing:
```

この問題を解決する別の方法として、関数の環境を`emptyenv()`で何も含まない環境に変えてしまうというものが考えられる。


```r
environment(f) <- emptyenv()
f()
```

```
## Error in f():  関数 "+" を見つけることができませんでした
```

```
This doesn't work because R relies on lexical scoping to find _everything_, even the `+` operator. It's never possible to make a function completely self-contained because you must always rely on functions defined in base R or other packages.
```

しかしこれは機能しない。なぜならRは_全て_(｀+｀演算子ですら)のオブジェクトの探索においてレキシカルスコープに依存しているからである。通常Rで演算を行う場合は、Rのbaseパッケージやその他のパッケージに定義された関数を用いざるをえないため、完全に自己完結した関数を作るのは不可能である。

```
You can use this same idea to do other things that are extremely ill-advised. For example, since all of the standard operators in R are functions, you can override them with your own alternatives.  If you ever are feeling particularly evil, run the following code while your friend is away from their computer:
```

これはちょっとした意地悪にも応用できる。例えば、Rにおける基本演算子は全て関数であり、独自定義でオーバーライドすることができる。もし読者が意地悪したくなった時は、友人がコンピュータから離れた隙に以下のコードを実行すると良い。


```r
`(` <- function(e1) {
  if (is.numeric(e1) && runif(1) < 0.1) {
    e1 + 1
  } else {
    e1
  }
}
replicate(50, (1 + 2))
```

```
##  [1] 3 3 3 3 3 4 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 4 3 3 3
## [36] 3 3 3 3 3 3 3 3 3 3 4 3 3 3 3
```

```r
rm("(")
```

```
This will introduce a particularly pernicious bug: 10% of the time, 1 will be added to any numeric calculation inside parentheses. This is another good reason to regularly restart with a clean R session!
```
このコードを実行すると、非常に悪質なバグをもたらす。10回に1回、カッコに囲まれたあらゆる演算結果に対して1が加えられた結果を返す。こういうことがあるので、Rのセッションは常にまっさらの状態から始めるようにしておいた方が良い。

### エクササイズ(Exercises)

```
1. What does the following code return? Why? What does each of the three `c`'s mean?
```

1. 以下のコードはどのような結果を返すだろうか? なぜそう思うか理由も述べよ? 3つの`c`のそれぞれが意味するものも併せて答えよ。

    
    ```r
    c <- 10
    c(c = c)
    ```

```
2. What are the four principles that govern how R looks for values?
```

2. Rが値を探す時に従う4つの原則とはなにか?

```
3. What does the following function return? Make a prediction before 
   running the code yourself.
```

3. 以下のコードはどのような結果を返すか? コードを実行する前にその結果を予想してみてほしい。
    
    ```r
    f <- function(x) {
      f <- function(x) {
        f <- function(x) {
          x ^ 2
        }
        f(x) + 1
      }
      f(x) * 2
    }
    f(10)
    ```

## 全ての操作は関数呼び出しである(Every operation is a function call) {#all-calls}

```
> "To understand computations in R, two slogans are helpful:
>
> * Everything that exists is an object.
> * Everything that happens is a function call."
>
> --- John Chambers
```

> "Rにおける演算を理解するには以下の2つを覚えておくとよい:
>
> * 全てはオブジェクトとして存在する。
> * R内で起きること全ては関数呼び出しである。"
>
> --- John Chambers

```
The previous example of redefining `(` works because every operation in R is a function call, whether or not it looks like one. This includes infix operators like `+`, control flow operators like `for`, `if`, and `while`, subsetting operators like `[]` and `$`, and even the curly brace `{`. This means that each pair of statements in the following example is exactly equivalent.  Note that `` ` ``, the backtick, lets you refer to functions or variables that have otherwise reserved or illegal names: \index{reserved names} \indexc{`} \index{backticks|see{\texttt{`}}}
```

先の例(`(`の再定義)は、Rにおける全ての操作が関数呼び出しである(一見そうは見えなくても)からできることであったこれはinfix演算子である`+`やcontrol flow演算子である`for`や`if`、`while`、データ抽出演算子である`[]`や`$`、そして中カッコの`{`についてもいえることである。これは以下に示すコード例がそれぞれ等価であることを意味している。なお、例の中で用いているバックティック`` ` ``は予約語として登録されている、または不正な名前になる関数や変数を参照するためのものである。 \index{reserved names} \indexc{`} \index{backticks|see{\texttt{`}}}


```r
x <- 10; y <- 5
x + y
```

```
## [1] 15
```

```r
`+`(x, y)
```

```
## [1] 15
```

```r
for (i in 1:2) print(i)
```

```
## [1] 1
## [1] 2
```

```r
`for`(i, 1:2, print(i))
```

```
## [1] 1
## [1] 2
```

```r
if (i == 1) print("yes!") else print("no.")
```

```
## [1] "no."
```

```r
`if`(i == 1, print("yes!"), print("no."))
```

```
## [1] "no."
```

```r
x[3]
```

```
## [1] NA
```

```r
`[`(x, 3)
```

```
## [1] NA
```

```r
{ print(1); print(2); print(3) }
```

```
## [1] 1
## [1] 2
## [1] 3
```

```r
`{`(print(1), print(2), print(3))
```

```
## [1] 1
## [1] 2
## [1] 3
```

```
It is possible to override the definitions of these special functions, but this is almost certainly a bad idea. However, there are occasions when it might be useful: it allows you to do something that would have otherwise been impossible. For example, this feature makes it possible for the `dplyr` package to translate R expressions into SQL expressions. [Domain specific languages](#dsl) uses this idea to create domain specific languages that allow you to concisely express new concepts using existing R constructs.
```

これらの特殊関数の定義のオーバーライドは可能であるが、あまり良い考えとはいえない。しかし、有用な場合もあり、オーバーライドなしには実現できないようなこともある。例えば、`dplyr`パッケージの中でRの式をSQLの式に翻訳する際にこのテクニックは使われている。[Domain specific languages](#dsl)の章ではこのテクニックを用いて、DSL(Domain Specific Language)を作っている。こうすることでRの既存の構造物を利用して新しいコンセプトの表現が実現できている。

```
It's more often useful to treat special functions as ordinary functions. For example, we could use `sapply()` to add 3 to every element of a list by first defining a function `add()`, like this: \indexc{sapply()}
```

オーバーライドしてしまうより、特殊関数を通常の関数として扱うことの方がケースとしては多い。例えば、`add()`という関数を定義して、それを`sapply()`に渡し、リストの各要素に3ずつ加える場合を考えてみよう。


```r
add <- function(x, y) x + y
sapply(1:10, add, 3)
```

```
##  [1]  4  5  6  7  8  9 10 11 12 13
```

```
But we can also get the same effect using the built-in `+` function.
```

この例は以下のように組み込みの`+`を通常の関数と同じように用いることでわざわざ`add()`を定義する手間が省ける。


```r
sapply(1:5, `+`, 3)
```

```
## [1] 4 5 6 7 8
```

```r
sapply(1:5, "+", 3)
```

```
## [1] 4 5 6 7 8
```

```
Note the difference between `` `+` `` and `"+"`.  The first one is the value of the object called `+`, and the second is a string containing the character `+`.  The second version works because `lapply` can be given the name of a function instead of the function itself: if you read the source of `lapply()`, you'll see the first line uses `match.fun()` to find functions given their names.
```

なお、`+`と`"+"`の違いに注意してほしい。前者は関数としての`+`に結びつけられた値が呼び出されるが、後者は文字列としての`+`に結びつけられた値つまり+という文字列が呼び出される。では上記の例において、なぜ`sapply()`に渡す関数として`"+"`を指定した場合でも動いているのだろうか。それは`sapply()`に対して関数そのものの代わりに関数の名前を渡すようになっているからである。

```
A more useful application is to combine `lapply()` or `sapply()` with subsetting:
```

より有用な応用例として`lapply()`もしくは`sapply()`とデータ抽出を組み合わせるというものがある。


```r
x <- list(1:3, 4:9, 10:12)
sapply(x, "[", 2)
```

```
## [1]  2  5 11
```

```r
# equivalent to
sapply(x, function(x) x[2])
```

```
## [1]  2  5 11
```

```
Remembering that everything that happens in R is a function call will help you in [metaprogramming](#metaprogramming).
```

なお、Rにおいて起きる全てのことは関数呼び出しであるという事実を覚えておくと、この後の[metaprogramming](#metaprogramming)の章の理解の助けとなるだろう。

## 関数の引数(Function arguments) {#function-arguments}

````
It's useful to distinguish between the formal arguments and the actual arguments of a function. The formal arguments are a property of the function, whereas the actual or calling arguments can vary each time you call the function. This section discusses how calling arguments are mapped to formal arguments, how you can call a function given a list of arguments, how default arguments work, and the impact of lazy evaluation.
```

関数の仮引数と実引数の区別をつけておくことは有用である。仮引数は関数のプロパティである一方、実引数(もしくは呼び出し時引数)は関数が呼び出されるたびに変わる。この節では実引数が仮引数にどのようにマップされているか、どのようにして関数に引数リストを渡して関数を呼び出すか、デフォルトの引数はどのように機能するか、遅延評価のインパクトとはどのようなものか、について議論する。

### 関数の呼び出し(Calling functions)

```
When calling a function you can specify arguments by position, by complete name, or by partial name. Arguments are matched first by exact name (perfect matching), then by prefix matching, and finally by position. \index{functions!arguments}
```

関数を呼び出すとき、その引数は位置、名前(場合によっては部分的な名前)で特定する。引数はまず正確な名前(perfect matching)でマッチされ、その次のプレフィックスマッチ(prefix matching)、最後に位置でのマッチングという順で実行される。 \index{functions!arguments}


```r
f <- function(abcdef, bcde1, bcde2) {
  list(a = abcdef, b1 = bcde1, b2 = bcde2)
}
str(f(1, 2, 3))
```

```
## List of 3
##  $ a : num 1
##  $ b1: num 2
##  $ b2: num 3
```

```r
str(f(2, 3, abcdef = 1))
```

```
## List of 3
##  $ a : num 1
##  $ b1: num 2
##  $ b2: num 3
```

```r
# 長い名前の引数は短縮できる
# Can abbreviate long argument names:
str(f(2, 3, a = 1))
```

```
## List of 3
##  $ a : num 1
##  $ b1: num 2
##  $ b2: num 3
```

```r
# ただし以下の場合は短縮すると曖昧なためエラーになる
# But this doesn't work because abbreviation is ambiguous
str(f(1, 3, b = 1))
```

```
## Error in f(1, 3, b = 1):  引数 3 が複数の仮引数に一致します
```

```
Generally, you only want to use positional matching for the first one or two arguments; they will be the most commonly used, and most readers will know what they are. Avoid using positional matching for less commonly used arguments, and only use readable abbreviations with partial matching. (If you are writing code for a package that you want to publish on CRAN you can not use partial matching, and must use complete names.) Named arguments should always come after unnamed arguments. If a function uses `...` (discussed in more detail below), you can only specify arguments listed after `...` with their full name.
```

一般的に、１-2つ目の引数については位置によるマッチングを選ぶだろう。これらの引数はよく使われるものであり、多くの読者もその引数が意味するものを知っているからである。しかし、あまり使われない引数に対しては位置によるマッチングを用いることを避け、部分的なマッチングと併せて可読的な短縮形を用いるべきである。(CRANに公開するパッケージのコードを書く場合は、部分的なマッチを使わずに、完全な名前による引数指定をすべきである。)名前付き引数は、名前なし引数の後に置くべきである。もし関数の中で`...`(これについては後ほど詳しく述べる)を用いる場合は、その後にくる引数についてはフルネームで指定する必要がある。

```
These are good calls:
```

以下は良い関数呼び出しの例である。


```r
mean(1:10)
mean(1:10, trim = 0.05)
```

```
This is probably overkill:
```

これは不必要に書き過ぎている。


```r
mean(x = 1:10)
```

```
And these are just confusing:
```

これらは混乱を招く。


```r
mean(1:10, n = T)
mean(1:10, , FALSE)
mean(1:10, 0.05)
mean(, TRUE, x = c(1:10, NA))
```

### 引数リストを渡した場合の関数呼び出し(Calling a function given a list of arguments)

```
Suppose you had a list of function arguments: \indexc{do.call()}
```

関数の引数リストがあると仮定する。 \indexc{do.call()}


```r
args <- list(1:10, na.rm = TRUE)
```

```
How could you then send that list to `mean()`?  You need `do.call()`:
```

このリストをどのように`mean()`に渡したらよいだろう? この時、`do.call()`を用いる。


```r
do.call(mean, list(1:10, na.rm = TRUE))
```

```
## [1] 5.5
```

```r
# 以下と等価である
# Equivalent to
mean(1:10, na.rm = TRUE)
```

```
## [1] 5.5
```

### デフォルト引数および引数の欠損(Default and missing arguments)

```
Function arguments in R can have default values. \index{functions!default values}
```

Rにおける関数の引数はデフォルトの値を持てる。 \index{functions!default values}



```r
f <- function(a = 1, b = 2) {
  c(a, b)
}
f()
```

```
## [1] 1 2
```

```
Since arguments in R are evaluated lazily (more on that below), the default value can be defined in terms of other arguments:
```

Rにおける引数は遅延評価される(これについては後ほど述べる)ため、引数の初期値は他の引数を用いて評価することもできる。


```r
g <- function(a = 1, b = a * 2) {
  c(a, b)
}
g()
```

```
## [1] 1 2
```

```r
g(10)
```

```
## [1] 10 20
```

```
Default arguments can even be defined in terms of variables created within the function. This is used frequently in base R functions, but I think it is bad practice, because you can't understand what the default values will be without reading the complete source code.
```

デフォルト引数は関数の中で生成された変数に入れて評価することさえ可能である。これはRのbaseパッケージの関数ではよく使われているテクニックだが、個人的には悪い習慣であると思われる。なぜならデフォルト引数として与えられた値がどうなるか、ソースコードを全て読むまで理解できないからである。


```r
h <- function(a = 1, b = d) {
  d <- (a + 1) ^ 2
  c(a, b)
}
h()
```

```
## [1] 1 4
```

```r
h(10)
```

```
## [1]  10 121
```

```
You can determine if an argument was supplied or not with the `missing()` function. \indexc{missing()}
```

`missing()`によって、引数が未指定かどうかを判定することができる。 \indexc{missing()}


```r
i <- function(a, b) {
  c(missing(a), missing(b))
}
i()
```

```
## [1] TRUE TRUE
```

```r
i(a = 1)
```

```
## [1] FALSE  TRUE
```

```r
i(b = 2)
```

```
## [1]  TRUE FALSE
```

```r
i(1, 2)
```

```
## [1] FALSE FALSE
```

```
Sometimes you want to add a non-trivial default value, which might take several lines of code to compute. Instead of inserting that code in the function definition, you could use `missing()` to conditionally compute it if needed. However, this makes it hard to know which arguments are required and which are optional without carefully reading the documentation. Instead, I usually set the default value to `NULL` and use `is.null()` to check if the argument was supplied.
```

少なくない数の初期値を設定した場合、実行に数行費やす場合もあるだろう。そのような事態を避けるためにも、初期値は引数に与えるのではなく、`missing()`を用いて必要な時にのみ実行するように変更すると良い。しかし、こうしてしまうと、ドキュメントを注意深く読まない限り、どの引数が必須のもので、どの引数がオプションなのかがわかりにくくなってしまう。筆者はそのような場合への対応として、初期値に`NULL`を与え、引数の有無をチェックするには`is.null()`を用いている。

### 遅延評価(Lazy evaluation) {#lazy-evaluation}

```
By default, R function arguments are lazy --- they're only evaluated if they're actually used: \index{lazy evaluation} \index{functions!lazy evaluation}
```

Rの関数における引数は遅延評価される。つまり、その引数が必要とされる時になって評価される。 \index{lazy evaluation} \index{functions!lazy evaluation}


```r
f <- function(x) {
  10
}
f(stop("This is an error!"))
```

```
## [1] 10
```

```
If you want to ensure that an argument is evaluated you can use `force()`: \indexc{force()}
```

もし、引数をすぐに評価したい場合は`force()`を用いるとよい。


```r
f <- function(x) {
  force(x)
  10
}
f(stop("This is an error!"))
```

```
## Error in force(x): This is an error!
```

```
This is important when creating closures with `lapply()` or a loop:
```

これは`lapply()`やループでもってクロージャを生成する時に重要なテクニックである。


```r
add <- function(x) {
  function(y) x + y
}
adders <- lapply(1:10, add)
adders[[1]](10)
```

```
## [1] 20
```

```r
adders[[10]](10)
```

```
## [1] 20
```

```
`x` is lazily evaluated the first time that you call one of the adder functions. At this point, the loop is complete and the final value of `x` is 10.  Therefore all of the adder functions will add 10 on to their input, probably not what you wanted!  Manually forcing evaluation fixes the problem:
```

addersのいずれか(例えばadders[[1]])を呼び出して初めて、`x`は評価される。この時点で`lapply()`におけるループは終了しており、`x`の値は10である。したがって、このaddersは入力値に対して、常に10を足すという関数になってしまう。しかし期待していたのはadders[[1]]なら1を足し、adders[[10]]ならば10を足すという結果である。この問題には強制評価を行うことで対処する。


```r
add <- function(x) {
  force(x)
  function(y) x + y
}
adders2 <- lapply(1:10, add)
adders2[[1]](10)
```

```
## [1] 11
```

```r
adders2[[10]](10)
```

```
## [1] 20
```

```
This code is exactly equivalent to
```

このコードは下記コードと等価である。


```r
add <- function(x) {
  x
  function(y) x + y
}
```

```
because the force function is defined as `force <- function(x) x`. However, using this function clearly indicates that you're forcing evaluation, not that you've accidentally typed `x`.
```

なぜなら`force()`は、`force <- function(x) x`と定義されているからである。しかし、この関数を使うことで、タイプミスで`x`と書いたのではなく、強制評価させたいという意思表示ができる。

```
Default arguments are evaluated inside the function. This means that if the expression depends on the current environment the results will differ depending on whether you use the default value or explicitly provide one.
```

デフォルト引数は関数の中で評価される。これはデフォルト引数として与える式が環境に依存するものである場合(以下の例における`ls()`)、デフォルト引数として与えられたか、それとも明示的に指定したかによって結果が異なってくることを意味する。


```r
f <- function(x = ls()) {
  a <- 1
  x
}

# ls()はfの中で評価される
# ls() evaluated inside f:
f()
```

```
## [1] "a" "x"
```

```r
# ls()は大域的環境において評価される
# ls() evaluated in global environment:
f(ls())
```

```
##  [1] "add"     "adders"  "adders2" "args"    "f"       "fn"      "funs"   
##  [8] "g"       "h"       "i"       "objs"    "trgrow"  "x"       "y"
```

```
More technically, an unevaluated argument is called a __promise__, or (less commonly) a thunk. A promise is made up of two parts: \index{promises} \index{thunks|see{promises}}
```

さらに踏み込んだ解説をすると、未評価の引数は__プロミス__もしくはあまり一般的ではないがサンクと呼ばれる。プロミスは以下の2つで構成されている。 \index{promises} \index{thunks|see{promises}}

```
* The expression which gives rise to the delayed computation. (It can be accessed with `substitute()`. See [non-standard evaluation](#nse) for more details.)
```

* 遅延評価の対象となる式(これは`substitute()`でアクセスできる)。詳しくは[non-standard evaluation](#nse)を参照のこと。

```
* The environment where the expression was created and where it should be evaluated.
```

* 式が生成された環境および式が評価されるべき環境

```
The first time a promise is accessed the expression is evaluated in the environment where it was created. This value is cached, so that subsequent access to the evaluated promise does not recompute the value (but the original expression is still associated with the value, so `substitute()` can continue to access it). You can find more information about a promise using `pryr::promise_info()`. This uses some C++ code to extract information about the promise without evaluating it, which is impossible to do in pure R code.
```

プロミスに初めてアクセスした時、式は生成された環境において評価される。この値はキャッシュされ、その後のアクセスに対して改めて評価するようなことはない(しかし、元の式は値に紐づいているので、`substitute()`はその値に対して継続してアクセスすることができる)。プロミスについてより情報が欲しいなら`pryr::promise_info()`を使うと良い。この関数は従来のRコードではアクセスできないプロミスについての情報を取り出すためにC++を用いている。

```
Laziness is useful in if statements --- the second statement below will be evaluated only if the first is true. If it wasn't, the statement would return an error because `NULL > 0` is a logical vector of length 0 and not a valid input to `if`. \indexc{if}
```

遅延評価は、if文において有用である。例えば下記コードにおいて、2つ目の引数は遅延評価により1つ目の引数が真の時のみ評価される。もし遅延評価をしない場合、このif文はエラーを返す。なぜなら`NULL > 0`は長さ0の論理値ベクトルであり、`if`の入力値としては適切ではないからである。 \indexc{if}


```r
x <- NULL
if (!is.null(x) && x > 0) {

}
```

```
We could implement "&&" ourselves:
```

ここであらためて"&&"を実装してみよう。


```r
`&&` <- function(x, y) {
  if (!x) return(FALSE)
  if (!y) return(FALSE)

  TRUE
}
a <- NULL
!is.null(a) && a > 0
```

```
## [1] FALSE
```

```
This function would not work without lazy evaluation because both `x` and `y` would always be evaluated, testing `a > 0` even when `a` was NULL.
```

この関数は遅延評価なしには機能しない。なぜなら`x`と`y`の両者ともに常に評価されるからである。`a `がNULLであっても`a > 0 `がテストされる。

```
Sometimes you can also use laziness to eliminate an if statement altogether. For example, instead of:
```

遅延評価を用いてif文を無くしてしまうこともできる。例えば下記コードのように書く代わりに


```r
if (is.null(a)) stop("a is null")
```

```
## Error in eval(expr, envir, enclos): a is null
```

こう書くこともできる。


```r
!is.null(a) || stop("a is null")
```

```
## Error in eval(expr, envir, enclos): a is null
```

### `...`

```
There is a special argument called `...` .  This argument will match any arguments not otherwise matched, and can be easily passed on to other functions.  This is useful if you want to collect arguments to call another function, but you don't want to prespecify their possible names. `...` is often used in conjunction with S3 generic functions to allow individual methods to be more flexible. \indexc{...}
```

特殊な引数として`...`がある。これはマッチしなかった任意の引数にマッチするものであり、他の関数に容易に渡すことができる。これは事前に指定しづらい引数をまとめて、他の関数に渡す時に便利である。`...`はS3のジェネリック関数と組み合わせて用いることで、個々のメソッドをより柔軟にすることができる。

```
One relatively sophisticated user of `...` is the base `plot()` function. `plot()` is a generic method with arguments `x`, `y` and `...` . To understand what `...` does for a given function we need to read the help: "Arguments to be passed to methods, such as graphical parameters". Most simple invocations of `plot()` end up calling `plot.default()` which has many more arguments, but also has `...` .  Again, reading the documentation reveals that `...` accepts "other graphical parameters", which are listed in the help for `par()`.  This allows us to write code like:
```

`...`をうまく使っている例として`plot()`が挙げられる。`plot()`は`x`、`y`および`...`を引数にもつジェネリック関数である。この関数における`...`の役割を理解するにはヘルプを読む必要がある。ヘルプにはこのように書いてある。「`...`はグラフィカルパラメータといった他のメソッドに渡される」。もっともシンプルな形で`plot()`を呼び出す方法は`plot.default()`を呼び出すことである。これは`plot()`よりも多くの引数をもつだけでなく、`...`ももつ。なお、`...`を介して渡される他のグラフィカルパラメータは、`par()`のヘルプにリストアップされている。これを用いて以下のようなコードが書ける。



```r
plot(1:5, col = "red")
plot(1:5, cex = 5, pch = 20)
```

```
This illustrates both the advantages and disadvantages of `...`: it makes `plot()` very flexible, but to understand how to use it, we have to carefully read the documentation. Additionally, if we read the source code for `plot.default`, we can discover undocumented features. It's possible to pass along other arguments to `Axis()` and `box()`:
```

このコードは`...`の宵面と悪い面を表している。`...`は`plot()`をフレキシブルなものとする一方、使い方を理解しようとするとドキュメントを注意深く読まなくてはならない。しかも、`plot.default()`のソースコードを読むとドキュメント化されていない部分があることに気づく。例えば、以下のように他の引数を`Axis()`および`box()`に引き渡すことも実は可能である。


```r
plot(1:5, bty = "u")
plot(1:5, labels = FALSE)
```

```
To capture `...` in a form that is easier to work with, you can use `list(...)`. (See [capturing unevaluated dots](#capturing-dots) for other ways to capture `...` without evaluating the arguments.)
```
`...`をより使いやすい形で取り出すには、`list(...)`を用いるとよい。
(`...`を未評価のまま取り出す他の方法については[capturing unevaluated dots](#capturing-dots)を参照のこと。)


```r
f <- function(...) {
  names(list(...))
}
f(a = 1, b = 2)
```

```
## [1] "a" "b"
```

```
Using `...` comes at a price --- any misspelled arguments will not raise an error, and any arguments after `...` must be fully named.  This makes it easy for typos to go unnoticed:
```

`...`を使うにはそれなりの代償を必要とする。引数をスペルミスしてもエラーは出ず、また`...`の後にくる引数は名前付きのものでなくてはならない。これは気づかれることの無いタイプミスを招く。


```r
sum(1, 2, NA, na.mr = TRUE)
```

```
## [1] NA
```

```
It's often better to be explicit rather than implicit, so you might instead ask users to supply a list of additional arguments. That's certainly easier if you're trying to use `...` with multiple additional functions.
```

暗黙の指定よりも、明示的な指定の方が往々にして望ましい。したがって、追加する引数のリストを渡すような形で関数は作るべきである。複数の追加関数に対して`...`を用いようとするのであればそれがより簡単な方法といえる。

### エクササイズ(Exercises)

```
1.  Clarify the following list of odd function calls:
```

以下の関数呼び出しにおける奇妙な点を挙げよ。

    
    ```r
    x <- sample(replace = TRUE, 20, x = c(1:10, NA))
    y <- runif(min = 0, max = 1, 20)
    cor(m = "k", y = y, u = "p", x = x)
    ```

```
1.  What does this function return? Why? Which principle does it illustrate?
```

以下の関数はどのような結果を返すか? どのような原則を表しているか?

    
    ```r
    f1 <- function(x = {y <- 1; 2}, y = 0) {
      x + y
    }
    f1()
    ```

```
1.  What does this function return? Why? Which principle does it illustrate?
```

以下の関数はどのような結果を返すか? どのような原則を表しているか?

    
    ```r
    f2 <- function(x = z) {
      z <- 100
      x
    }
    f2()
    ```

## 特殊な関数呼び出し(Special calls) {#special-calls}

```
R supports two additional syntaxes for calling special types of functions: infix and replacement functions.
```

Rでは特殊な関数呼び出しとして2つの仕組みを備えている。infix関数とreplacement関数である。

### infix関数(Infix functions) {#infix-functions}

```
Most functions in R are "prefix" operators: the name of the function comes before the arguments. You can also create infix functions where the function name comes in between its arguments, like `+` or `-`.  All user-created infix functions must start and end with `%`. R comes with the following infix functions predefined: `%%`, `%*%`, `%/%`, `%in%`, `%o%`,  `%x%`. (The complete list of built-in infix operators that don't need `%` is: `::, :::, $, @, ^, *, /, +, -, >, >=, <, <=, ==, !=, !, &, &&, |, ||, ~, <-, <<-`) \index{functions!infix} \index{infix functions} \indexc{\%\%}
```

Rにおける多くの関数は"prefix"タイプの演算子である。つまり、関数の名称が引数の前にくる。ここで、Rにおいて、`+`や`-`のように引数の間に関数の名称がくる"infix"タイプの関数を生成することもできる。ユーザが定義したinfix関数は`%`で関数の名称をはさむことになっている。このような形であらかじめ定義された関数として以下のものがある(`%%`、`%*%`、`%/%`、`%in%`、`%o%`、`%x%`)。なお、`%`を必要としない組み込みのinfix演算子として、`::, :::, $, @, ^, *, /, +, -, >, >=, <, <=, ==, !=, !, &, &&, |, ||, ~, <-, <<-`が挙げられる。 \index{functions!infix} \index{infix functions} \indexc{\%\%}

```
For example, we could create a new operator that pastes together strings:
```

例えば、文字列を結合する演算子として以下のようなものを新しく定義してみよう。


```r
`%+%` <- function(a, b) paste0(a, b)
"new" %+% " string"
```

```
## [1] "new string"
```

```
Note that when creating the function, you have to put the name in backticks because it's a special name. This is just a syntactic sugar for an ordinary function call; as far as R is concerned there is no difference between these two expressions:
```

ここで二項演算子を定義する際、Rに通常の文字列とは異なることを認識させるためにバックティックで囲むことを忘れてはいけない。%を用いた二項演算子はあくまで関数呼び出しにおける糖衣構文であり、Rが認識する限り、以下の例のようにバックティックを用いて通常の関数呼び出しを行うこともできる。


```r
"new" %+% " string"
```

```
## [1] "new string"
```

```r
`%+%`("new", " string")
```

```
## [1] "new string"
```

```
Or indeed between \indexc{`}
```

%を用いない二項演算子についても同じことがいえる。


```r
1 + 5
```

```
## [1] 6
```

```r
`+`(1, 5)
```

```
## [1] 6
```

```
The names of infix functions are more flexible than regular R functions: they can contain any sequence of characters (except "%", of course). You will need to escape any special characters in the string used to define the function, but not when you call it:
```

二項演算子の名称は通常のRの関数よりも柔軟性があり、"%"を除くあらゆる文字列を用いることができる。なお、関数定義の際は、特殊文字についてはエスケープ文字を用いる必要があるが、関数呼び出しの際はそのようなエスケープ文字は必要ではない。


```r
`% %` <- function(a, b) paste(a, b)
`%'%` <- function(a, b) paste(a, b)
`%/\\%` <- function(a, b) paste(a, b)

"a" % % "b"
```

```
## [1] "a b"
```

```r
"a" %'% "b"
```

```
## [1] "a b"
```

```r
"a" %/\% "b"
```

```
## [1] "a b"
```

```
R's default precedence rules mean that infix operators are composed from left to right:
```

デフォルトでは二項演算子は左から右に評価される。


```r
`%-%` <- function(a, b) paste0("(", a, " %-% ", b, ")")
"a" %-% "b" %-% "c"
```

```
## [1] "((a %-% b) %-% c)"
```

```
There's one infix function that I use very often. It's inspired by Ruby's `||` logical or operator, although it works a little differently in R because Ruby has a more flexible definition of what evaluates to `TRUE` in an if statement. It's useful as a way of providing a default value in case the output of another function is `NULL`:
```

筆者がよく使う二項演算子を紹介しよう。これはRuby言語の`||`演算子に触発されて作ったものである。Rubyにおける`||`はif文における`TRUE`の評価についてより柔軟な定義がされており、Rの`||`とは挙動が異なる。以下の例のように、独自に`%||%`を定義して、一方の関数の出力が`NULL`だった場合、あらかじめ指定した初期値を返すようにしておくと便利である。


```r
`%||%` <- function(a, b) if (!is.null(a)) a else b
function_that_might_return_null() %||% default value
```

### Replacement functions {#replacement-functions}

```
Replacement functions act like they modify their arguments in place, and have the special name `xxx<-`. They typically have two arguments (`x` and `value`), although they can have more, and they must return the modified object. For example, the following function allows you to modify the second element of a vector: \index{replacement functions} \index{functions!replacement}
```

replacement関数はその引数をその場で変更するような挙動をする。また、`xxx<-`といった特殊な名前をもつ。引数は複数とることができるが`x`と`value`という2つの引数をもつことが多く、返り値として変更されたオブジェクトを返す。例えば以下の関数を用いると引数として指定したベクトルの2つ目の要素を変更する。 \index{replacement functions} \index{functions!replacement}


```r
`second<-` <- function(x, value) {
  x[2] <- value
  x
}
x <- 1:10
second(x) <- 5L
x
```

```
##  [1]  1  5  3  4  5  6  7  8  9 10
```

```
When R evaluates the assignment `second(x) <- 5`, it notices that the left hand side of the `<-` is not a simple name, so it looks for a function named `second<-` to do the replacement. \index{assignment!replacement functions}
```

Rは`second(x) <- 5`を評価するとき、`<-`の左側はただのオブジェクトの名前ではないことを把握する。そして`<-`の代わりに`second<-`という名前の関数がないか検索する。

```
I say they "act" like they modify their arguments in place, because they actually create a modified copy. We can see that by using `pryr::address()` to find the memory address of the underlying object.
```

先ほどreplacement関数は引数をその場で変更するような"挙動をする"と述べたが、実際は変更済みのコピーを作る。以下の例では`pryr::address()`を用いてオブジェクトのメモリアドレスを確認する。


```r
library(pryr)
x <- 1:10
address(x)
```

```
## [1] "0x7fa5f5cbb740"
```

```r
second(x) <- 6L
address(x)
```

```
## [1] "0x7fa5f5c922f0"
```

```
Built-in functions that are implemented using `.Primitive()` will modify in place: \index{primitive functions}
```

`.Primitive()`を用いた組み込み関数はコピーではなく実際にオブジェクトをその場で変更する挙動をする。 \index{primitive functions}


```r
x <- 1:10
address(x)
#> [1] "0x103945110"

x[2] <- 7L
address(x)
#> [1] "0x103945110"
```

```
It's important to be aware of this behaviour since it has important performance implications.
```

パフォーマンスにおいて重要な意味合いをもっているため、この挙動を理解しておくことは重要である。

```
If you want to supply additional arguments, they go in between `x` and `value`:
```

引数を加える際は、`x`と`value`の間に加える


```r
`modify<-` <- function(x, position, value) {
  x[position] <- value
  x
}
modify(x, 1) <- 10
x
```

```
##  [1] 10  6  3  4  5  6  7  8  9 10
```

```
When you call `modify(x, 1) <- 10`, behind the scenes R turns it into:
```

`modify(x, 1) <- 10`を実行すると、その裏ではRは以下のようなコードを実行している。


```r
x <- `modify<-`(x, 1, 10)
```

```
This means you can't do things like:
```

これは以下のようなコードは実行できないことを意味する。


```r
modify(get("x"), 1) <- 10
```

```
because that gets turned into the invalid code:
```

なぜなら上記コードは以下のコードと同義であり、これはエラーになるからである。


```r
get("x") <- `modify<-`(get("x"), 1, 10)
```

```
It's often useful to combine replacement and subsetting:
```

replacement関数はデータ抽出関数と組み合わせると使い勝手が良い。


```r
x <- c(a = 1, b = 2, c = 3)
names(x)
```

```
## [1] "a" "b" "c"
```

```r
names(x)[2] <- "two"
names(x)
```

```
## [1] "a"   "two" "c"
```

```
This works because the expression `names(x)[2] <- "two"` is evaluated as if you had written:
```
このコードは正常に実行できる。なぜなら`names(x)[2] <- "two"`は以下のコードと等価であるからだ。


```r
`*tmp*` <- names(x)
`*tmp*`[2] <- "two"
names(x) <- `*tmp*`
```

```
(Yes, it really does create a local variable named `*tmp*`, which is removed afterwards.)
```

(実際に、ローカル変数として*tmp*は生成されており、処理が終わった後消去されている。)

### エクササイズ(Exercises)

```
1. Create a list of all the replacement functions found in the base package. Which ones are primitive functions?
```

1. baseパッケージ内のreplacement関数をリストアップせよ。また、どれがprimitive関数だろうか?

```
2. What are valid names for user-created infix functions?
```

2. ユーザー定義の二項演算子はどのように定義すればよいか?

```
3. Create an infix `xor()` operator.
```

3. `xor()`演算子を定義せよ。

```
4. Create infix versions of the set functions `intersect()`, `union()`, and `setdiff()`.
```

`intersect()`、`union()`、`setdiff()`といった集合演算子と同じ挙動を示す演算子を定義せよ。


```
5. Create a replacement function that modifies a random location in a vector.
```

引数として与えられたベクトルにおいてランダムな位置で値を変更するreplacement関数を定義せよ。

## 返り値(Return values) {#return-values}

```
The last expression evaluated in a function becomes the return value, the result of invoking the function. \index{functions!return value}
```

関数内で最後に評価された式は返り値、つまり関数の実行結果となる。 \index{functions!return value}


```r
f <- function(x) {
  if (x < 10) {
    0
  } else {
    10
  }
}
f(5)
```

```
## [1] 0
```

```r
f(15)
```

```
## [1] 10
```

```
Generally, I think it's good style to reserve the use of an explicit `return()` for when you are returning early, such as for an error, or a simple case of the function. This style of programming can also reduce the level of indentation, and generally make functions easier to understand because you can reason about them locally. \indexc{return()}
```

筆者としては明示的に`return()`を用いるのはエラー処理の時など関数の浅い段階で値を返すとき、もしくは簡単な関数ときに限った方が良いと考える。このプログラミングスタイルをとると、インデントが深くなるのを避けることができる。また関数に局所的に意味をもたせることができるので、理解しやすいコードになるという利点がある。


```r
f <- function(x, y) {
  if (!x) return(y)

  # この後に複雑な処理が続く
  # complicated processing here
}
```

```
Functions can return only a single object. But this is not a limitation because you can return a list containing any number of objects.
```

関数は単体のオブジェクトを返す。しかしこの制限はオブジェクトをリストに格納して返り値として返すことで回避できる。

```
The functions that are the easiest to understand and reason about are pure functions: functions that always map the same input to the same output and have no other impact on the workspace. In other words, pure functions have no __side effects__: they don't affect the state of the world in any way apart from the value they return. \index{pure functions}
```

最も理解しやすくその挙動を把握できるのは純粋関数、つまり同じ入力に対しては常に同じ出力を返し、ワークスペースに何の影響も及ぼさない関数である。言い換えると、純粋関数は__副作用__の無い関数といえる。純粋関数はその返り値以外、世界の状態に何の影響も及ぼさない。 \index{pure functions}

```
R protects you from one type of side effect: most R objects have copy-on-modify semantics. So modifying a function argument does not change the original value: \index{copy-on-modify}
```

多くのRオブジェクトはcopy-on-modifyセマンティクスをもつということで副作用を防いでいる。つまり関数の引数の変更はその元の値を変化させない。


```r
f <- function(x) {
  x$a <- 2
  x
}
x <- list(a = 1)
f(x)
```

```
## $a
## [1] 2
```

```r
x$a
```

```
## [1] 1
```

```
(There are two important exceptions to the copy-on-modify rule: environments and reference classes. These can be modified in place, so extra care is needed when working with them.)
```

(copy-on-modfiyルールには2つの重要な例外がある。環境とリファレンスクラスである。これらはその場で変更され、コピーは生じない。従って、これらを扱う時は少し注意が必要である。)

```
This is notably different to languages like Java where you can modify the inputs of a function. This copy-on-modify behaviour has important performance consequences which are discussed in depth in [profiling](#profiling). (Note that the performance consequences are a result of R's implementation of copy-on-modify semantics; they are not true in general. Clojure is a new language that makes extensive use of copy-on-modify semantics with limited performance consequences.)
```

これは、関数の引数を変更してしまうJavaなど他のプログラミング言語とは明らかに違っている点である。copy-on-modifyのパフォーマンス面の議論については[profiling](#profiling)の章で行う。(なお、このパフォーマンス面の結果はRにおけるcopy-on-modifyの実装の結果であり、copy-on-modifyのアルゴリズムとしての結果とはいえないことに注意してほしい。Clojureという新しい言語ではcopy-on-modifyを拡張して用いており、結果としてパフォーマンスが限定的なものとなっている。)

```
Most base R functions are pure, with a few notable exceptions:
```

Rの基本関数群の多くは純粋関数だが、いくつか押さえておきたい例外がある。

```
* `library()` which loads a package, and hence modifies the search path.
```

* `library()`はパッケージをロードするための関数であり、結果としてサーチパスを変更する。

```
* `setwd()`, `Sys.setenv()`, `Sys.setlocale()` which change the working directory, environment variables, and the locale, respectively.
```

* `setwd()`, `Sys.setenv()`, `Sys.setlocale()`はそれぞれ作業ディレクトリ、環境変数、ロケールを変更する関数である。

```
* `plot()` and friends which produce graphical output.
```

* `plot()`関数群はグラフィカル出力を生成する。

```
* `write()`, `write.csv()`, `saveRDS()`, etc. which save output to disk.
```

* `write()`, `write.csv()`, `saveRDS()`などは出力をディスクに保存する。

```
* `options()` and `par()` which modify global settings.
```

* `options()` and `par()`はグローバル設定を変更する。

```
* S4 related functions which modify global tables of classes and methods.
```

* S4クラスシステムに関連する関数群はクラスおよびメソッドのグローバルテーブルを変更する。

```
* Random number generators which produce different numbers each time you run them.
```

乱数生成は実行するたびに異なる数値を生成する。

```
It's generally a good idea to minimise the use of side effects, and where possible, to minimise the footprint of side effects by separating pure from impure functions. Pure functions are easier to test (because all you need to worry about are the input values and the output), and are less likely to work differently on different versions of R or on different platforms. For example, this is one of the motivating principles of ggplot2: most operations work on an object that represents a plot, and only the final `print` or `plot` call has the side effect of actually drawing the plot.
```

副作用の利用を必要最小限に抑え、可能な限り純粋関数と非純粋関数を分けて副作用の影響を小さくするのが一般的に良いアイデアとされている。純粋関数はテストが容易である。なぜなら入力値と出力値のみに気を配ればよいからである。また、Rの異なるバージョン、Rの異なる動作環境において異なる挙動を示す可能性は小さい。例えばggplot2の開発においては以下のような原則を設けている。「ggplot2におけるほとんどの操作はプロットを表現するオブジェクトにおいて行われる。そして`print`もしくは`plot`の呼び出しのみが実際のプロットを描画するという副作用をもつ。」

```
Functions can return `invisible` values, which are not printed out by default when you call the function. \indexc{invisible()} \index{functions!invisible results}
```

関数は`invisible`な値を返すこともできる。これは関数を呼び出したときにその結果をコンソールに出力しないというものである。 \indexc{invisible()} \index{functions!invisible results}


```r
f1 <- function() 1
f2 <- function() invisible(1)

f1()
```

```
## [1] 1
```

```r
f2()
f1() == 1
```

```
## [1] TRUE
```

```r
f2() == 1
```

```
## [1] TRUE
```

```
You can force an invisible value to be displayed by wrapping it in parentheses:
```

カッコで囲むことでinvisibleな値を強制的に表示させることもできる。


```r
(f2())
```

```
## [1] 1
```

```
The most common function that returns invisibly is `<-`: \index{assignment}
```

invisibleな値を返す関数の中でもっとも馴染みがあるのは`<-`だろう。\index{assignment}


```r
a <- 2
(a <- 2)
```

```
## [1] 2
```

```
This is what makes it possible to assign one value to multiple variables:
```

これにより、一つの値を複数の変数に代入することが可能になっている。


```r
a <- b <- c <- d <- 2
```

```
because this is parsed as:
```

実際にはこれは以下のようにパースされている。


```r
(a <- (b <- (c <- (d <- 2))))
```

```
## [1] 2
```

### 処理を抜ける際の処理(On exit) {#on-exit}

```
As well as returning a value, functions can set up other triggers to occur when the function is finished using `on.exit()`. This is often used as a way to guarantee that changes to the global state are restored when the function exits. The code in `on.exit()` is run regardless of how the function exits, whether with an explicit (early) return, an error, or simply reaching the end of the function body. \indexc{on.exit()}
```

返り値の他にも関数は、処理の終了時に`on.exit()`を用いて他の挙動を起こすことができる。これは関数がその処理を終えた時にglobal stateに対する変化を回復することを保証する方法としてしばしば用いられる。`on.exit()`内のコードは関数がどのような形(返り値を返す場合、エラーを起こした場合、関数定義の最後の行まで実行し終わった場合)で処理を終了しようとも実行される。


```r
in_dir <- function(dir, code) {
  old <- setwd(dir)
  on.exit(setwd(old))

  force(code)
}
getwd()
```

```
## [1] "/Users/ichikawadaisuke/Google ドライブ/univ/sinchoku"
```

```r
in_dir("~", getwd())
```

```
## [1] "/Users/ichikawadaisuke"
```

```
The basic pattern is simple:
```

シンプルな使用例を示した。

```
* We first set the directory to a new location, capturing the current location from the output of `setwd()`.
```

* まずディレクトリを新しい場所に設定し、その際`setwd()`の出力から変更前の作業ディレクトリを保存している。

```
* We then use `on.exit()` to ensure that the working directory is returned to the previous value regardless of how the function exits.
```

ここで`on.exit()`を用いており、関数がどのような形で終了しようとも、作業ディレクトリが関数実行前の場所にセットされるようにしている。

```
* Finally, we explicitly force evaluation of the code. (We don't actually need `force()` here, but it makes it clear to readers what we're doing.)
```

最後にコードを明示的に強制評価している(ここでわざわざ`force()`を用いる必要は無いが、コードの意図を明確に伝えるために用いている。)

```
**Caution**: If you're using multiple `on.exit()` calls within a function, make sure to set `add = TRUE`. Unfortunately, the default in `on.exit()` is `add = FALSE`, so that every time you run it, it overwrites existing exit expressions. Because of the way `on.exit()` is implemented, it's not possible to create a variant with `add = TRUE`, so you must be careful when using it.
```

**注意** `on.exit()`を一つの関数の中で複数回用いる際は、`add=TRUE`を引数にセットしておくこと。こうすることで、関数を実行するたびに既存の終了時実行式が上書きされていく。`on.exit()`の仕様により、`add=TRUE`とした`on.exit()`の派生関数を定義することはできないので、`on.exit()`を用いる際は、`add=TRUE`に注意しておいてほしい。

### エクササイズ(Exercises)

```
1.  How does the `chdir` parameter of `source()` compare to `in_dir()`? Why might you prefer one approach to the other?
```

1.  `source`関数の`chdir`パラメータは、先に定義した`in_dir()`と比較するとどのような違いがあるか? どちらのアプローチが良いと思うか?

```
1.  What function undoes the action of `library()`? How do you save and restore the values of `options()` and `par()`?
```

1. `library()` の実行結果を元に戻してしまう関数はなにか? どのようにすれば`options()`や`par()`の値を保存し、元の状態に回復させられるだろうか?

```
1.  Write a function that opens a graphics device, runs the supplied code, and closes the graphics device (always, regardless of whether or not the plotting code worked).
```

1.  グラフィックデバイスを開き、任意のコードを実行し、その終了結果にかかわらずグラフィックデバイスを閉じる関数を書きなさい。

```
1.  We can use `on.exit()` to implement a simple version of `capture.output()`.
```

1.  `capture.output()`関数の簡易版を作る際に`on.exit()`を用いることができる。

    
    ```r
    capture.output2 <- function(code) {
      temp <- tempfile()
      on.exit(file.remove(temp), add = TRUE)
    
      sink(temp)
      on.exit(sink(), add = TRUE)
    
      force(code)
      readLines(temp)
    }
    capture.output2(cat("a", "b", "c", sep = "\n"))
    ```
    
    ```
    ## [1] "a" "b" "c"
    ```

```
    Compare `capture.output()` to `capture.output2()`. How do the functions differ? What features have I removed to make the key ideas easier to see? How have I rewritten the key ideas to be easier to understand?
```

`capture.output()`と`capture.output2()`を比較しなさい。この2つの関数はどのように異なるか? `capture.output()`の鍵となるアイデアが明確にわかるよう筆者が省いた要素はなんだろうか? 結果としてどれだけわかりやすくなっているだろうか?

## クイズの解答(Quiz answers) {#function-answers}

\enlargethispage*{\baselineskip}

```
1.  The three components of a function are its body, arguments, and environment.
```

1.  3つの構成要素とはbodyとargumentとenvironmentである。

```
1.  `f1(1)()` returns 11.
```

1.  `f1(1)()`は11を返す。

```
1.  You'd normally write it in infix style: `1 + (2 * 3)`.
```

1.  二項演算子の形で書くと`1 + (2 * 3)`となる。

```
1.  Rewriting the call to `mean(c(1:10, NA), na.rm = TRUE)` is easier to understand.
```

1.  `mean(c(1:10, NA), na.rm = TRUE)`と書き直すとわかりやすい。

```    
1.  No, it does not throw an error because the second argument is never used so it's never evaluated.
```

1.  答えはいいえである。2つ目の引数は用いられないため評価されることはない。結果としてエラーを投げない。

```
1.  See [infix](#infix-functions) and 
    [replacement functions](#replacement-functions).
```

1.  [infix](#infix-functions) と [replacement functions](#replacement-functions)の項を見よ。

```
1.  You use `on.exit()`; see [on exit](#on-exit) for details.
```

1.  `on.exit()`を用いると良い。詳しくは[on exit](#on-exit)の項目を見よ。
