---
layout: page
title: TypeHandle (llvm.core)
---

TypeHandle objects are used to create recursive types, like this linked
list node structure in C:

{% highlight c %}
struct node
{
    int data;
    struct node *next;
};
{% endhighlight %}

This can be realized in llvm-py like this:

{% highlight c %}
include../../test/typehandle.py[]
{% endhighlight %}

which gives the output:

{% highlight llvm %}
; ModuleID = 'mod1'

%struct.node = type { i32, %struct.node* }
{% endhighlight %}

For more details on what is going on here, please refer the LLVM
Programmer's Manual section [LLVM Type
Resolution](http://llvm.org/docs/ProgrammersManual.html#TypeResolve).
The TypeHandle class of llvm-py corresponds to
[llvmPATypeHolder](http://www.llvm.org/doxygen/classllvm_1_1PATypeHolder.html)
in C++. The above example is available as
[test/typehandle.py](http://code.google.com/p/llvm-py/source/browse/trunk/test/typehandle.py)
in the source distribution.

* * *

# llvm.core.TypeHandle

## Static Constructors

### `new(abstract_ty)`

create a new `TypeHandle` instance, which holds a reference to the
given abstract type `abstract_ty`. Typically, the abstract type used
is `Type.opaque()`.

## Properties

### `type`

returns the contained type. Typically the `refine` method is called
on the returned type.




