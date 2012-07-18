---
layout: page
title: Types (llvm.core)
---

Types are what you think they are. A instance of `llvm.core.Type`, or
one of its derived classes, represent a type. llvm-py does not use as
many classes to represent types as does LLVM itself. Some types are
represented using `llvm.core.Type` itself and the rest are represented
using derived classes of `llvm.core.Type`. As usual, an instance is created
via one of the static methods of `Type`. These methods return an
instance of either `llvm.core.Type` itself or one of its derived
classes.

The following table lists all the available types along with the static
method which has to be used to construct it and the name of the class whose
object is actually returned by the static method.


Name | Constructor Method | Class |
-----|:------------------:|:-----:|
integer of bitwidth *n* | Type.int(n) | IntegerType |
32-bit float | Type.float() | Type |
64-bit double | Type.double() | Type |
80-bit float | Type.x86_fp80() | Type |
128-bit float (112-bit mantissa) | Type.fp128() | Type |
128-bit float (two 64-bits) | Type.ppc_fp128() | Type |
function | Type.function(r, p, v) | FunctionType |
unpacked struct | Type.struct(eltys) | StructType |
packed struct | Type.packed_struct(eltys) | StructType |
array | Type.array(elty, count) | ArrayType |
pointer to value of type *pty* | Type.pointer(pty, addrspc) | PointerType |
vector | Type.vector(elty, count) | VectorType |
void | Type.void() | Type |
label | Type.label() | Type |
opaque | Type.opaque() | Type |

<br/>


The class hierarchy is:


    Type
        IntegerType
        FunctionType
        StructType
        ArrayType
        PointerType
        VectorType


The class-level documentation follows:

* * *

**Index**

* This will become a table of contents (this text will be scraped).
{:toc}

* * *

# llvm.core.Type


## Static Constructors

### `int(n)`

Create an integer type of bit width `n`.

### `float()`

Create a 32-bit floating point type.


### `double()`

Create a 64-bit floating point type.


### `x86_fp80()`

Create a 80-bit 80x87-style floating point type.


### `fp128()`

Create a 128-bit floating point type (112-bit mantissa).


### `ppc_fp128()`

Create a 128-bit float (two 64-bits).


### `function(ret, params, vararg=False)`

Create a function type, having the return type `ret` (must be a
`Type`), accepting the parameters `params`, where `params` is an
iterable, that yields `Type` objects representing the type of
each function argument in order. If `vararg` is `True`, function is
variadic.


### `struct(eltys)`

Create an unpacked structure. `eltys` is an iterable, that yields
`Type` objects representing the type of each element in order.


### `packed_struct(eltys)`

Like `struct(eltys)`, but creates a packed struct.


### `array(elty, count)`

Creates an array type, holding `count` elements, each of type `elty`
(which should be a `Type`).


### `pointer(pty, addrspc=0)`

Create a pointer to type `pty` (which should be a `Type`). `addrspc`
is an integer that represents the address space of the pointer (see
LLVM docs or ask on llvm-dev for more info).


### `void()`

Creates a void type. Used for function return types.


### `label()`

Creates a label type.


### `opaque()`

Opaque type, used for creating self-referencing types.

## Properties


### `kind` \[read-only]

A value (enum) representing the "type" of the object. It will be
one of the following constants defined in `llvm.core`:

{% highlight python %}
# Warning: do not rely on actual numerical values!
TYPE_VOID       = 0
TYPE_FLOAT      = 1
TYPE_DOUBLE     = 2
TYPE_X86_FP80   = 3
TYPE_FP128      = 4
TYPE_PPC_FP128  = 5
TYPE_LABEL      = 6
TYPE_INTEGER    = 7
TYPE_FUNCTION   = 8
TYPE_STRUCT     = 9
TYPE_ARRAY      = 10
TYPE_POINTER    = 11
TYPE_OPAQUE     = 12
TYPE_VECTOR     = 13
TYPE_METADATA   = 14
TYPE_UNION      = 15
{% endhighlight %}

#### Example:
{% highlight python %}
assert Type.int().kind == TYPE_INTEGER
assert Type.void().kind == TYPE_VOID
{% endhighlight %}

## Methods

### `refine`

Used for constructing self-referencing types. See the documentation
of `TypeHandle` objects.

## Special Methods

### `__str__`

`Type` objects can be stringified into it's LLVM assembly language
representation.

### `__eq__`

`Type` objects can be compared for equality. Internally, this
converts both arguments into their LLVM assembly representations and
compares the resultant strings.

* * *

# llvm.core.IntegerType

## Base Class

- `llvm.core.Type`

## Properties


### `width` \[read-only]

The width of the integer type, in number of bits.

* * *

# llvm.core.FunctionType

## Base Class

- `llvm.core.Type`

## Properties


### `return_type` \[read-only]

A `Type` object, representing the return type of the function.

### `vararg` \[read-only]

`True` if the function is variadic.


### `args` \[read-only]

Returns an iterable object that yields `Type` objects that
represent, in order, the types of the arguments accepted by the
function. Used like this:

{% highlight python %}
func_type = Type.function( Type.int(), [ Type.int(), Type.int() ] )
for arg in func_type.args:
    assert arg.kind == TYPE_INTEGER
    assert arg == Type.int()
assert func_type.arg_count == len(func_type.args)
{% endhighlight %}


### `arg_count` \[read-only]

The number of arguments. Same as `len(obj.args)`, but faster.

* * *

# llvm.core.StructType

## Base Class

- `llvm.core.Type`

## Properties


### `packed` \[read-only]

`True` if the structure is packed (no padding between elements).


### `elements` \[read-only]

Returns an iterable object that yields `Type` objects that
represent, in order, the types of the elements of the structure.
Used like this:

{% highlight python %}
struct_type = Type.struct( [ Type.int(), Type.int() ] )
for elem in struct_type.elements:
    assert elem.kind == TYPE_INTEGER
    assert elem == Type.int()
assert struct_type.element_count == len(struct_type.elements)
{% endhighlight %}


### `element_count` \[read-only]

The number of elements. Same as `len(obj.elements)`, but faster.

* * *

# llvm.core.ArrayType

## Base Class

- `llvm.core.Type`

## Properties


### `element` \[read-only]

A `Type` object representing the type of the element of the array.


### `count` \[read-only]

The number of elements in the array.

* * *


# llvm.core.PointerType

## Base Class

- `llvm.core.Type`

## Properties


### `address_space` \[read-only]

The address space of the pointer.


### `pointee` \[read-only]

A `Type` object representing the type of the value pointed to.

* * *


# llvm.core.VectorType

## Base Class

- `llvm.core.Type`

## Properties


### `element` \[read-only]

A `Type` object representing the type of the element of the vector.


### `count` \[read-only]

The number of elements in the vector.

* * *

# An Example

Here is an example that demonstrates the creation of types:

{% highlight python %}
#!/usr/bin/env python

# integers
int_ty      = Type.int()
bool_ty     = Type.int(1)
int_64bit   = Type.int(64)

# floats
sprec_real  = Type.float()
dprec_real  = Type.double()

# arrays and vectors
intar_ty    = Type.array( int_ty, 10 )     # "typedef int intar_ty[10];"
twodim      = Type.array( intar_ty , 10 )  # "typedef int twodim[10][10];"
vec         = Type.array( int_ty, 10 )

# structures
s1_ty       = Type.struct( [ int_ty, sprec_real ] )
    # "struct s1_ty { int v1; float v2; };"

# pointers
intptr_ty   = Type.pointer(int_ty)         # "typedef int *intptr_ty;"

# functions
f1 = Type.function( int_ty, [ int_ty ] )
    # functions that take 1 int_ty and return 1 int_ty

f2 = Type.function( Type.void(), [ int_ty, int_ty ] )
    # functions that take 2 int_tys and return nothing

f3 = Type.function( Type.void(), ( int_ty, int_ty ) )
    # same as f2; any iterable can be used

fnargs = [ Type.pointer( Type.int(8) ) ]
printf = Type.function( Type.int(), fnargs, True ) # variadic function
{% endhighlight %}

* * *

# TypeHandle (llvm.core)

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




