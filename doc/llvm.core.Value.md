---
layout: page
title: Value (llvm.core)
---

`llvm.core.Value` is the base class of all values computed by a program
that may be used as operands to other values. A value has a type
associated with it (an object of `llvm.core.Type`).

The class hierarchy is:


    Value
      User
        Constant
          ConstantExpr
          ConstantAggregateZero
          ConstantInt
          ConstantFP
          ConstantArray
          ConstantStruct
          ConstantVector
          ConstantPointerNull
          UndefValue
          GlobalValue
            GlobalVariable
            Function
        Instruction
          CallOrInvokeInstruction
          PHINode
          SwitchInstruction
          CompareInstruction
      Argument
      BasicBlock


The `Value` class is abstract, it's not meant to be instantiated. `User`
is a `Value` that in turn uses (i.e., can refer to) other values (for
e.g., a constant expression 1+2 refers to two constant values 1 and 2).

`Constant`-s represent constants that appear within code or as
initializers of globals. They are constructed using static methods of
`Constant`. Various types of constants are represented by various
subclasses of `Constant`. However, most of them are empty and do
not provide any additional attributes or methods over `Constant`.

The `Function` object represents an instance of a function type.
Such objects contain `Argument` objects, which represent the actual,
local-variable-like arguments of the function (not to be confused with
the arguments returned by a function _type_ object -- these represent
the _type_ of the arguments).

The various `Instruction`-s are created by the `Builder` class. Most
instructions are represented by `Instruction` itself, but there are
a few subclasses that represent interesting instructions.

`Value` objects have a type (read-only), and a name (read-write).

* * *

**Index**

* This will become a table of contents (this text will be scraped).
{:toc}

* * *

# llvm.core.Value

## Properties
### `name`

The name of the value.

### `type` \[read-only]

An `llvm.core.Type` object representing the type of the value.

### `uses` \[read-only]

The list of values (`llvm.core.Value`) that use this value.

### `use_count` \[read-only]

The number of values that use (refer) this value. Same as `len(val.uses)`
but faster if you just want the count.

### `value_id` \[read-only]

Returns `llvmValuegetValueID()`. Refer LLVM documentation
for more info.

## Special Methods

### `__str__`

`Value` objects can be stringified into it's LLVM assembly language
representation.

### `__eq__`

`Value` objects can be compared for equality. Internally, this
converts both arguments into their LLVM assembly representations and
compares the resultant strings.


* * *

# User (llvm.core)

`User`-s are values that refer to other values. The values so refered
can be retrived by the properties of `User`. This is the reverse of
the `Value.uses`. Together these can be used to traverse the use-def
chains of the SSA.

* * *


# llvm.core.User # {#user}
## Base Class
- `llvm.core.Value`

## Properties

### `operands` \[read-only]

The list of operands (values, of type `llvm.core.Value`) that this
value refers to.

### `operand_count` \[read-only]

The number of operands that this value referes to. Same as
`len(uses.operands)` but faster if you just want the count.

* * *

# Constants (llvm.core)

`Constant`-s represents constants that appear within the code. The
values of such objects are known at creation time. Constants can be
created from Python constants. A constant expression is also a constant
-- given a `Constant` object, an operation (like addition, subtraction
etc) can be specified, to yield a new `Constant` object. Let's see some
examples:

{% highlight python %}
#!/usr/bin/env python

ti = Type.int()                         # a 32-bit int type

k1 = Constant.int(ti, 42)               # "int k1 = 42;"
k2 = k1.add( Constant.int( ti, 10 ) )   # "int k2 = k1 + 10;"

tr = Type.float()

r1 = Constant.real(tr, "3.141592")      # create from a string
r2 = Constant.real(tr, 1.61803399)      # create from a Python float
{% endhighlight %}

# llvm.core.Constant

## Static factory methods

### `null(ty)`

A null value (all zeros) of type `ty`

### `all_ones(ty)`

All 1's value of type `ty`

### `undef(ty)`

An undefined value of type `ty`

### `int(ty, value)`

Integer of type `ty`, with value `value` (a Python int or long)

### `int_signextend(ty, value)`

Integer of signed type `ty` (use for signed types)

### `real(ty, value)`

Floating point value of type `ty`, with value `value` (a Python float)

### `stringz(value)`

A null-terminated string. `value` is a Python string

### `string(value)`

As `string(ty)`, but not null terminated

### `array(ty, consts)`

Array of type `ty`, initialized with `consts` (an iterable yielding `Constant`
objects of the appropriate type)

### `struct(ty, consts)`

Struct (unpacked) of type `ty`, initialized with `consts` (an iterable yielding
`Constant` objects of the appropriate type)

### `packed_struct(ty, consts)`

As `struct(ty, consts)` but packed

### `vector(consts)`

Vector, initialized with `consts` (an iterable yielding `Constant` objects of
the appropriate type)

### `sizeof(ty)`

Constant value representing the sizeof the type `ty`


## Methods

The following operations on constants are supported. For more details on
any operation, consult the
[Constant Expressions](http://www.llvm.org/docs/LangRef.html#constantexprs)
section of the LLVM Language Reference.

### `k.neg()`

negation, same as `0 - k`

### `k.not_()`

1's complement of `k`. Note trailing underscore.

### `k.add(k2)`

`k + k2`, where `k` and `k2` are integers.


### `k.fadd(k2)`

`k + k2`, where `k` and `k2` are floating-point.

### `k.sub(k2)`

`k - k2`, where `k` and `k2` are integers.

### `k.fsub(k2)`

`k - k2`, where `k` and `k2` are floating-point.

### `k.mul(k2)`

`k * k2`, where `k` and `k2` are integers.

### `k.fmul(k2)`

`k * k2`, where `k` and `k2` are floating-point.

### `k.udiv(k2)`

Quotient of unsigned division of `k` with `k2`

### `k.sdiv(k2)`

Quotient of signed division of `k` with `k2`

### `k.fdiv(k2)`

Quotient of floating point division of `k` with `k2`

### `k.urem(k2)`

Reminder of unsigned division of `k` with `k2`

### `k.srem(k2)`

Reminder of signed division of `k` with `k2`

### `k.frem(k2)`

Reminder of floating point division of `k` with `k2`

### `k.and_(k2)`

Bitwise and of `k` and `k2`. Note trailing underscore.

### `k.or_(k2)`

Bitwise or of `k` and `k2`. Note trailing underscore.

### `k.xor(k2)`

Bitwise exclusive-or of `k` and `k2`.

### `k.icmp(icmp, k2)`

Compare `k` with `k2` using the predicate `icmp`. See [below](#icmp) for
list of predicates for integer operands.

### `k.fcmp(fcmp, k2)`

Compare `k` with `k2` using the predicate `fcmp`. See [below](#fcmp) for list
of predicates for real operands.

### `k.shl(k2)`

Shift `k` left by `k2` bits.

### `k.lshr(k2)`

Shift `k` logically right by `k2` bits (new bits are 0s).

### `k.ashr(k2)`

Shift `k` arithmetically right by `k2` bits (new bits are same as previous sign bit).

### `k.gep(indices)`

GEP, see [LLVM docs](http://www.llvm.org/docs/GetElementPtr.html).

### `k.trunc(ty)`

Truncate `k` to a type `ty` of lower bitwidth.

### `k.sext(ty)`

Sign extend `k` to a type `ty` of higher bitwidth, while extending the sign bit.

### `k.zext(ty)`

Sign extend `k` to a type `ty` of higher bitwidth, all new bits are 0s.

### `k.fptrunc(ty)`

Truncate floating point constant `k` to floating point type `ty` of lower size
than k's.

### `k.fpext(ty)`

Extend floating point constant `k` to floating point type `ty` of higher size
than k's.

### `k.uitofp(ty)`

Convert an unsigned integer constant `k` to floating point constant of
type `ty`.

### `k.sitofp(ty)`

Convert a signed integer constant `k` to floating point constant of type `ty`.

### `k.fptoui(ty)`

Convert a floating point constant `k` to an unsigned integer constant of type `ty`.

### `k.fptosi(ty)`

Convert a floating point constant `k` to a signed integer constant of type `ty`.

### `k.ptrtoint(ty)`

Convert a pointer constant `k` to an integer constant of type `ty`.

### `k.inttoptr(ty)`

Convert an integer constant `k` to a pointer constant of type `ty`.

### `k.bitcast(ty)`

Convert `k` to a (equal-width) constant of type `ty`.

### `k.select(cond,k2,k3)`

Replace value with `k2` if the 1-bit integer constant `cond` is 1,
else with `k3`.

### `k.extract_element(idx)`

Extract value at `idx` (integer constant) from a vector constant `k`.

### `k.insert_element(k2,idx)`

Insert value `k2` (scalar constant) at index `idx` (integer constant) of vector
constant `k`.

### `k.shuffle_vector(k2,mask)`

Shuffle vector constant `k` based on vector constants `k2` and `mask`.


* * *

# Comparison Operations

## Integer Comparision ## {#icmp}

Predicates for use with `icmp` instruction are listed below. All
of these are integer constants defined in the `llvm.core` module.

### `ICMP_EQ`

Equality

### `ICMP_NE`

Inequality

### `ICMP_UGT`

Unsigned greater than

### `ICMP_UGE`

Unsigned greater than or equal

### `ICMP_ULT`

Unsigned less than

### `ICMP_ULE`

Unsigned less than or equal

### `ICMP_SGT`

Signed greater than

### `ICMP_SGE`

Signed greater than or equal

### `ICMP_SLT`

Signed less than

### `ICMP_SLE`

Signed less than or equal

## Float Comparision ## {#fcmp}

Predicates for use with `fcmp` instruction are listed below. All
of these are integer constants defined in the `llvm.core` module.

### `FCMP_FALSE`

Always false

### `FCMP_OEQ`

True if ordered and equal

### `FCMP_OGT`

True if ordered and greater than

### `FCMP_OGE`

True if ordered and greater than or equal

### `FCMP_OLT`

True if ordered and less than

### `FCMP_OLE`

True if ordered and less than or equal

### `FCMP_ONE`

True if ordered and operands are unequal

### `FCMP_ORD`

True if ordered (no NaNs)

### `FCMP_UNO`

True if unordered: `isnan(X) | isnan(Y)`

### `FCMP_UEQ`

True if unordered or equal

### `FCMP_UGT`

True if unordered or greater than

### `FCMP_UGE`

True if unordered, greater than or equal

### `FCMP_ULT`

True if unordered, or less than

### `FCMP_ULE`

True if unordered, less than or equal

### `FCMP_UNE`

True if unordered or not equal

### `FCMP_TRUE`

Always true

# Other Constant Classes (llvm.core)

The following subclasses of `Constant` do not provide additional
methods, **they serve only to provide richer type information.**


Subclass | LLVM C++ Class | Remarks |
---------|----------------|---------|
`ConstantExpr` | `llvmConstantExpr` | A constant expression |
`ConstantAggregateZero`| `llvmConstantAggregateZero`| All-zero constant |
`ConstantInt`| `llvmConstantInt`| An integer constant |
`ConstantFP`| `llvmConstantFP`| A floating-point constant |
`ConstantArray`| `llvmConstantArray`| An array constant |
`ConstantStruct`| `llvmConstantStruct`| A structure constant |
`ConstantVector`| `llvmConstantVector`| A vector constant |
`ConstantPointerNull`| `llvmConstantPointerNull`| All-zero pointer constant |
`UndefValue`| `llvmUndefValue`| corresponds to `undef` of LLVM IR |


These types are helpful in `isinstance` checks, like so:

{% highlight python %}
ti = Type.int(32)
k1 = Constant.int(ti, 42)           # int32_t k1 = 42;
k2 = Constant.array(ti, [k1, k1])   # int32_t k2[] = { k1, k1 };

assert isinstance(k1, ConstantInt)
assert isinstance(k2, ConstantArray)
{% endhighlight %}

* * *

# Global Value (llvm.core)

The class `llvm.core.GlobalValue` represents module-scope aliases, variables
and functions. Global variables are represented by the sub-class
`llvm.core.GlobalVariable` and functions by `llvm.core.Function`.

Global values have the read-write properties `linkage`, `section`,
`visibility` and `alignment`. Use one of the following constants (from
llvm.core) as values for `linkage`
(see [LLVM documentaion](http://www.llvm.org/docs/LangRef.html#linkage) for
details on each):


Value | Equivalent LLVM Assembly Keyword |
------|----------------------------------|
`LINKAGE_EXTERNAL` | `externally_visible` |
`LINKAGE_AVAILABLE_EXTERNALLY` | `available_externally` |
`LINKAGE_LINKONCE_ANY` | `linkonce` |
`LINKAGE_LINKONCE_ODR` | `linkonce_odr` |
`LINKAGE_WEAK_ANY` | `weak` |
`LINKAGE_WEAK_ODR` | `weak_odr` |
`LINKAGE_APPENDING` | `appending` |
`LINKAGE_INTERNAL` | `internal` |
`LINKAGE_PRIVATE` | `private` |
`LINKAGE_DLLIMPORT` | `dllimport` |
`LINKAGE_DLLEXPORT` | `dllexport` |
`LINKAGE_EXTERNAL_WEAK` | `extern_weak` |
`LINKAGE_GHOST` | deprecated -- do not use |
`LINKAGE_COMMON` | `common` |
`LINKAGE_LINKER_PRIVATE` | `linker_private` |

<br/>

The `section` property can be assigned strings (like ".rodata"), which
will be used if the target supports it. Visibility property can be set
to one of thse constants (from llvm.core, see also
[LLVM docs](http://www.llvm.org/docs/LangRef.html#visibility)):


Value | Equivalent LLVM Assembly Keyword |
------|----------------------------------|
`VISIBILITY_DEFAULT` | `default` |
`VISIBILITY_HIDDEN` | `hidden` |
`VISIBILITY_PROTECTED` | `protected` |

<br/>


The `alignment` property can be 0 (default), or can be set to a power of 2.
The read-only property `is_declaration` can be used to check if the
global is a declaration or not. The module to which the global belongs
to can be retrieved using the `module` property (read-only).

* * *


# llvm.core.GlobalValue

## Base Class

- `llvm.core.Constant`

## Properties

### `linkage`

The linkage type, takes one of the constants listed above (LINKAGE_\*).

### `section`

A string like ".rodata", indicating the section into which the
global is placed into.

### `visibility`

The visibility type, takes one of the constants listed above (VISIBILITY_\*).

### `alignment`

A power-of-2 integer indicating the boundary to align to.

### `is_declaration` \[read-only]

`True` if the global is a declaration, `False` otherwise.

### `module` \[read-only]

    The module object to which this global belongs to.

* * *

# Global Variable (llvm.core)

Global variables (`llvm.core.GlobalVariable`) are subclasses of
`llvm.core.GlobalValue` and represent module-level variables. These can
have optional initializers and can be marked as constants. Global
variables can be created either by using the `add_global_variable`
method of the `Module` class (see above), or by using the static method
`GlobalVariable.new`.

{% highlight python %}
# create a global variable using add_global_variable method
gv1 = module_obj.add_global_variable(Type.int(), "gv1")

# or equivalently, using a static constructor method
gv2 = GlobalVariable.new(module_obj, Type.int(), "gv2")
{% endhighlight %}

Existing global variables of a module can be accessed by name using
`module_obj.get_global_variable_named(name)` or `GlobalVariable.get`.
All existing global variables can be enumerated via iterating over the
property `module_obj.global_variables`.

{% highlight python %}
# retrieve a reference to the global variable gv1,
# using the get_global_variable_named method
gv1 = module_obj.get_global_variable_named("gv1")

# or equivalently, using the static `get` method:
gv2 = GlobalVariable.get(module_obj, "gv2")

# list all global variables in a module
for gv in module_obj.global_variables:
    print gv.name, "of type", gv.type
{% endhighlight %}

The initializer for a global variable can be set by assigning to the
`initializer` property of the object. The `is_global_constant` property
can be used to indicate that the variable is a global constant.

Global variables can be delete using the `delete` method. Do not use the
object after calling `delete` on it.

{% highlight python %}
# add an initializer 10 (32-bit integer)
gv.initializer = Constant.int( Type.int(), 10 )

# delete the global
gv.delete()
# DO NOT dereference `gv' beyond this point!
gv = None
{% endhighlight %}

# llvm.core.GlobalVariable
## Base Class

- `llvm.core.GlobalValue`

## Static Constructors

### `new(module_obj, ty, name)`

Create a global variable named `name` of type `ty` in the module
`module_obj` and return a `GlobalVariable` object that represents it.

### `get(module_obj, name)`

Return a `GlobalVariable` object to represent the global variable
named `name` in the module `module_obj` or raise `LLVMException` if
such a variable does not exist.

## Properties

### `initializer`

The intializer of the variable. Set to `llvm.core.Constant` (or
derived). Gets the initializer constant, or `None` if none exists.
`global_constant`
`True` if the variable is a global constant, `False` otherwise.

## Methods

### `delete()`
Deletes the global variable from it's module. _Do not hold any
references to this object after calling `delete` on it._

* * *


# Function (llvm.core)

Functions are represented by `llvm.core.Function` objects. They are
contained within modules, and can be created either with the method
`module_obj.add_function` or the static constructor `Function.new`.
References to functions already present in a module can be retrieved via
`module.get_function_named` or by the static constructor method
`Function.get`. All functions in a module can be enumerated by iterating
over `module_obj.functions`.

{% highlight python %}
# create a type, representing functions that take an integer and return
# a floating point value.
ft = Type.function( Type.float(), [ Type.int() ] )

# create a function of this type
f1 = module_obj.add_function(ft, "func1")

# or equivalently, like this:
f2 = Function.new(module_obj, ft, "func2")

# get a reference to an existing function
f3 = module_obj.get_function_named("func3")

# or like this:
f4 = Function.get(module_obj, "func4")

# list all function names in a module
for f in module_obj.functions:
    print f.name
{% endhighlight %}


## Intrinsic

References to intrinsic functions can be got via the static constructor
`intrinsic`. This returns a `Function` object, calling which is
equivalent to invoking the intrinsic. The `intrinsic` method has to be
called with a module object, an instrinic ID (which is a numeric
constant) and a list of the types of arguments (which LLVM uses to
resolve overloaded intrinsic functions).

{% highlight python %}
# get a reference to the llvm.bswap intrinsic
bswap = Function.intrinsic(mod, INTR_BSWAP, [Type.int()])

# call it
builder.call(bswap, [value])
{% endhighlight %}

Here, the constant `INTR_BSWAP`, available from `llvm.core`, represents the
LLVM intrinsic [llvm.bswap](http://www.llvm.org/docs/LangRef.html#int_bswap).
The `[Type.int()]` selects the version of `llvm.bswap` that has a single 32-bit
integer argument. The list of intrinsic IDs defined as integer constants
in `llvm.core`. These are:

{% include intrinsics.csv %}

There are also target-specific intrinsics (which correspond to that
target's CPU instructions) available, but are omitted here for brevity.
Full list can be seen from
[_intrinsic_ids.py](https://github.com/numba/llvm-py/blob/master/llvm/_intrinsic_ids.py).
See the [LLVM Language Reference](http://www.llvm.org/docs/LangRef.html)
for more information on the intrinsics, and the
[test](https://github.com/numba/llvm-py/blob/master/test/intrinsic.py)
directory in the source distribution for more examples. The intrinsic ID
can be retrieved from a function object with the read-only property
`intrinsic_id`.

> **Auto-generation of Intrinsic IDs**
>
>
> A script (tool/intrgen.py in source tree) generates the intrinsic IDs
> automatically. This is necessary when compiling llvm-py with a different
> version of LLVM.

## Calling Convention ## {#callconv}
The function's calling convention can be set using the
`calling_convention` property. The following (integer) constants defined
in `llvm.core` can be used as values:

Value | Equivalent LLVM Assembly Keyword |
------|----------------------------------|
`CC_C` | `ccc` |
`CC_FASTCALL` | `fastcc` |
`CC_COLDCALL` |  `coldcc` |
`CC_X86_STDCALL` | `x86_stdcallcc` |
`CC_X86_FASTCALL` | `x86_fastcallcc` |

See the [LLVM docs](http://www.llvm.org/docs/LangRef.html#callingconv) for
more information on each. Backend-specific numbered conventions can be
directly passed as integers.

An arbitrary string identifying which garbage collector to use can be
set or got with the property `collector`.

The value objects corresponding to the arguments of a function can be
got using the read-only property `args`. These can be iterated over, and
also be indexed via integers. An example:

{% highlight python %}
# list all argument names and types
for arg in fn.args:
    print arg.name, "of type", arg.type

# change the name of the first argument
fn.args[0].name = "objptr"
{% endhighlight %}

Basic blocks (see later) are contained within functions. When newly
created, a function has no basic blocks. They have to be added
explicitly, using the `append_basic_block` method, which adds a new,
empty basic block as the last one in the function. The first basic block
of the function can be retrieved using the `get_entry_basic_block`
method. The existing basic blocks can be enumerated by iterating over
using the read-only property `basic_blocks`. The number of basic blocks
can be got via `basic_block_count` method. Note that
`get_entry_basic_block` is slightly faster than `basic_blocks[0]` and so
is `basic_block_count`, over `len(f.basic_blocks)`.

{% highlight python %}
# add a basic block
b1 = fn.append_basic_block("entry")

# get the first one
b2 = fn.get_entry_basic_block()
b2 = fn.basic_blocks[0]  # slower than previous method

# print names of all basic blocks
for b in fn.basic_blocks:
    print b.name

# get number of basic blocks
n = fn.basic_block_count
n = len(fn.basic_blocks)  # slower than previous method
{% endhighlight %}

Functions can be deleted using the method `delete`. This deletes them
from their containing module. All references to the function object
should be dropped after `delete` has been called.

Functions can be verified with the `verify` method. Note that this may
not work properly (aborts on errors).

## Function Attributes ## {#fnattr}
Function attributes, as documented
[here](http://www.llvm.org/docs/LangRef.html#fnattrs), can be
set on functions using the methods `add_attribute` and
`remove_attribute`. The following values may be used to refer to the
LLVM attributes:


Value | Equivalent LLVM Assembly Keyword |
------|----------------------------------|
`ATTR_ALWAYS_INLINE`|`alwaysinline` |
`ATTR_INLINE_HINT`|`inlinehint` |
`ATTR_NO_INLINE`|`noinline` |
`ATTR_OPTIMIZE_FOR_SIZE`|`optsize` |
`ATTR_NO_RETURN`|`noreturn` |
`ATTR_NO_UNWIND`|`nounwind` |
`ATTR_READ_NONE`|`readnone` |
`ATTR_READONLY`|`readonly` |
`ATTR_STACK_PROTECT`|`ssp` |
`ATTR_STACK_PROTECT_REQ`|`sspreq` |
`ATTR_NO_REDZONE`|`noredzone` |
`ATTR_NO_IMPLICIT_FLOAT`|`noimplicitfloat` |
`ATTR_NAKED`|`naked` |


Here is how attributes can be set and removed:

{% highlight python %}
# create a function
ti = Type.int(32)
tf = Type.function(ti, [ti, ti])
m = Module.new('mod')
f = m.add_function(tf, 'sum')
print f
#   declare i32 @sum(i32, i32)

# add a couple of attributes
f.add_attribute(ATTR_NO_UNWIND)
f.add_attribute(ATTR_READONLY)
print f
#   declare i32 @sum(i32, i32) nounwind readonly
{% endhighlight %}


* * *

# llvm.core.Function

## Base Class

- `llvm.core.GlobalValue`

## Static Constructors

### `new(module_obj, func_ty, name)`

Create a function named `name` of type `func_ty` in the module
`module_obj` and return a `Function` object that represents it.

### `get(module_obj, name)`

Return a `Function` object to represent the function
named `name` in the module `module_obj` or raise `LLVMException` if
such a function does not exist.

### `get_or_insert(module_obj, func_ty, name)`

Similar to `get`, except that if the function does not exist it
is added first, as though with `new`.

### `intrinsic(module_obj, intrinsic_id, types)`

Create and return a `Function` object that refers to an intrinsic
function, as described [above](#intrinsic).

## Properties

###  `calling_convention`

The calling convention for the function, as listed [above](#callconv).

### `collector`

A string holding the name of the garbage collection algorithm.
See [LLVM docs](http://www.llvm.org/docs/LangRef.html#gc).

### `does_not_throw`

Setting to True sets the `ATTR_NO_UNWIND` attribute, False
removes it. Shortcut to using `f.add_attribute(ATTR_NO_UNWIND)`
and `f.remove_attribute(ATTR_NO_UNWIND)`.

### `args` \[read-only]

List of `llvm.core.Argument` objects representing the formal
arguments of the function.

### `basic_block_count` \[read-only]

Number of basic blocks belonging to this function. Same as
`len(f.basic_blocks)` but faster if you just want the count.

### `entry_basic_block` \[read-only]

The `llvm.core.BasicBlock` object representing the entry
basic block for this function, or `None` if there are no
basic blocks.

### `basic_blocks` \[read-only]

List of `llvm.core.BasicBlock` objects representing the
basic blocks belonging to this function.

### `intrinsic_id` \[read-only]

Returns the ID of the intrinsic if this object represents an
intrinsic instruction. Otherwise 0.

## Methods

### `delete()`

Deletes the function from it's module. _Do not hold any
references to this object after calling `delete` on it.

### `append_basic_block(name)`

Add a new basic block named `name`, and return a corresponding
`llvm.core.BasicBlock` object. Note that if this is not the
entry basic block, you'll have to add appropriate branch
instructions from other basic blocks yourself.

### `add_attribute(attr)`

Add an attribute `attr` to the function, from the set listed above.

### `remove_attribute(attr)`

Remove the attribute `attr` of the function.

### `viewCFG()`

Displays the control flow graph using the GraphViz tool.

### `viewCFGOnly()`

Displays the control flow graph using the GraphViz tool, but
omitting function bodies.

### `verify()`

Verifies the function. See [LLVM docs](http://llvm.org/docs/Passes.html#verify).

* * *


# Argument (llvm.core) # {#argattrs}

The `args` property of `llvm.core.Function` objects yields
`llvm.core.Argument` objects. This allows for setting attributes for
functions arguments. `Argument` objects cannot be constructed from user
code, the only way to get a reference to these are from `Function`
objects.

The method `add_attribute` and `remove_attribute` can be used to add or
remove the following attributes:

Value| Equivalent LLVM Assembly Keyword |
-----|----------------------------------|
`ATTR_ZEXT`| `zeroext` |
`ATTR_SEXT`| `signext` |
`ATTR_IN_REG`| `inreg` |
`ATTR_BY_VAL`| `byval` |
`ATTR_STRUCT_RET`| `sret` |
`ATTR_NO_ALIAS`| `noalias` |
`ATTR_NO_CAPTURE`| `nocapture` |
`ATTR_NEST`| `nest` |

These method work exactly like the [corresponding methods](#fnattr)
of the `Function` class above. Refer
[LLVM docs](http://www.llvm.org/docs/LangRef.html#paramattrs)
for information on what each attribute means.

The alignment of any argument can be set via the `alignment`
property, to any power of 2.

* * *

# llvm.core.Argument

## Base Class

- `llvm.core.Value`

## Properties

### `alignment`

The alignment of the argument. Must be a power of 2.

## Methods

### `add_attribute(attr)`

Add an attribute `attr` to the argument, from the set listed above.

### `remove_attribute(attr)`

Remove the attribute `attr` of the argument.


* * *


# Instructions (llvm.core)

An `llvm.core.Instruction` object represents an LLVM instruction. This
class is the root of a small hierarchy:


    Instruction
        CallOrInvokeInstruction
        PHINode
        SwitchInstruction
        CompareInstruction


Instructions are not created directly, but via a builder. The builder
both creates instructions and adds them to a basic block at the same
time. One way of getting instruction objects are from basic blocks.

Being derived from [`llvm.core.User`](#user), the instruction
is-a user, i.e., an instruction in turn uses other values. The values
an instruction uses are its operands. These may be accessed using
`operands` property from the `llvm.core.User` base.

The name of the instruction (like `add`, `mul` etc) can be got
via the `opcode_name` property. The `basic_block` property gives
the basic block to which the instruction belongs to. Note that
llvm-py does not allow free-standing instruction objects (i.e.,
all instructions are created contained within a basic block).

Classes of instructions can be got via the properties
`is_terminator`, `is_binary_op`, `is_shift` etc. See below for
the full list.


# llvm.core.Instruction

## Base Class

- `llvm.core.User`

## Properties

### `basic_block` \[read-only]
The basic block to which this instruction belongs to.

### `is_terminator` \[read-only]
True if the instruction is a terminator instruction.

### `is_binary_op` \[read-only]
True if the instruction is a binary operator.

### `is_shift` \[read-only]
True if the instruction is a shift instruction.

### `is_cast` \[read-only]
True if the instruction is a cast instruction.

### `is_logical_shift` \[read-only]
True if the instruction is a logical shift instruction.

### `is_arithmetic_shift` \[read-only]
True if the instruction is an arithmetic shift instruction.

### `is_associative` \[read-only]
True if the instruction is associative.

### `is_commutative` \[read-only]
True if the instruction is commutative.

### `is_volatile` \[read-only]
True if the instruction is a volatile load or store.

### `opcode` \[read-only]
The numeric opcode value of the instruction. Do not rely
on the absolute value of this number, it may change with
LLVM version.

### `opcode_name` \[read-only]
The name of the instruction, like `add`, `sub` etc.

* * *

# CallOrInvokeInstruction (llvm.core)

The `llvm.core.CallOrInvokeInstruction` is a subclass of
`llvm.core.Instruction`, and represents either a `call` or an
`invoke` instruction.


# llvm.core.CallOrInvokeInstruction

## Base Class

- `llvm.core.Instruction`

## Properties

`calling_convention`
    Get or set the calling convention. See the link:#callconv[list above]
    for possible values.

## Methods

### `add_parameter_attribute(idx, attr)`

Add an attribute `attr` to the `idx`-th argument. See
[above](#argattrs) for possible values of `attr`.

### `remove_parameter_attribute(idx, attr)`

Remove an attribute `attr` from the `idx`-th argument. See
[above](#argattrs) for possible values of `attr`.

### `set_parameter_alignment(idx, align)`

Set the alignment of the `idx`-th argument to `align`.
`align` should be a power of two.

* * *

# PHINode (llvm.core)

The `llvm.core.PHINode` is a subclass of
`llvm.core.Instruction`, and represents the `phi` instruction. When
created (using `Builder.phi`) the phi node contains no incoming
blocks (nor their corresponding values). To add an incoming arc to
the phi node, use the `add_incoming` method, which takes a source
block (`llvm.core.BasicBlock` object) and a value (object of
`llvm.core.Value` or of a class derived from it) that the phi node
will take on if control branches in from that block.


# llvm.core.PHINode

## Base Class

- `llvm.core.Instruction`

## Properties
`incoming_count` [read-only]
    The number of incoming arcs for this phi node.

## Methods

### `add_incoming(value, block)`

Add an incoming arc, from the `llvm.core.BasicBlock` object
`block`, with the corresponding value `value`. `value` should
be an object of `llvm.core.Value` (or of a descendent class).
See [above](#argattrs) for possible values of `attr`.

### `get_incoming_value(idx)`

Returns the `idx`-th incoming arc's value.

### `get_incoming_block(idx)`

Returns the `idx`-th incoming arc's block.


# SwitchInstruction (llvm.core)

(TODO describe)

# llvm.core.SwitchInstruction

## Base Class

- `llvm.core.Instruction`

## Methods

### `add_case(const, block)`
Add another case to the switch statement. When the expression
being evaluated equals `const`, then control branches to
`block`. Here `const` must be of type `llvm.core.ConstantInt`.

* * *

# CompareInstruction (llvm.core)

(TODO describe)

# llvm.core.CompareInstruction

## Base Class

- `llvm.core.Instruction`

## Properties

###`predicate` [read-only]

The predicate of the compare instruction, one of the `ICMP_*` or
`FCMP_*` constants.

* * *

# Basic Block (llvm.core)

TODO


# Builder (llvm.core)

TODO


# Target Data (llvm.ee)

TODO


# Execution Engine (llvm.ee)

TODO. For now, see `test/example-jit.py`.


# Pass Manager and Passes (llvm.passes)

TODO. For now, see `test/passes.py`.


