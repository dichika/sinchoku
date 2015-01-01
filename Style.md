---
title: 05_コーディングスタイル(Style)
output:
  html_document:
    keep_md: yes
---

# コーディングスタイルガイド(Style guide) {#style}

```
Good coding style is like using correct punctuation. You can manage without it, but it sure makes things easier to read. As with styles of punctuation, there are many possible variations. The following guide describes the style that I use (in this book and elsewhere). It is based on Google's [R style guide][1], with a few tweaks. You don't have to use my style, but you really should use a consistent style. \index{style guide} \index{code style}
```

良いコーディングスタイルは、適切な句読点の使い方に似ている。それなしでもやっていけるが、使った方が俄然コードは読みやすくなる。句読点のスタイルと同様、コーディングスタイルにも様々な流儀がある。いかに示すガイドは、筆者がこの本を含めた各所で用いているコーディングスタイルを紹介したものである。原則として[R style guide][1]に則ったものであり、一部修正を加えている。もちろん読者は必ずしも筆者のコーディングスタイルに沿う必要はないが、自分の中で一貫したものを持つようにはしておいてほしい。\index{style guide} \index{code style}

```
Good style is important because while your code only has one author, it'll usually have multiple readers. This is especially true when you're writing code with others. In that case, it's a good idea to agree on a common style up-front. Since no style is strictly better than another, working with others may mean that you'll need to sacrifice some preferred aspects of your style.
```

コードは一人で書いていても、それを読む人はたいていの場合複数になる。その意味で良いコーディングスタイルを身につけておくことは大切である。特に他者とコードを書いていく場合にそのことはいえる。この場合、前もって共通のコーディングスタイルを用いるようにしておくのが良いだろう。なお、他のコーディングスタイルより明らかに優れたものはないため、他者とコードを書いていく際は、自分が用いるコーディングスタイルの良い部分を多少はあきらめて他者のものとすり合わせる必要がある。

```
The formatR package, by Yihui Xie, makes it easier to clean up poorly formatted code. It can't do everything, but it can quickly get your code from terrible to pretty good. Make sure to read [the notes on the wiki](https://github.com/yihui/formatR/wiki) before using it.
```

Yihui Xieが開発したformatRパッケージは、整形されていないコードを整形してくれる。もちろん全てに手が回るものではないが、手軽にコードを極めてひどい状態から少しは良い状態にまではもっていってくれる。なおformatRを利用する際は[the notes on the wiki](https://github.com/yihui/formatR/wiki)を一読しておいてほしい。

## 表記および命名(Notation and naming)

### ファイル名(File names)

```
File names should be meaningful and end in `.R`.
```
ファイル名は意味をもったものにすること。
そして、拡張子は`.R`と大文字のRにすること。


    # 良い
       #  Good
    fit-models.R
    utility-functions.R

    # 悪い
    # Bad
    foo.r
    stuff.r
```
If files need to be run in sequence, prefix them with numbers:
```
連続して実行したいファイルについては、先頭に数字を入れること。
    0-download.R
    1-parse.R
    2-explore.R

### オブジェクト名(Object names)

```
> "There are only two hard things in Computer Science: cache invalidation and 
> naming things." 
>
> --- Phil Karlton
```

> コンピュータサイエンスにおいて頭を悩ますことが2つある。キャッシュ無効化そして「命名」だ
>  
> --- Phil Karlton

```
Variable and function names should be lowercase. Use an underscore (`_`) to separate words within a name. Generally, variable names should be nouns and function names should be verbs. Strive for names that are concise and meaningful (this is not easy!).
```

変数および関数名は小文字でつけるべきである。名前と名前の間にはアンダースコア(`_`)を入れる。一般的に変数名には名詞を、関数名には動詞を用いる。名前は簡潔かつ意味をもつものになるよう努める(しかし、簡単なことではない)。


```r
# 良い
# Good
day_one
day_1

# 悪い
# Bad
first_day_of_the_month
DayOne
dayone
djm1
```

```
Where possible, avoid using names of existing functions and variables. This will cause confusion for the readers of your code.
```

可能なら、既存の関数や変数と同じ名前は避ける。コードを読む側が混乱するからである。


```r
# 悪い
# Bad
T <- FALSE
c <- 10
mean <- function(x) sum(x)
```

## 文法(Syntax)

### スペースの入れ方(Spacing)

```
Place spaces around all infix operators (`=`, `+`, `-`, `<-`, etc.). The same rule applies when using `=` in function calls. Always put a space after a comma, and never before (just like in regular English).
```

演算子（`=`、`+`,、`-`、`<-`など)全般を用いる際は前後にスペースを入れること。これは関数呼び出しにおいて、`=`を用いる際にもいえる。通常、コンマの後にスペースを入れるが、前には入れない。これは英語における用法と同様である。


```r
# 良い
# Good
average <- mean(feet / 12 + inches, na.rm = TRUE)

# 悪い
# Bad
average<-mean(feet/12+inches,na.rm=TRUE)
```

```
There's a small exception to this rule: `:`, `::` and `:::` don't need spaces around them.
```
ただし、例外として`:`、`::`、`:::`の場合はスペースを入れない。


```r
# 良い
# Good
x <- 1:10
base::get

# 悪い
# Bad
x <- 1 : 10
base :: get
```
```
Place a space before left parentheses, except in a function call.
```

関数呼び出しの場合を除いて、左カッコの前にスペースを挿入する。


```r
# Good
if (debug) do(x)
plot(x, y)

# Bad
if(debug)do(x)
plot (x, y)
```

```
Extra spacing (i.e., more than one space in a row) is ok if it improves alignment of equal signs or assignments (`<-`).
```

`=`や`<-`の並びを整えるためのスペース追加は許容される。


```r
list(
  total = a + b + c, 
  mean  = (a + b + c) / n
)
```

```
Do not place spaces around code in parentheses or square brackets (unless there's a comma, in which case see above).
```

カッコ`()`や角カッコ`[]`に囲まれるコードの前後にスペースを入れてはいけない。ただし、コンマがある場合はその後にスペースを入れる。


```r
# 良い
# Good
if (debug) do(x)
diamonds[5, ]

# 悪い
# Bad
if ( debug ) do(x)  # debugの前後にはスペースを入れない(No spaces around debug)
x[1,]   # コンマの後にスペースを入れる(Needs a space after the comma)
x[1 ,]  # コンマの前にはスペースを入れないSpace goes after comma not before
```

### 波カッコ(Curly braces)

```
An opening curly brace should never go on its own line and should always be followed by a new line. A closing curly brace should always go on its own line, unless it's followed by `else`.
```

波カッコを開く場合、それまでのコードに続けて挿入し、残りのコードは改行して続ける。波カッコを閉じる際は改行して挿入する。ただし`else`が続く場合は改行しない。

```
Always indent the code inside curly braces.
```

波カッコに囲まれているコードはインデントで整列させること。


```r
# Good

if (y < 0 && debug) {
  message("Y is negative")
}

if (y == 0) {
  log(x)
} else {
  y ^ x
}

# Bad

if (y < 0 && debug)
message("Y is negative")

if (y == 0) {
  log(x)
} 
else {
  y ^ x
}
```

```
It's ok to leave very short statements on the same line:
```

ただしコードが短い場合はそのまま同じ行に続けても良い。


```r
if (y < 0 && debug) message("Y is negative")
```

### Line length

Strive to limit your code to 80 characters per line. This fits comfortably on a printed page with a reasonably sized font. If you find yourself running out of room, this is a good indication that you should encapsulate some of the work in a separate function.

### Indentation

When indenting your code, use two spaces. Never use tabs or mix tabs and spaces.

The only exception is if a function definition runs over multiple lines. In that case, indent the second line to where the definition starts:


```r
long_function_name <- function(a = "a long argument", 
                               b = "another argument",
                               c = "another long argument") {
  # As usual code is indented by two spaces.
}
```

### Assignment

Use `<-`, not `=`, for assignment. \index{assignment}


```r
# Good
x <- 5
# Bad
x = 5
```

## Organisation

### Commenting guidelines

Comment your code. Each line of a comment should begin with the comment symbol and a single space: `# `. Comments should explain the why, not the what. \index{comments}

Use commented lines of `-` and `=` to break up your file into easily readable chunks.


```r
# Load data ---------------------------

# Plot data ---------------------------
```

 [1]: http://google-styleguide.googlecode.com/svn/trunk/google-r-style.html
