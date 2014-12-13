# OO field guide

# OO field guide {#oo}
```
This chapter is a field guide for recognising and working with R's objects in the wild. R has three object oriented systems (plus the base types), so it can be a bit intimidating. The goal of this guide is not to make you an expert in all four systems, but to help you identify which system you're working with and to help you use it effectively. \index{object-oriented programming}
```

本章は実務（in the wild） においてRのオブジェクトを使いこなすためのフィールドガイドです。Rには３つのオブジェクト指向型クラスシステム(object oriented systems)（及び基本のデータ型(base types)）があり、それゆえ大仰な(intimidating)印象があります。本章の目的は、あなたをこの４つのオブジェクトシステムのエキスパートにすることではなく、あなたが使っているのがどのクラスシステムであるかを認識し、どのようにすれば効率よくそれを使えるようになるかを手助けすることにあります。

```
Central to any object-oriented system are the concepts of class and method. A __class__ defines the behaviour of __objects__ by describing their attributes and their relationship to other classes. The class is also used when selecting __methods__, functions that behave differently depending on the class of their input. Classes are usually organised in a hierarchy: if a method does not exist for a child, then the parent's method is used instead; the child __inherits__ behaviour from the parent.
```
いかなるオブジェクト指向型クラスシステムにおいても中心となるのはクラスとメソッドです。 __クラス__ においては __オブジェクト__ の属性(attributes)と他クラスとの関係(relationship)を記述することで、そのクラスの挙動を定義します。クラスはメソッドを選ぶ際にも用いられます（ __*ここの文章はよくわかりません*__ ）。メソッドとはクラスの入力に従ってその挙動を変える関数のことです。通常、クラスは階層構造を持っており、子クラスは親クラスの挙動を __継承__ します。例えば、あるメソッドが子クラスに存在しない場合、親クラスのメソッドが代わりに呼び出されます。

```
R's three OO systems differ in how classes and methods are defined:

* __S3__ implements a style of OO programming called generic-function OO. 
  This is different from most programming languages, like Java, C++, and C#,
  which implement message-passing OO. With message-passing, messages (methods)
  are sent to objects and the object determines which function to call.
  Typically, this object has a special appearance in the method call, usually
  appearing before the name of the method/message: e.g., 
  `canvas.drawRect("blue")`. S3 is different. While computations are still
  carried out via methods, a special type of function called a 
  __generic function__ decides which method to call, e.g., 
  `drawRect(canvas, "blue")`. S3 is a very casual system. It has no 
  formal definition of classes.
```

* __S4__ works similarly to S3, but is more formal. There are two major
  differences to S3. S4 has formal class definitions, which describe the
  representation and inheritance for each class, and has special helper
  functions for defining generics and methods. S4 also has multiple dispatch,
  which means that generic functions can pick methods based on the class of 
  any number of arguments, not just one.

* __Reference classes__, called RC for short, are quite different from S3 
  and S4. RC implements message-passing OO, so methods belong to classes, 
  not functions. `$` is used to separate objects and methods, so method calls
  look like `canvas$drawRect("blue")`. RC objects are also mutable: they don't
  use R's usual copy-on-modify semantics, but are modified in place. This 
  makes them harder to reason about, but allows them to solve problems that 
  are difficult to solve with S3 or S4.

There's also one other system that's not quite OO, but it's important to mention here:

* __base types__, the internal C-level types that underlie the other OO 
  systems. Base types are mostly manipulated using C code, but they're 
  important to know about because they provide the building blocks for the 
  other OO systems.

The following sections describe each system in turn, starting with base types. You'll learn how to recognise the OO system that an object belongs to, how method dispatch works, and how to create new objects, classes, generics, and methods for that system. The chapter concludes with a few remarks on when to use each system.

##### Prerequisites

You'll need the pryr package, `install.packages("pryr")`, to access useful functions for examining OO properties.

##### Quiz

Think you know this material already? If you can answer the following questions correctly, you can safely skip this chapter. Find the answers at the end of the chapter in [answers](#oo-answers).

1. How do you tell what OO system (base, S3, S4, or RC) an object is 
   associated with?

1. How do you determine the base type (like integer or list) of an object?

1. What is a generic function?

1. What are the main differences between S3 and S4? What are the main 
   differences between S4 & RC?
