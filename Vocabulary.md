---
title: 04_ボキャブラリー(Vocab)
output:
  html_document:
    keep_md: yes
---
# ボキャブラリー(Vocabulary) {#vocabulary}

```
An important part of being fluent in R is having a good working vocabulary. Below, I have listed the functions that I believe constitute such a vocabulary. You don't need to be intimately familiar with the details of every function, but you should at least be aware that they all exist. If there are functions in this list that you've never heard of, I strongly recommend that you read their documentation. \index{vocabulary}
```

Rを使いこなすための重要なステップとして良いワーキングボキャブラリーを持つというのがある。以下に、そのようなボキャブラリーを構成する関数を列挙した。それぞれの関数に対して深く踏み込む必要はないが、存在は頭に入れておくと良い。もし以下のリストの中に全く聞いたことのない関数があれば、そのドキュメントを読んでおくことを強く勧める。\index{vocabulary}

```
I came up with this list by looking through all the functions in the base, stats, and utils packages, and extracting those that I think are most useful. The list also includes a few pointers to particularly important functions in other packages, and some of the more important `options()`.
```

以下のリストはbase、stats, utilsパッケージの中から筆者が最も有用だと思う関数を抜き出してきている。また、このリストは他パッケージにおいて極めて重要な関数群へのポインタにもなっており、重要な`options()`の一部も紹介している。

## 基本的な関数群(The basics)


```r
# まず最初に知っておくべき関数
# The first functions to learn
?
str

# 重要な演算子および代入演算子
# Important operators and assignment
%in%, match
=, <-, <<-
$, [, [[, head, tail, subset
with
assign, get

# 比較
# Comparison 
all.equal, identical
!=, ==, >, >=, <, <=
is.na, complete.cases
is.finite

# 基本的な数学関数
# Basic math
*, +, -, /, ^, %%, %/%
abs, sign
acos, asin, atan, atan2
sin, cos, tan
ceiling, floor, round, trunc, signif
exp, log, log10, log2, sqrt

max, min, prod, sum
cummax, cummin, cumprod, cumsum, diff
pmax, pmin
range
mean, median, cor, sd, var
rle

# 関数定義に関連した関数
# Functions to do with functions
function
missing
on.exit
return, invisible

# 論理演算および集合演算
# Logical & sets 
&, |, !, xor
all, any
intersect, union, setdiff, setequal
which

# ベクトルと行列
# Vectors and matrices
c, matrix
# データ型の自動変換の優先順位 文字列 > 数値 > 論理
# automatic coercion rules character > numeric > logical
length, dim, ncol, nrow
cbind, rbind
names, colnames, rownames
t
diag
sweep
as.matrix, data.matrix

# ベクトルの生成
# Making vectors 
c
rep, rep_len
seq, seq_len, seq_along
rev
sample
choose, factorial, combn
(is/as).(character/numeric/logical/...)

# リストとデータフレーム
# Lists & data.frames 
list, unlist
data.frame, as.data.frame
split
expand.grid

# 制御構文
# Control flow 
if, &&, || (short circuiting)
for, while
next, break
switch
ifelse

# apply関数群
# Apply & friends
lapply, sapply, vapply
apply
tapply
replicate
```


## よく使われるデータ構造(Common data structures)


```r
# 時間
# Date time
ISOdate, ISOdatetime, strftime, strptime, date
difftime
julian, months, quarters, weekdays
library(lubridate)

# 文字列操作
# Character manipulation 
grep, agrep
gsub
strsplit
chartr
nchar
tolower, toupper
substr
paste
library(stringr)

# 因子
# Factors 
factor, levels, nlevels
reorder, relevel
cut, findInterval
interaction
options(stringsAsFactors = FALSE)

# 配列操作
# Array manipulation
array
dim
dimnames
aperm
library(abind)
```

## 統計学関連(Statistics)


```r
# 順序および表作成
# Ordering and tabulating 
duplicated, unique
merge
order, rank, quantile
sort
table, ftable

# 線形モデル
# Linear models 
fitted, predict, resid, rstandard
lm, glm
hat, influence.measures
logLik, df, deviance
formula, ~, I
anova, coef, confint, vcov
contrasts

# 検定関係
# Miscellaneous tests
apropos("\\.test$")

# 乱数
# Random variables 
(q, p, d, r) * (beta, binom, cauchy, chisq, exp, f, gamma, geom, 
  hyper, lnorm, logis, multinom, nbinom, norm, pois, signrank, t, 
  unif, weibull, wilcox, birthday, tukey)

# 線形代数
# Matrix algebra 
crossprod, tcrossprod
eigen, qr, svd
%*%, %o%, outer
rcond
solve
```

## Rを制御する関数群(Working with R)


```r
# Workspace 
ls, exists, rm
getwd, setwd
q
source
install.packages, library, require

# Help
help, ?
help.search
apropos
RSiteSearch
citation
demo
example
vignette

# Debugging
traceback
browser
recover
options(error = )
stop, warning, message
tryCatch, try
```

## I/O


```r
# Output
print, cat
message, warning
dput
format
sink, capture.output

# Reading and writing data
data
count.fields
read.csv, write.csv
read.delim, write.delim
read.fwf
readLines, writeLines
readRDS, saveRDS
load, save
library(foreign)

# Files and directories 
dir
basename, dirname, tools::file_ext
file.path
path.expand, normalizePath
file.choose
file.copy, file.create, file.remove, file.rename, dir.create
file.exists, file.info
tempdir, tempfile
download.file, library(downloader)
```
