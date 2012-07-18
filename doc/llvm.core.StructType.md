---
layout: page
title: StructType (llvm.core)
---

# llvm.core.StructType

## Base Class

- [llvm.core.Type](llvm.core.Type.html)

## Properties


### `packed` \[read-only\]

`True` if the structure is packed (no padding between elements).


### `elements` \[read-only\]

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


### `element_count` \[read-only\]

The number of elements. Same as `len(obj.elements)`, but faster.

