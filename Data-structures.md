---
title: 01_データ構造(Data structures)
output:
  html_document:
    keep_md: yes
---

# 01_データ構造(Data structures) {#data-structures}
```
This chapter summarises the most important data structures in base R. You've probably used many (if not all) of them before, but you may not have thought deeply about how they are interrelated. In this brief overview, I won't discuss individual types in depth. Instead, I'll show you how they fit together as a whole. If you need more details, you can find them in R's documentation.
```
本章ではRにおけるデータ構造(data structure)について概観する。読者はおそらくこれらのデータ構造の多くを使った経験があると思われるが、データ構造間の関係性について思いを馳せたことはないだろう。本章においては筆者は個々のデータ型(individual types)について深く議論するつもりはない。その代わりにデータ型間が全体としてどのような関係性をもっているか(how they fit together as a whole)について述べていこうと思う。もし詳細について知りたい場合は、Rの公式ドキュメント群を参照してほしい。

```
R's base data structures can be organised by their dimensionality (1d, 2d, or nd) and whether they're homogeneous (all contents must be of the same type) or heterogeneous (the contents can be of different types). This gives rise to the five data types most often used in data analysis: 

|    | Homogeneous   | Heterogeneous |
|----|---------------|---------------|
| 1d | Atomic vector | List          |
| 2d | Matrix        | Data frame    |
| nd | Array         |               |

Almost all other objects are built upon these foundations. In [the OO field guide](#oo) you'll see how more complicated objects are built of these simple pieces. Note that R has no 0-dimensional, or scalar types. Individual numbers or strings, which you might think would be scalars, are actually vectors of length one. 

Given an object, the best way to understand what data structures it's composed of is to use `str()`. `str()` is short for structure and it gives a compact, human readable description of any R data structure. \indexc{str()}
```
Rにおける基本データ構造はその次元数（１次元(1d)、２次元(2d)等）及び同一性(その構造に含まれる値が全て同じ型である必要があるか/違う型も許されるか)によって分類される。データ分析において多く用いられる5つのデータ型について下表に示した。

|    | 同じ型である必要がある   | 違う型も許される |
|----|---------------|---------------|
| 1d | アトム(Atomic vector) | リスト(List)          |
| 2d | 行列(Matrix)        | データフレーム(Data frame)    |
| nd | 配列(Array)         |               |

ほとんど全てのオブジェクトはこれらの基礎的な型のもと構築されている。詳しくは[the OO field guide](#oo)の章で述べるが、複雑な構造をもつオブジェクトもこれらの小さなピースで組み立てられている。なお、Rには0次元つまりスカラー型は無いことに注意しておきたい。一般的にスカラーとして考えられる個々の数値もしくは文字列(Individual numbers or strings)は、実際のところ長さが1のベクトルとして表現される。

１つのオブジェクトが与えられた時、そのオブジェクトに用いられているデータ構造を知るためには`str()`関数を用いる。`str()`はstructureの短縮形であり、Rのデータ構造について簡潔でまとまった人間が読める形の情報を返す。\indexc{str()}

##### 確認テスト(Quiz)
```
Take this short quiz to determine if you need to read this chapter. If the answers quickly come to mind, you can comfortably skip this chapter. You can check your answers in [answers](#data-structure-answers).

1. What are the three properties of a vector, other than its contents?

1. What are the four common types of atomic vectors? What are the two 
   rare types?

1. What are attributes? How do you get them and set them?

1. How is a list different from an atomic vector? How is a matrix different
   from a data frame?

1. Can you have a list that is a matrix? Can a data frame have a column 
   that is a matrix?
```
以下のテストは本章を読む必要があるかどうかを判定するテストである。答えがすぐに頭に浮かぶようであれば本章はとばしても良い。答えは[answers](#data-structure-answers)を参照のこと。

1. ベクトルの性質(property)を、その内容以外に3つ挙げよ。

1. アトム(atomic vector)において、よく用いられる型を4つ、稀に用いられる型を2つ挙げよ。

1. 属性(attribute)とは何か？属性情報はどのようにして取得し、どのように設定するか？

1. アトム(atomic vector)とリストの違いはなにか4つ挙げよ。

1. 行列であるリストは存在するか？構成している列が行列であるデータフレームは存在するか？

##### 概要(Outline)

```
* [Vectors](#vectors) introduces you to atomic vectors and lists, R's 1d 
  data structures.
  
* [Attributes](#attributes) takes a small detour to discuss attributes,
  R's flexible metadata specification. Here you'll learn about factors,
  an important data structure created by setting attributes of an atomic 
  vector.
  
* [Matrices and arrays](#matrices-and-arrays) introduces matrices and arrays,
  data structures for storing 2d and higher dimensional data.
  
* [Data frames](#data-frames) teaches you about the data frame, the most
  important data structure for storing data in R. Data frames combine 
  the behaviour of lists and matrices to make a structure ideally suited for
  the needs of statistical data.
```

* ベクトル[Vectors](#vectors):この項ではRにおける1次元のデータ構造であるアトム(atomic vector)およびリストについて触れる。

* 属性[Attributes](#attributes):この項では属性について説明する。属性とは柔軟性の高いメタデータの説明(flexible metadata specification)である。例として、因子型(factor)をとりあげる。因子型はよく使われるデータ型の一つであり、属性をアトムとして設定することで作られる。

* 行列と配列[Matrices and arrays](#matrices-and-arrays):この項では行列と配列について触れる。これらは2次元以上の高次元のデータを格納するデータ構造である。

* データフレーム[Data frames](#data-frames) :この項ではデータフレームについて述べる。データフレームはRにおいて最も有名なデータ構造であり、リストおよび行列の性質を併せ持ち、統計的データを扱う目的に極めて合致したデータ構造となっている。

## ベクトル(Vectors) {#vectors}

```
The basic data structure in R is the vector. Vectors come in two flavours: atomic vectors and lists. They have three common properties:

* Type, `typeof()`, what it is.
* Length, `length()`, how many elements it contains.
* Attributes, `attributes()`, additional arbitrary metadata.

They differ in the types of their elements: all elements of an atomic vector must be the same type, whereas the elements of a list can have different types.

NB: `is.vector()` does not test if an object is a vector. Instead it returns `TRUE` only if the object is a vector with no attributes apart from names. Use `is.atomic(x) || is.list(x)` to test if an object is actually a vector.
```

Rにおける最も基本的な型はベクトルである。ベクトルはアトムでありリストでもあるという２つの側面(flavour)を持つ。また以下に示す3つの性質を持つ。

* 型(Type) `typeof()` そのオブジェクトが何かを示す。
* 長さ(Length), `length()`, そのオブジェクトが持つ要素数を示す。
* 属性(Attributes), `attributes()`, そのオブジェクトに付加された任意のメタ情報を示す。

アトムとリストはその構成要素の型において相違点がある。アトムは構成要素全てが同じ型を持つ一方、リストは異なる型を許容する。

注意(NB):`is.vector()`はそのオブジェクトがベクトルであるか否かを判定しない。その代わり、対象となるオブジェクトが名前以外の属性を持たない場合のみ`TRUE`を返す。ベクトルであるか否かを判定したい際は`is.atomic(x) || is.list(x)`を用いること。


### アトム(Atomic vectors)

```
There are four common types of atomic vectors that I'll discuss in detail: logical, integer, double (often called numeric), and character. There are two rare types that I will not discuss further: complex and raw. \index{atomic vectors} \index{vectors!atomic|see{atomic vectors}}

Atomic vectors are usually created with `c()`, short for combine: \indexc{c()}
```

アトムのうち、よく使われるものとして論理型(logical)、整数型(integer)、倍精度小数点型(double)(numericと呼ばれることも)、文字型(character)の4つがあり、これらについては詳述する。また稀に使われるものとして、複素数型(complex)、ロウ型(raw)の2つがあり、こちらについてはこれ以上は触れない。\index{atomic vectors} \index{vectors!atomic|see{atomic vectors}}

アトムは`c()`によって生成することが多い。cはcombineの短縮形である。 \indexc{c()}

```r
dbl_var <- c(1, 2.5, 4.5)
# Lをつけることでdouble型ではなく整数型を指定できる
# With the L suffix, you get an integer rather than a double
int_var <- c(1L, 6L, 10L)
# TRUE(T)もしくはFALSE(F)で論理型のベクトルを生成できる
# Use TRUE and FALSE (or T and F) to create logical vectors
log_var <- c(TRUE, FALSE, T, F)
chr_var <- c("these are", "some strings")
```

```
Atomic vectors are always flat, even if you nest `c()`'s:
```
アトムはフラットな構造となっており、`c()`をネストにした場合でもネスト構造は保持されない。


```r
c(1, c(2, c(3, 4)))
```

```
## [1] 1 2 3 4
```

```r
#　以下と同様である
# the same as
c(1, 2, 3, 4)
```

```
## [1] 1 2 3 4
```

```
Missing values are specified with `NA`, which is a logical vector of length 1. `NA` will always be coerced to the correct type if used inside `c()`, or you can create `NA`s of a specific type with `NA_real_` (a double vector), `NA_integer_` and `NA_character_`. \indexc{NA}
```

欠損値は長さ1の論理型の値である`NA`として表現される。`NA`は`c()`の中で用いられると適切な型に変換されるが、`NA_real_` (倍精度小数点型のベクトル)、`NA_integer_`、`NA_character_`のように型を指定して作成することも可能である。\indexc{NA}

#### 型とその判定(Types and tests)

```
Given a vector, you can determine its type with `typeof()`, or check if it's a specific type with an "is" function: `is.character()`, `is.double()`, `is.integer()`, `is.logical()`, or, more generally, `is.atomic()`. \indexc{typeof()}
```

あるベクトルの型を調べる際には`typeof()`を用いる。また、特定の型であるかどうかを判定する際は`is.character()`, `is.double()`, `is.integer()`, `is.logical()`, `is.atomic()`といったis関数群を用いる。 \indexc{typeof()}


```r
int_var <- c(1L, 6L, 10L)
typeof(int_var)
```

```
## [1] "integer"
```

```r
is.integer(int_var)
```

```
## [1] TRUE
```

```r
is.atomic(int_var)
```

```
## [1] TRUE
```

```r
dbl_var <- c(1, 2.5, 4.5)
typeof(dbl_var)
```

```
## [1] "double"
```

```r
is.double(dbl_var)
```

```
## [1] TRUE
```

```r
is.atomic(dbl_var)
```

```
## [1] TRUE
```

```
NB: `is.numeric()` is a general test for the "numberliness" of a vector and returns `TRUE` for both integer and double vectors. It is not a specific test for double vectors, which are often called numeric. \indexc{is.numeric()}
```

注意(NB):`is.numeric()`は対象の値が数値であるかどうかを判定する汎用的な関数であり、整数型、倍精度小数点型のいずれにおいても`TRUE`を返す。倍精度小数点型は、数値型(numeric)と呼ばれることが多いが`is.numeric()`は倍精度小数点型を判定するための関数ではないことに注意してほしい。



```r
is.numeric(int_var)
```

```
## [1] TRUE
```

```r
is.numeric(dbl_var)
```

```
## [1] TRUE
```

#### 型変換(Coercion)

```
All elements of an atomic vector must be the same type, so when you attempt to combine different types they will be __coerced__ to the most flexible type. Types from least to most flexible are: logical, integer, double, and character. \index{coercion}

For example, combining a character and an integer yields a character:
```

アトムはその構成要素が全て同一の型である必要がある。したがって、異なる型の要素を結合して1つのベクトルとする際、各要素は最も柔軟性の高い(flexible)型に __型変換(coerced)__ される。型を柔軟性の低い順に並べると、論理型、整数型、倍精度小数点型、文字型の順になる。

例えば、文字型と整数型を結合した場合、結合後のベクトルは文字型となる。


```r
str(c("a", 1))
```

```
##  chr [1:2] "a" "1"
```

```
When a logical vector is coerced to an integer or double, `TRUE` becomes 1 and `FALSE` becomes 0. This is very useful in conjunction with `sum()` and `mean()`
```

論理型を整数型もしくは倍精度小数点型に変換すると、`TRUE`は1となり、`FALSE`は0となる。この性質は`sum()`および`mean()`と組み合わせると大変便利である。


```r
x <- c(FALSE, FALSE, TRUE)
as.numeric(x)
```

```
## [1] 0 0 1
```

```r
# TRUEの数をカウント
# Total number of TRUEs
sum(x)
```

```
## [1] 1
```

```r
# TRUEの割合を算出
# Proportion that are TRUE
mean(x)
```

```
## [1] 0.3333333
```

```
Coercion often happens automatically. Most mathematical functions (`+`, `log`, `abs`, etc.) will coerce to a double or integer, and most logical operations (`&`, `|`, `any`, etc) will coerce to a logical. You will usually get a warning message if the coercion might lose information. If confusion is likely, explicitly coerce with `as.character()`, `as.double()`, `as.integer()`, or `as.logical()`. 
```

多くの場合、型変換は関数適用時に自動的に行われる。多くの数学的関数群(`+`, `log`, `abs`など)は値を倍精度小数点または整数型に変換し、多くの論理演算子(logical operations)は論理型に変換して解釈する。型変換により情報が失われる場合は、警告のメッセージが出力される。混乱を避けたい場合は(confusion is likely)、`as.character()`、`as.double()`、`as.integer()`、`as.logical()`といった関数を用いて明示的に変換すると良い。

### リスト(Lists)

```
Lists are different from atomic vectors because their elements can be of any type, including lists. You construct lists by using `list()` instead of `c()`: \index{lists} \index{vectors!lists|see{lists}}
```
リストはその構成要素が同一の型であることを要求しないという点でアトムとは異なる。そしてその構成要素にはリスト自身も含まれる。リストを生成する際は`c()`の代わりに`list()`を用いる。\index{lists} \index{vectors!lists|see{lists}}


```r
x <- list(1:3, "a", c(TRUE, FALSE, TRUE), c(2.3, 5.9))
str(x)
```

```
## List of 4
##  $ : int [1:3] 1 2 3
##  $ : chr "a"
##  $ : logi [1:3] TRUE FALSE TRUE
##  $ : num [1:2] 2.3 5.9
```

```
Lists are sometimes called __recursive__ vectors, because a list can contain other lists. This makes them fundamentally different from atomic vectors.
```

リスト は__再起的な(recursive)__ ベクトルと呼ばれることがある。これはリストはその構成要素にリストを含めることができるからである。これはベクトルとは根本的に異なる点である。


```r
x <- list(list(list(list())))
str(x)
```

```
## List of 1
##  $ :List of 1
##   ..$ :List of 1
##   .. ..$ : list()
```

```r
is.recursive(x)
```

```
## [1] TRUE
```

```
`c()` will combine several lists into one. If given a combination of atomic vectors and lists, `c()` will coerce the vectors to lists before combining them. Compare the results of `list()` and `c()`:
```

`c()`は複数のリストを1つに結合する。アトムとリストが混在している場合、`c()`はアトムをリストに変換した上で結合する。以下に`list()`と`c()`をそれぞれ用いた結果を示す。



```r
x <- list(list(1, 2), c(3, 4))
y <- c(list(1, 2), c(3, 4))
str(x)
```

```
## List of 2
##  $ :List of 2
##   ..$ : num 1
##   ..$ : num 2
##  $ : num [1:2] 3 4
```

```r
str(y)
```

```
## List of 4
##  $ : num 1
##  $ : num 2
##  $ : num 3
##  $ : num 4
```

```
The `typeof()` a list is `list`. You can test for a list with `is.list()` and coerce to a list with `as.list()`. You can turn a list into an atomic vector with `unlist()`. If the elements of a list have different types, `unlist()` uses the same coercion rules as `c()`.

Lists are used to build up many of the more complicated data structures in R. For example, both data frames (described in [data frames](#data-frames)) and linear models objects (as produced by `lm()`) are lists:
```

リストに対して`typeof()`を適用した場合、`list`という結果が返る。あるオブジェクトがリストかどうかを判定するには`is.list()`が使える。また、リストに変換したい場合は`as.list()`を用いる。リストをアトムに変換する際は`unlist()`を使う。リストが異なる型の要素で構成されている場合、`unlist()`は`c()`と同じ変換ルールに従ってその構成要素を変換する。

リストはより複雑なデータ構造を構築する際にその構成要素として用いられる。例えば、データフレーム([data frames](#data-frames)で述べる)および、`lm()`によって作られる線形モデル(linear models)オブジェクトの構成要素はいずれもリストである。


```r
is.list(mtcars)
```

```
## [1] TRUE
```

```r
mod <- lm(mpg ~ wt, data = mtcars)
is.list(mod)
```

```
## [1] TRUE
```

### エクササイズ(Exercises)

```
1. What are the six types of atomic vector? How does a list differ from an
   atomic vector?

1. What makes `is.vector()` and `is.numeric()` fundamentally different to
   `is.list()` and `is.character()`?

1. Test your knowledge of vector coercion rules by predicting the output of
   the following uses of `c()`:

1.  Why do you need to use `unlist()` to convert a list to an 
    atomic vector? Why doesn't `as.vector()` work? 

1. Why is `1 == "1"` true? Why is `-1 < FALSE` true? Why is `"one" < 2` false?

1. Why is the default missing value, `NA`, a logical vector? What's special
   about logical vectors? (Hint: think about `c(FALSE, NA_character_)`.)

```

1. アトムにおける6つのデータ型とはなにか？アトムとリストの違いはなにか？

1. `is.vector()`と`is.numeric()`が`is.list()`および`is.character()`と根本的に異なる点はなにか？

1. 以下の例において`c()`が出力する結果を予想して、ベクトルの変換ルールの理解度を確認しなさい

    
    ```r
    c(1, FALSE)
    c("a", 1)
    c(list(1), "a")
    c(TRUE, 1L)
    ```

1. なぜリストをアトムに変換する際に`unlist()`を用いる必要があるか？ なぜ `as.vector()` では駄目なのか？

1. なぜ `1 == "1"`および`-1 < FALSE` はTRUEとなるのか？なぜ`"one" < 2`はFALSEとなるのか？

1. なぜ欠損値の初期値は論理型なのか？論理型の特殊な点は何か（ヒント：`c(FALSE, NA_character_)`について考えてみると良い）


## 属性(Attributes) {#attributes}

```
All objects can have arbitrary additional attributes, used to store metadata about the object. Attributes can be thought of as a named list (with unique names). Attributes can be accessed individually with `attr()` or all at once (as a list) with `attributes()`. \index{attributes}
```

全てのオブジェクトは任意の属性(attributes)を付与することができる。属性とはそのオブジェクトに関するメタデータである。属性は一意の名前をもつ名前付きリストと考えると良い。個々の要素に対して属性を取得するには`attr()`、構成要素全ての属性をリストとして取得するには`attributes()`を用いる。\index{attributes}


```r
y <- 1:10
attr(y, "my_attribute") <- "This is a vector"
attr(y, "my_attribute")
```

```
## [1] "This is a vector"
```

```r
str(attributes(y))
```

```
## List of 1
##  $ my_attribute: chr "This is a vector"
```

```
The `structure()` function returns a new object with modified attributes: \indexc{structure()}
```

`structure()`は変更された属性をもつ新しいオブジェクトを返す。


```r
structure(1:10, my_attribute = "This is a vector")
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
## attr(,"my_attribute")
## [1] "This is a vector"
```

```
By default, most attributes are lost when modifying a vector:
```

デフォルトでは多くの属性はベクトルを変更した際に失われる。


```r
attributes(y[1])
```

```
## NULL
```

```r
attributes(sum(y))
```

```
## NULL
```

```
The only attributes not lost are the three most important:

* Names, a character vector giving each element a name, described in 
  [names](#vector-names). 

* Dimensions, used to turn vectors into matrices and arrays, 
  described in [matrices and arrays](#matrices-and-arrays).

* Class, used to implement the S3 object system, described in [S3](#s3).
 
Each of these attributes has a specific accessor function to get and set values. When working with these attributes, use `names(x)`, `dim(x)`, and `class(x)`, not `attr(x, "names")`, `attr(x, "dim")`, and `attr(x, "class")`.
```

ベクトル変更時に失われない属性のうち、重要な3つを以下に挙げる。

* 名前(Names):構成要素の名前を示す文字型のベクトルである。[names](#vector-names)で述べる。

* 次元(Dimensions):行列および配列の次元数を示す。[matrices and arrays](#matrices-and-arrays)で述べる。

* クラス(Class):S3オブジェクトシステム(the S3 object system)を実装する際に用いる。[S3](#s3)で述べる。

以上の属性情報を取得もしくは設定する際は専用の関数を用いる。たとえば`attr(x, "names")`、`attr(x, "dim")`、`attr(x, "class")`の代わりに`names(x)`、`dim(x)`、`class(x)`を使う。


#### 名前(Names) {#vector-names}

```
You can name a vector in three ways: \index{attributes|names}

* When creating it: `x <- c(a = 1, b = 2, c = 3)`.

* By modifying an existing vector in place: 
  `x <- 1:3; names(x) <- c("a", "b", "c")`. \indexc{names()}

* By creating a modified copy of a vector: 
  `x <- setNames(1:3, c("a", "b", "c"))`. \indexc{setNames()}

Names don't have to be unique. However, character subsetting, described in [subsetting](#lookup-tables), is the most important reason to use names and it is most useful when the names are unique.

Not all elements of a vector need to have a name. If some names are missing, `names()` will return an empty string for those elements. If all names are missing, `names()` will return `NULL`.
```

ベクトルに名前をつけるには以下の3つの方法がある \index{attributes|names}。

* ベクトル生成時に名前をつける: `x <- c(a = 1, b = 2, c = 3)`

* 既存のベクトルに名前をつける: 
`x <- 1:3; names(x) <- c("a", "b", "c")`. \indexc{names()}

* ベクトルのa modified copyを作る:
  `x <- setNames(1:3, c("a", "b", "c"))`. \indexc{setNames()}

名前は一意である必要はない。しかし、名前をつける用途としては[subsetting](#lookup-tables)で述べているような文字列の一部を取り出すというケースが最も多いため、一意な名前をつけておくと便利である。

全ての構成要素が名前を持つ必要はない。いくつかの構成要素において名前がない場合、`names()`はその要素については空の文字列を返す。全ての構成要素に名前がない場合は`names()`は`NULL`を返す。


```r
y <- c(a = 1, 2, 3)
names(y)
```

```
## [1] "a" ""  ""
```

```r
z <- c(1, 2, 3)
names(z)
```

```
## NULL
```

```
You can create a new vector without names using `unname(x)`, or remove names in place with `names(x) <- NULL`.
```
名前付きのベクトルから名前をつけていないベクトルを新しく作る場合は`unname(x)`を用いるか、`names(x) <- NULL`で名前を消去する。


### 因子(Factors)

```
One important use of attributes is to define factors. A factor is a vector that can contain only predefined values, and is used to store categorical data. Factors are built on top of integer vectors using two attributes: the `class()`, "factor", which makes them behave differently from regular integer vectors, and the `levels()`, which defines the set of allowed values. \index{factors|(}
```

属性の用途として最も重要なものに因子(factors)の定義が挙げられる。因子とはとはpredefineの値(predefined values)のみを格納するベクトルでありカテゴリカルデータを格納する際に用いられる。因子は2つの属性、`class()`と`levels()`で構成されている。前者には"factor"が設定され、これにより通常の整数型ベクトルとは異なる挙動を示すようになる。また後者によって許容される値の範囲を定義する。\index{factors|(}


```r
x <- factor(c("a", "b", "b", "a"))
x
```

```
## [1] a b b a
## Levels: a b
```

```r
class(x)
```

```
## [1] "factor"
```

```r
levels(x)
```

```
## [1] "a" "b"
```

```r
# levelに含まれない値を用いることはできない
# You can't use values that are not in the levels
x[2] <- "c"
```

```
## Warning in `[<-.factor`(`*tmp*`, 2, value = "c"): invalid factor level, NA
## generated
```

```r
x
```

```
## [1] a    <NA> b    a   
## Levels: a b
```

```r
# 注意:因子同士を結合することはできない
# NB: you can't combine factors
c(factor("a"), factor("b"))
```

```
## [1] 1 1
```

```
Factors are useful when you know the possible values a variable may take, even if you don't see all values in a given dataset. Using a factor instead of a character vector makes it obvious when some groups contain no observations:
```

因子は変数のとりうる値がわかっている際には有用である。これはデータセットの中にとりうる全ての値が含まれていない場合にもいえる。例えば以下のように、因子を文字型ベクトルの代わりに用いることで、設定したグループのうちデータがないものが何か明確になる。


```r
sex_char <- c("m", "m", "m")
sex_factor <- factor(sex_char, levels = c("m", "f"))

table(sex_char)
```

```
## sex_char
## m 
## 3
```

```r
table(sex_factor)
```

```
## sex_factor
## m f 
## 3 0
```

```
Sometimes when a data frame is read directly from a file, a column you'd thought would produce a numeric vector instead produces a factor. This is caused by a non-numeric value in the column, often a missing value encoded in a special way like `.` or `-`. To remedy the situation, coerce the vector from a factor to a character vector, and then from a character to a double vector. (Be sure to check for missing values after this process.) Of course, a much better plan is to discover what caused the problem in the first place and fix that; using the `na.strings` argument to `read.csv()` is often a good place to start.
```

データフレームをファイルから読み込んだ際、一部の列において、数値型のベクトルを期待していたのに因子になっている場合がある。これはその列に数値型ではない値が含まれていたからである。よくある例としては欠損値を`.`や`-`といった値で入力しているというものが挙げられる。対策としては因子を文字型ベクトルに変換した上でさらに倍精度小数点型に変換するという方法がある（この方法を適用した際には欠損値をチェックしておくこと。）もちろんこのような場当たり的な方法ではなく、原因を確認して解決するという方法が望ましいことは言うまでもない。欠損値を指定する文字列が把握できた場合は、`read.csv()`の引数である`na.strings`で対象と鳴る文字列を指定すると良い。


```r
# ここではファイルの代わりにテキストを読みこんでいる
# Reading in "text" instead of from a file here:
z <- read.csv(text = "value\n12\n1\n.\n9")
typeof(z$value)
```

```
## [1] "integer"
```

```r
as.double(z$value)
```

```
## [1] 3 2 1 4
```

```r
# 3 2 1 4は因子のレベルであり、今回読み込みたい値とは異なる
# Oops, that's not right: 3 2 1 4 are the levels of a factor, 
# not the values we read in!
class(z$value)
```

```
## [1] "factor"
```

```r
# 修正する
# We can fix it now:
as.double(as.character(z$value))
```

```
## Warning: 強制変換により NA が生成されました
```

```
## [1] 12  1 NA  9
```

```r
# もしくは読み込み方を変更する
# Or change how we read it in:
z <- read.csv(text = "value\n12\n1\n.\n9", na.strings=".")
typeof(z$value)
```

```
## [1] "integer"
```

```r
class(z$value)
```

```
## [1] "integer"
```

```r
z$value
```

```
## [1] 12  1 NA  9
```

```r
# これで完璧
# Perfect! :)
```

```
Unfortunately, most data loading functions in R automatically convert character vectors to factors. This is suboptimal, because there's no way for those functions to know the set of all possible levels or their optimal order. Instead, use the argument `stringsAsFactors = FALSE` to suppress this behaviour, and then manually convert character vectors to factors using your knowledge of the data. A global option, `options(stringsAsFactors = FALSE)`, is available to control this behaviour, but I don't recommend using it. Changing a global option may have unexpected consequences when combined with other code (either from packages, or code that you're `source()`ing), and global options make code harder to understand because they increase the number of lines you need to read to understand how a single line of code will behave.  \indexc{stringsAsFactors}
```

残念なことにRのデータ読み込み関数群の多くは文字型ベクトルを自動的に因子に変換してしまう。これは最善手とは言いがたい。なぜならこれらの関数を用いる際に、とりうるレベルもしくはその順序を指定する方法が無いからである。この場合、データ読み込み関数群の引数に`stringsAsFactors = FALSE`を指定して、自動的に変換しないようにした上で、そのデータに関する知識(レベルの範囲や順序など)を用いて文字型ベクトルから因子にあらためて変換するようにした方が良い。グローバルオプション(global option)として`options(stringsAsFactors = FALSE)`を用いるという方法もあるが、筆者はこの方法を勧めない。グローバルオプションの変更は、外部パッケージや`source()`によって読み込まれた他のコードと組み合わせた場合に予期しない結果を招くことがあるからである。またグローバルオプションの指定はあるコードの一行を理解するのに必要なコードの量を増加させるため、コードを読みづらいものとしてしまうという難点もある。\indexc{stringsAsFactors}

```
While factors look (and often behave) like character vectors, they are actually integers. Be careful when treating them like strings. Some string methods (like `gsub()` and `grepl()`) will coerce factors to strings, while others (like `nchar()`) will throw an error, and still others (like `c()`) will use the underlying integer values. For this reason, it's usually best to explicitly convert factors to character vectors if you need string-like behaviour. In early versions of R, there was a memory advantage to using factors instead of character vectors, but this is no longer the case. \index{factors|)}
```

因子は外見および挙動において文字型ベクトルと似ているが、その実態は整数値である。文字列のように扱う際には注意してほしい。`gsub()`や`grepl()`といった文字列を扱う関数の一部は因子を文字列に自動的に変換してくれるが、`nchar()`などの関数はエラーを返す。`c()`のように整数値として扱う関数もある。以上の理由から、因子を文字列として扱いたい場合は、明示的に文字型ベクトルから因子に変換するのがベストである。なお、Rの初期バージョンでは因子は文字型ベクトルに比べてメモリの消費量が少ないという利点があったが、現在はそのような差は無くなっている。\index{factors|)}

### エクササイズ(Exercises)

```
1.  An early draft used this code to illustrate `structure()`:

    
    ```r
    structure(1:5, comment = "my attribute")
    ```
    
    ```
    ## [1] 1 2 3 4 5
    ```

    But when you print that object you don't see the comment attribute.
    Why? Is the attribute missing, or is there something else special about
    it? (Hint: try using help.) \index{attributes!comment}
```

1. `structure()`の例を示すために書いた初期のコードを示す。
    
    ```r
    structure(1:5, comment = "my attribute")
    ```
    
    ```
    ## [1] 1 2 3 4 5
    ```

    しかしこのオブジェクトをprintすると、このオブジェクトの属性（コメント）は表示されない。
    これはなぜか？属性が失われた、もしくはなにか特殊な事情があるのだろうか？（ヒント：ヘルプ参照）
    \index{attributes!comment}

```
1.  What happens to a factor when you modify its levels? 
```

1. 以下のように因子のレベルを変更すると何が起きるだろうか？
    
    ```r
    f1 <- factor(letters)
    levels(f1) <- rev(levels(f1))
    ```

```
1.  What does this code do? How do `f2` and `f3` differ from `f1`?
```

1. 以下のコードの結果はどうなるだろうか？`f2`および`f3`と`f1`の違いはなんだろうか？

    
    ```r
    f2 <- rev(factor(letters))
    
    f3 <- factor(letters, levels = rev(letters))
    ```

## 行列および配列(Matrices and arrays) {#matrices-and-arrays}

```
Adding a `dim()` attribute to an atomic vector allows it to behave like a multi-dimensional __array__. A special case of the array is the __matrix__, which has two dimensions. Matrices are used commonly as part of the mathematical machinery of statistics. Arrays are much rarer, but worth being aware of. \index{arrays|(} \index{matrices|see{arrays}}

Matrices and arrays are created with `matrix()` and `array()`, or by using the assignment form of `dim()`:
```

`dim()`属性をアトムに加えることで多次元 __配列(array)__ のような挙動をさせることができる。この配列の特殊な形として 次元数が2となる __行列(matrix)__ が挙げられる。行列は統計学の数学的要素を語る上で書かせない要素である(the mathematical machinery of statistics)。なお、行列に比べると、配列を用いることは稀だが、知っておいて損は無い。\index{arrays|(} \index{matrices|see{arrays}}

行列および配列は`matrix()`、`array()`を用いるか、`dim()`を設定することで生成できる。


```r
# 下記は次元（行および列）を指定するために2つのスカラーでもって指定している
# Two scalar arguments to specify rows and columns
a <- matrix(1:6, ncol = 3, nrow = 2)
# 下記は一つのベクトルでもって次元を指定している
# One vector argument to describe all dimensions
b <- array(1:12, c(2, 3, 2))

# dim()を指定することでオブジェクトを変形させることができる
# You can also modify an object in place by setting dim()
c <- 1:6
dim(c) <- c(3, 2)
c
```

```
##      [,1] [,2]
## [1,]    1    4
## [2,]    2    5
## [3,]    3    6
```

```r
dim(c) <- c(2, 3)
c
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
```

```
`length()` and `names()` have high-dimensional generalisations:

* `length()` generalises to `nrow()` and `ncol()` for matrices, and `dim()`
  for arrays. \indexc{nrow()} \indexc{ncol()} \indexc{dim()}

* `names()` generalises to `rownames()` and `colnames()` for matrices, and
  `dimnames()`, a list of character vectors, for arrays. \indexc{rownames()}
  \indexc{colnames()} \indexc{dimnames()}

```
`length()`および`names()`はそれぞれ高次元で同じ役割をもつ関数が用意されている。

* `length()`の高次元版として、行列には`nrow`と`ncol()`、配列には `dim()`が用意されている。
\indexc{nrow()} \indexc{ncol()} \indexc{dim()}

* `names()` の高次元版として 行列には`rownames()`と`colnames()`、配列には`dimnames()`が用意されている。`dimnames()`は文字型ベクトルのリストである。\indexc{rownames()}
  \indexc{colnames()} \indexc{dimnames()}


```r
length(a)
```

```
## [1] 6
```

```r
nrow(a)
```

```
## [1] 2
```

```r
ncol(a)
```

```
## [1] 3
```

```r
rownames(a) <- c("A", "B")
colnames(a) <- c("a", "b", "c")
a
```

```
##   a b c
## A 1 3 5
## B 2 4 6
```

```r
length(b)
```

```
## [1] 12
```

```r
dim(b)
```

```
## [1] 2 3 2
```

```r
dimnames(b) <- list(c("one", "two"), c("a", "b", "c"), c("A", "B"))
b
```

```
## , , A
## 
##     a b c
## one 1 3 5
## two 2 4 6
## 
## , , B
## 
##     a  b  c
## one 7  9 11
## two 8 10 12
```

```
`c()` generalises to `cbind()` and `rbind()` for matrices, and to `abind()` (provided by the `abind` package) for arrays. You can transpose a matrix with `t()`; the generalised equivalent for arrays is `aperm()`. \indexc{cbind()} \indexc{rbind()} \indexc{abind()} \indexc{aperm()}
```

`c()`を高次元に対応させたものとして、行列には`cbind()`と`rbind()`、配列には`abind()`(ただしこれは`abind`パッケージが必要)がある。行列の転置には`t()`が用いられる。配列に関してこれと同等の役割をもつ関数としては`aperm()`がある。\indexc{cbind()} \indexc{rbind()} \indexc{abind()} \indexc{aperm()}

```
You can test if an object is a matrix or array using `is.matrix()` and `is.array()`, or by looking at the length of the `dim()`. `as.matrix()` and `as.array()` make it easy to turn an existing vector into a matrix or array.
```

オブジェクトが行列か配列か判定するには`is.matrix()`、`is.array()`を用いるか、`dim()`によって次元数を確認するとよい。また、既存のベクトルを行列および配列に変換するには `as.matrix()`、`as.array()`を用いるのが簡単である。

```
Vectors are not the only 1-dimensional data structure. You can have matrices with a single row or single column, or arrays with a single dimension. They may print similarly, but will behave differently. The differences aren't too important, but it's useful to know they exist in case you get strange output from a function (`tapply()` is a frequent offender). As always, use `str()` to reveal the differences. \index{arrays!1d}
```

ベクトルは1次元のデータ構造というだけではない。1つの行（または列）のみをもつ行列や、1次元の配列を生成することは可能である。これらは外観が同一だが、異なる挙動を示す。その違いはそこまで重要なものではないが、関数の出力に差異が出ることは知っておくと有用だろう(`tapply()`の出力において困ることが多い)。`str()`を用いることでベクトル、行列、配列は見分けることができる。\index{arrays!1d}


```r
str(1:3)                   # 1次元のベクトル 1d vector
```

```
##  int [1:3] 1 2 3
```

```r
str(matrix(1:3, ncol = 1)) # 行列の列ベクトル column vector
```

```
##  int [1:3, 1] 1 2 3
```

```r
str(matrix(1:3, nrow = 1)) # 行列の行ベクトル row vector
```

```
##  int [1, 1:3] 1 2 3
```

```r
str(array(1:3, 3))         # 配列 "array" vector
```

```
##  int [1:3(1d)] 1 2 3
```

```
While atomic vectors are most commonly turned into matrices, the dimension attribute can also be set on lists to make list-matrices or list-arrays: \index{arrays!list-arrays} \index{list-arrays}
```

アトムから行列への変換はよく利用されるが、下記例のように次元属性をリストに付与することでリスト-行列や、リスト-配列を作ることができる。\index{arrays!list-arrays} \index{list-arrays}


```r
l <- list(1:3, "a", TRUE, 1.0)
dim(l) <- c(2, 2)
l
```

```
##      [,1]      [,2]
## [1,] Integer,3 TRUE
## [2,] "a"       1
```

```
These are relatively esoteric data structures, but can be useful if you want to arrange objects into a grid-like structure. For example, if you're running models on a spatio-temporal grid, it might be natural to preserve the grid structure by storing the models in a 3d array. \index{arrays|)}
```

リスト-行列やリスト-配列はあまり使われない(esoteric)データ構造ではあるがオブジェクトをグリッド上の構造に構成したい時は有用である。例えば、空間-時間グリッドの上でモデルを走らせる際、3次元配列にモデルを格納したグリッド構造を用いるのが自然だろう。

### エクササイズ(Exercises)

```
1.  What does `dim()` return when applied to a vector?

1.  If `is.matrix(x)` is `TRUE`, what will `is.array(x)` return?

1.  How would you describe the following three objects? What makes them
    different to `1:5`?
```

1. `dim()`をベクトルに適用した場合、どのような結果が返ってくるか？

1. `is.matrix(x)`が`TRUE`だった場合、`is.array(x)`はどのような結果となるか？

1. 以下の3つのオブジェクトはどのように説明されるか？`1:5`との違いはなにか？

    
    ```r
    x1 <- array(1:5, c(1, 1, 5))
    x2 <- array(1:5, c(1, 5, 1))
    x3 <- array(1:5, c(5, 1, 1))
    ```

## データフレーム(Data frames) {#data-frames}

```
A data frame is the most common way of storing data in R, and if [used systematically](http://vita.had.co.nz/papers/tidy-data.pdf) makes data analysis easier. Under the hood, a data frame is a list of equal-length vectors. This makes it a 2-dimensional structure, so it shares properties of both the matrix and the list.  This means that a data frame has `names()`, `colnames()`, and `rownames()`, although `names()` and `colnames()` are the same thing. The `length()` of a data frame is the length of the underlying list and so is the same as `ncol()`; `nrow()` gives the number of rows. \index{data frames|(}
```

データフレームはRにおいてデータを格納する手段としては最も使われている方法である。また[システマティックに用いることで](http://vita.had.co.nz/papers/tidy-data.pdf)、データ分析がより楽になる。データフレームは長さが同一のベクトルのリストである。この構造により、データは2次元構造として表現され行列とリストの両方の性質を併せ持っている。これはデータフレームは`names()`、`colnames()`、`rownames()`を持っていることを意味する。ただしデータフレームにおいては`names()`と`colnames()`は同じものを指している。データフレームの`length()`はデータフレームを構成するリストの長さ、つまり`ncol()`と同一のものを指す。`nrow()`は行数を示す。 \index{data frames|(}

```
As described in [subsetting](#subsetting), you can subset a data frame like a 1d structure (where it behaves like a list), or a 2d structure (where it behaves like a matrix).
```

[subsetting](#subsetting)で後述するが、データフレームの一部を1次元のデータ構造(この場合リストのような挙動を示す)として取り出したり、2次元のデータ構造(この場合は行列のような挙動を示す)として取り出すことができる。

### データフレームの生成(Creation)

```
You create a data frame using `data.frame()`, which takes named vectors as input:
```

`data.frame()`を用いることでデータフレームは生成できる。この場合の入力としては名前付きのベクトルを用いる。


```r
df <- data.frame(x = 1:3, y = c("a", "b", "c"))
str(df)
```

```
## 'data.frame':	3 obs. of  2 variables:
##  $ x: int  1 2 3
##  $ y: Factor w/ 3 levels "a","b","c": 1 2 3
```

```
Beware `data.frame()`'s default behaviour which turns strings into factors. Use `stringAsFactors = FALSE` to suppress this behaviour: \indexc{stringsAsFactors}
```

`data.frame()`はデフォルトでは文字列を因子に変換してしまうことに注意してほしい。この挙動を抑制するには引数に`stringAsFactors = FALSE`を指定する。\indexc{stringsAsFactors}


```r
df <- data.frame(
  x = 1:3,
  y = c("a", "b", "c"),
  stringsAsFactors = FALSE)
str(df)
```

```
## 'data.frame':	3 obs. of  2 variables:
##  $ x: int  1 2 3
##  $ y: chr  "a" "b" "c"
```

### データフレームの判定および変換(Testing and coercion)

```
Because a `data.frame` is an S3 class, its type reflects the underlying vector used to build it: the list. To check if an object is a data frame, use `class()` or test explicitly with `is.data.frame()`:
```

`data.frame()`はS3クラスなので、その型には構成要素の型が反映されており、リストとして判定される。オブジェクトがデータフレームであるか否かを判定するには、`class()`または明示的に`is.data.frame()`を用いる。


```r
typeof(df)
```

```
## [1] "list"
```

```r
class(df)
```

```
## [1] "data.frame"
```

```r
is.data.frame(df)
```

```
## [1] TRUE
```

```
You can coerce an object to a data frame with `as.data.frame()`:

* A vector will create a one-column data frame.

* A list will create one column for each element; it's an error if they're 
  not all the same length.
  
* A matrix will create a data frame with the same number of columns and rows as the matrix.
```

`as.data.frame()`を用いることでオブジェクトををデータフレームに変換できる。

* ベクトルを変換した場合、1列のデータフレームとなる。

* リストを変換した場合、各構成要素が列を構成するデータフレームとなる。長さが異なるリストで構成されている場合はエラーとなる。

* 行列を変換した場合、その行数、列数が同一のデータフレームが生成される。

### データフレームの結合(Combining data frames)

```
You can combine data frames using `cbind()` and `rbind()`: \indexc{cbind()} \indexc{rbind()}
```

データフレームを結合するには`cbind()`および`rbind()`を用いる。\indexc{cbind()} \indexc{rbind()}


```r
cbind(df, data.frame(z = 3:1))
```

```
##   x y z
## 1 1 a 3
## 2 2 b 2
## 3 3 c 1
```

```r
rbind(df, data.frame(x = 10, y = "z"))
```

```
##    x y
## 1  1 a
## 2  2 b
## 3  3 c
## 4 10 z
```

```
When combining column-wise, the number of rows must match, but row names are ignored. When combining row-wise, both the number and names of columns must match. Use `plyr::rbind.fill()` to combine data frames that don't have the same columns. 
```

列方向に結合する場合は、結合するデータフレーム間で行数が一致している必要があり、行の名前は無視される。行方向に結合する場合は列の数と列の名前が一致している必要がある。なお、`plyr::rbind.fill()`を用いることで列が一致していなくても結合することが可能となる。

```
It's a common mistake to try and create a data frame by `cbind()`ing vectors together. This doesn't work because `cbind()` will create a matrix unless one of the arguments is already a data frame. Instead use `data.frame()` directly:
```

よくある間違いとしてベクトルを`cbind()`で結合することでデータフレームを生成しようとする、というものがある。これは意図通りには動かない。なぜなら`cbind()`は適用する要素の1つがデータフレームでない限り行列を生成するからである。代わりに`data.frame()`を用いるべきである。


```r
bad <- data.frame(cbind(a = 1:2, b = c("a", "b")))
str(bad)
```

```
## 'data.frame':	2 obs. of  2 variables:
##  $ a: Factor w/ 2 levels "1","2": 1 2
##  $ b: Factor w/ 2 levels "a","b": 1 2
```

```r
good <- data.frame(a = 1:2, b = c("a", "b"),
  stringsAsFactors = FALSE)
str(good)
```

```
## 'data.frame':	2 obs. of  2 variables:
##  $ a: int  1 2
##  $ b: chr  "a" "b"
```

```
The conversion rules for `cbind()` are complicated and best avoided by ensuring all inputs are of the same type.
```

`cbind()`の変換ルールは複雑であり全ての入力データが同一の型であるとき以外は避けた方が良い。

### 特殊な列(Special columns)

```
Since a data frame is a list of vectors, it is possible for a data frame to have a column that is a list: \index{data frames!list in column}
```

データフレームはベクトルのリストであるため、データフレームがはその構成要素がリストである列を持つことができる。\index{data frames!list in column}


```r
df <- data.frame(x = 1:3)
df$y <- list(1:2, 1:3, 1:4)
df
```

```
##   x          y
## 1 1       1, 2
## 2 2    1, 2, 3
## 3 3 1, 2, 3, 4
```

```
However, when a list is given to `data.frame()`, it tries to put each item of the list into its own column, so this fails:
```

だが、リストを`data.frame()`に入れると、リストの各構成要素を列とするデータフレームを生成しようとしてエラーになる。


```r
data.frame(x = 1:3, y = list(1:2, 1:3, 1:4))
```

```
## Error in data.frame(1:2, 1:3, 1:4, check.names = FALSE, stringsAsFactors = TRUE): arguments imply differing number of rows: 2, 3, 4
```

```
A workaround is to use `I()`, which causes `data.frame()` to treat the list as one unit:
```

これを防ぐには`I()`を用いる。これにより、`data.frame()`は指定したリストを一つの構成要素として扱ってくれる。


```r
dfl <- data.frame(x = 1:3, y = I(list(1:2, 1:3, 1:4)))
str(dfl)
```

```
## 'data.frame':	3 obs. of  2 variables:
##  $ x: int  1 2 3
##  $ y:List of 3
##   ..$ : int  1 2
##   ..$ : int  1 2 3
##   ..$ : int  1 2 3 4
##   ..- attr(*, "class")= chr "AsIs"
```

```r
dfl[2, "y"]
```

```
## [[1]]
## [1] 1 2 3
```

```
`I()` adds the `AsIs` class to its input, but this can usually be safely ignored. \indexc{I()}
```

`I()`は`AsIs`クラスを付与するが、これは通常無視される(this can usually be safely ignored.)。

```
Similarly, it's also possible to have a column of a data frame that's a matrix or array, as long as the number of rows matches the data frame: \index{data frames!array in column}
```

他列と行数さえ一致しているなら、リストと同様に列の構成要素が行列もしくは配列であるデータフレームを生成することも可能である。\index{data frames!array in column}


```r
dfm <- data.frame(x = 1:3, y = I(matrix(1:9, nrow = 3)))
str(dfm)
```

```
## 'data.frame':	3 obs. of  2 variables:
##  $ x: int  1 2 3
##  $ y: 'AsIs' int [1:3, 1:3] 1 2 3 4 5 6 7 8 9
```

```r
dfm[2, "y"]
```

```
##      [,1] [,2] [,3]
## [1,]    2    5    8
```

```
Use list and array columns with caution: many functions that work with data frames assume that all columns are atomic vectors. \index{data frames|)}
```

リストおよび配列によって構成される列を生成する際には注意が必要である。データフレームに適用できる関数は構成要素がアトムであることを前提にしていることが多いからである。\index{data frames|)}

### エクササイズ(Exercises)

1.  What attributes does a data frame possess?

1.  What does `as.matrix()` do when applied to a data frame with 
    columns of different types?

1.  Can you have a data frame with 0 rows? What about 0 columns?

## Answers {#data-structure-answers}

1.  The three properties of a vector are type, length, and attributes.
   
1.  The four common types of atomic vector are logical, integer, double 
    (sometimes called numeric), and character. The two rarer types are
    complex and raw.
    
1.  Attributes allow you to associate arbitrary additional metadata to
    any object. You can get and set individual attributes with `attr(x, "y")`
    and `attr(x, "y") <- value`; or get and set all attributes at once with
    `attributes()`.

1.  The elements of a list can be any type (even a list); the elements of 
    an atomic vector are all of the same type. Similarly, every element of 
    a matrix must be the same type; in a data frame, the different columns 
    can have different types.
    
1.  You can make "list-array" by assuming dimensions to a list. You can
    make a matrix a column of a data frame with `df$x <- matrix()`, or
    using `I()` when creating a new data frame `data.frame(x = I(matrix())`.
