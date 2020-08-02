Tips from the book: *"Fluent Python", Ramaho (2015)*.


- [Chapter 2](#chapter-2-data-structures): Data Structures
- [Chapter 3](#chapter-3-dictionaries-and-sets): Dictinaries and Sets

Chapter 2: Data Structures
===========================

Strings and Bytes
-----------------

In Python 3, strings and bytes cannot be combined.

```python
my_string = "abc"
my_byte = b"def"

print(my_string.encode() + my_byte) # it works, string->byte
print(my_string + my_byte.decode()) # it works, byte-> string
print(my_string + my_byte) # error, does not work

```

Iterate a list in reverse
-------------------------

Use the method `reversed(my_list)`. For instance:

```python
my_list=[i for i in range(10)]
for i in reversed(my_list):
    print(i)
```

Print through repr
---------------

The `__repr__` special method is called by the repr built-in to get the string representation of the object for inspection. 

```python
class Vector:
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
    def __repr__(self):
        return 'Vector(%r, %r)' % (self.x, self.y)
```

If we did not implement `__repr__` , vector instances would be shown in the console like `<Vector object at 0x10e100070>`. The string returned by `__repr__` should be unambiguous and, if possible, match the source code necessary to re-create the object being represented. That is why our chosen representation looks like calling the constructor of the class (e.g., Vector(3, 4) ).


List comprehension
------------------

The [list comprehension](https://en.wikipedia.org/wiki/List_comprehension) is a syntactic construct available in  Python for creating a list based on existing lists. It follows the form of the mathematical set-builder notation (set comprehension).

<p align="center">
<img src="./images/set_comprehension.png" width="400">
</p>


Simple list comprehension syntax:

```python
my_list = [x for x in range(10)]
```

List comprehension can be used in more complex ways. For instance, if we have two lists of different sizes (first list of size N and second of size M) we can combine them to get all the pairs (size NxM):

```python
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
tshirts = [(color, size) for color in colors for size in sizes]
print(tshirts)
#[('black', 'S'), ('black', 'M'),
#('white', 'M'), ('white', 'L')]
```

NOTE: Listcomps are a one-trick pony: they build lists. To fill up other sequence types (e.g. arrays, tuples) a **Generator Expression** is the way to go. The next section is a brief look at genexps in the context of building nonlist sequences.

Generator Expressions
---------------------

Genexps use the same syntax as listcomps, but are enclosed in parentheses rather than brackets:

```python
a = tuple(x for x in range(10))
print(a) # (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
```

Unpacking
----------

```python
lax_coordinates = (33.9425, -118.408056)
latitude, longitude = lax_coordinates # tuple unpacking
```

An elegant application of tuple unpacking is **swapping** the values of variables without using a temporary variable:

```python
b, a = a, b
```

**Using * to grab excess items.** Defining function parameters with *args to grab arbitrary excess arguments is a classic Python feature. In Python 3, this idea was extended to apply to parallel assignment as well:

```python
a, b, *rest = range(5)
print(a) # 0
print(b) # 1
print(rest) # [2, 3, 4]
```

In the context of parallel assignment, the * prefix can be applied to exactly one variable, but it can appear in any position:

```python
a, *rest, b = range(5)
print(a) # 0
print(rest) # [1, 2, 3]
print(b) # 4
```


Slicing
-------

**Why Slices and Range Exclude the Last Item.** The Pythonic convention of excluding the last item in slices and ranges works well with
the zero-based indexing used in Python, C, and many other languages. Some convenient features of the convention are:

- It's easy to see the length of a slice or range when only the stop position is given: `range(3)` and `my_list[:3]` both produce three items.
- It's easy to compute the length of a slice or range when start and stop are given: just subtract stop - start.
- It's easy to split a sequence in two parts at any index x , without overlapping: simply get `my_list[:x]` and `my_list[x:]`.


This is no secret, but worth repeating just in case: `s[a:b:c]` can be used to specify a stride or step `c`, causing the resulting slice to skip items. The stride can also be negative, returning items in reverse. Three examples make this clear:

```python
s = 'bicycle'
s[::3] # 'bye'
s[::-1] # 'elcycib'
s[::-2] # 'eccb'
```

Sorting
--------

Both list.sort and sorted take two optional, keyword-only arguments: 

- **reverse.** If True, the items are returned in descending order (i.e., by reversing the comparison of the items). Default is False.
- **key.** A one-argument function that will be applied to each item to produce its sorting key. For example, when sorting a list of strings, `key=str.lower`can be used to perform a case-insensitive sort, and `key=len` will sort the strings by character length. The default is the identity function (i.e., the items themselves are compared).

```python
fruits = ['grape', 'raspberry', 'apple', 'banana']
sorted(fruits) # ['apple', 'banana', 'grape', 'raspberry']
fruits # ['grape', 'raspberry', 'apple', 'banana']
sorted(fruits, reverse=True) #['raspberry', 'grape', 'banana', 'apple']
sorted(fruits, key=len) # ['grape', 'apple', 'banana', 'raspberry']
sorted(fruits, key=len, reverse=True) # ['raspberry', 'banana', 'grape', 'apple']
fruits # ['grape', 'raspberry', 'apple', 'banana']
fruits.sort()
fruits # ['apple', 'banana', 'grape', 'raspberry']
```

Using key lets us sort a mixed bag of numbers and number-like strings. You just need to decide whether you want to treat all items as integers or strings:

```python
l = [28, 14, '28', 5, '9', '1', 0, 6, '23', 19]
sorted(l, key=int) # [0, '1', 5, 6, '9', 14, 19, '23', 28, '28']
sorted(l, key=str) # [0, '1', 14, 19, '23', 28, '28', 5, 6, '9']
```

**Note:** the sorting algorithm used in `sorted` and `list.sort` is [Timsort](https://en.wikipedia.org/wiki/Timsort), an adaptive algorithm that switches from insertion sort to merge sort strategies, depending on how ordered the data is. This is efficient because real-world data tends to have runs of sorted items. Timsort was first deployed in CPython, in 2002. Since 2009, Timsort is also used to sort arrays in both standard Java and Android, a fact that became widely known when Oracle used some of the code related to Timsort as evidence of Google infringement of Sun’s intellectual property. Timsort was invented by Tim Peters, a Python core developer so prolific that he is believed to be an AI, the Timbot. You can read about that conspiracy theory in Python Humor.

Bisect: binary search
----------------------

The bisect module offers two main functions: bisect and insort. They use the binary search algorithm to quickly find and insert items in any sorted sequence. The call to `bisect(haystack, needle)` does a binary search for needle in haystack --which must be a sorted sequenc-- to locate the position where needle can be inserted while main‐taining haystack in ascending order. In other words, all items appearing up to that position are less than or equal to needle . You could use the result of `bisect(haystack,needle)` as the index argument to `haystack.insert(index, needle)`. However, using `insort` does both steps, and is faster.

The behavior of bisect can be fine-tuned in two ways:

- First, a pair of optional arguments, `lo` and `hi`, allow narrowing the region in the sequence to be searched when inserting. lo defaults to 0 and hi to the len() of the sequence.
- Second, bisect is actually an alias for `bisect_right`, and there is a sister function called `bisect_left`. Their difference is apparent only when the needle compares equal to an item in the list. The `bisect_right` returns an insertion point after the existing item, and the `bisect_left` returns the position of the existing item, so insertion would occur before

```python
import bisect
haystack = [1, 4, 5, 6, 8, 12, 15, 20, 21, 23, 23, 26, 29, 30]
needle = 22
position = bisect.bisect(haystack, needle) # equivalent to bisect.bisect_right(haystack, needle)
print(position) # 9
position = bisect.bisect_left(haystack, needle) 
print(position) # 9
```

An interesting application of bisect is to perform **table lookups** by numeric values. For example, to convert test scores to letter grades:

```python
def grade(score, breakpoints=[60, 70, 80, 90], grades='FDCBA'):
    i = bisect.bisect(breakpoints, score)
    return grades[i]

scores = [33, 99, 77, 70, 89, 90, 100]
scores_graded = [grade(score) for score in scores] 
print(scores_graded) # ['F', 'A', 'C', 'C', 'B', 'A', 'A']
```

**Inserting with bisect.insort().** Sorting is expensive, so once you have a sorted sequence, it's good to keep it that way. That is why bisect.insort was created. Calling `insort(seq, item)` inserts item into seq so as to keep seq in ascending order. Like bisect , insort takes optional `lo` and `hi` arguments to limit the search to a sub-sequence. There is also an `insort_left` variation that uses `bisect_left` to find insertion points.


Array
-----

Python programmers sometimes overuse the list type because it is so handy (I know I’ve done it). If you are handling lists of numbers, arrays are the way to go. The list type is flexible and easy to use, but depending on specific requirements, there are better options. For example, if you need to store 10 million floating-point values, an array is much more efficient, because an array does not actually hold full-fledged float objects, but only the packed bytes representing their machine values—just like an array in the C language.


```python
from array import array
from random import random
floats = array('d', (random() for i in range(10**7)))
floats[-1] # 0.07802343889111107
fp = open('floats.bin', 'wb')
floats.tofile(fp) # fast saving to file
fp.close()
floats2 = array('d')
fp = open('floats.bin', 'rb')
floats2.fromfile(fp, 10**7) # fast reading from file
fp.close()
floats2[-1] # 0.07802343889111107
floats2 == floats # True
```


Chapter 3: Dictionaries and Sets
=================================

The dict type is not only widely used in our programs but also a fundamental part of the Python implementation. Because of their crucial role, Python dicts are highly optimized. **Hash tables** are the engines behind Python’s high-performance dicts. All mapping types in the standard library use the basic dict in their implementation, so they share the limitation that the **keys must be hashable** (the values need not be hashable, only the keys).

**What is hashable?** Here is part of the definition of hashable from the Python Glossary: 

*"An object is hashable if it has a hash value which never changes during its lifetime (it needs a `__hash__()` method), and can be compared to other objects (it needs an `__eq__()` method). Hashable objects which compare equal must have the same hash value."*

The atomic immutable types ( str , bytes , numeric types) are all hashable. A tuple is hashable only if all its items are hashable.

```python
tt = (1, 2, (30, 40))
hash(tt) # hashabel: 8027212646858338501
tl = (1, 2, [30, 40])
hash(tl) # not hashable: error
```

Dictionary declaration
-------------

There are various ways a Dictionary cab be declared. Here, some examples:

```python
a = dict(one=1, two=2, three=3)
b = {'one': 1, 'two': 2, 'three': 3}
c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
d = dict([('two', 2), ('one', 1), ('three', 3)])
e = dict({'three': 3, 'one': 1, 'two': 2})
a == b == c == d == e # True
```



Dictionary Comprehensions
-------------------

Comprehension can be used with both dictionaries and sets. Here, an example:

```python
DIAL_CODES = [(86, 'China'),(91, 'India'),(1, 'United States'),
              (62, 'Indonesia'),(55, 'Brazil'),(92, 'Pakistan'),
              (880, 'Bangladesh'),(234, 'Nigeria'),(7, 'Russia'),
              (81, 'Japan'),]

country_code = {country: code for code, country in DIAL_CODES}

print(country_code)
#{'China': 86, 'India': 91, 'Bangladesh': 880, 'United States': 1,
#'Pakistan': 92, 'Japan': 81, 'Russia': 7, 'Brazil': 55, 'Nigeria':
#234, 'Indonesia': 62}
```

Search in a dictionary
-----------------------

A search like `k in my_dict.keys()` is **efficient** in Python 3 even for very large mappings because `dict.keys()` returns a view, which is similar to a set, and containment checks in sets are as fast as in dictionaries. Details are documented in the “Dictionary” view objects section of the documentation. In Python 2, `dict.keys()` returns a list, so our solution also works there, but it is not efficient for large dictionaries, because `k in my_list` must scan the list.


Sets
------

A set is a collection of unique objects. Set elements must be hashable. The set type is not hashable. Like dictionary they allow fast access to elements but they do not use a key-value mechanism. They can be considered perfect for membership checks, replacing those dictionaries where a dummy value was previously used. Sets can be declared this way:

```python
my_set={1,2,3,4}
print(my_set) # {1,2,3,4}
```

A basic use case is removing duplication:

```python
l = ['spam', 'spam', 'eggs', 'spam']
set(l) # {'eggs', 'spam'}
list(set(l)) # ['eggs', 'spam']
```

The set types implement the **essential set operations** as infix operators. Given two sets `a` and `b` , 

- `a | b` computes the union.
- `a & b` computes the intersection.
- `a - b` computes the difference (relative complement).
- `a ^ b` computes the symmetric difference (complement of the intersection `a & b`).
- `a < b` returns True if `a` is a subset of `b` (likewise `a > b` returns True if superset).

Smart use of set operations can reduce both the line count and the runtime of Python programs, at the same time making code easier to read and reason about—by removing loops and lots of conditional logic. For example, imagine you have a large set of email addresses (the haystack ) and a smaller set of addresses (the needles ) and you need to count how many needles occur in the haystack. Thanks to set intersection (the & operator) you can code that in a simple line:

```python
# needles and haystack are sets
found = len(needles & haystack) # first way
found = len(needles.intersection(haystack)) # second way
```

The notation for **set comprehension** can also be used with sets.


Comparing dict, set, and list
-----------------------------

A hash table is a sparse array (i.e., an array that always has empty cells). In standard data
structure texts, the cells in a hash table are often called “buckets.” In a dict hash table,
there is a bucket for each item, and it contains two fields: a reference to the key and a
reference to the value of the item.

**Dictionary.** To fetch the value at `my_dict[search_key]`, Python calls `hash(search_key)` to obtain the hash value of `search_key` and uses the least significant bits of that number as an offset to look up a bucket in the hash table (the number of bits used depends on the current size of the table). If the found bucket is empty, `KeyError` is raised. Otherwise, the found bucket has an item (a `found_key:found_value` pair) and then Python checks whether `search_key == found_key`. If they match, that was the item sought: `found_value` is returned. If `search_key` and `found_key` do not match, this is a hash collision. In order to resolve the collision, the algorithm then takes different bits in the hash, massages them in a particular way, and uses the result as an offset to look up a different bucket. Python may determine that the hash table is too crowded and rebuild it to a new location with more room. As the hash table grows, so does the number of hash bits used as bucket offsets, and this keeps the rate of collisions low. This implementation may seem like a lot of work, but even with millions of items in a dict, many searches happen with no collisions, and the average number of collisions per search is between one and two. Under normal usage, even the unluckiest keys can be found after a handful of collisions are resolved.

**Set.** Similarly to dictionaries, set types are also implemented with a hash table, except that each bucket holds only a reference to the element (as if it were a key in a dict, but without a value to go with it). In fact, before set was added to the language, we often used dictionaries with dummy values just to perform fast membership tests on the keys.

**List.** They contain references to elements. However, differently from dictionaries and sets they do not use a hash table for mapping. It follows that retrieving a particular element from a list by using `in` has cost dependent from the length of the list (it necessary to iterate all the elements).

<p align="center">
<img src="./images/dict_set_list_comparison.png" width="500">
</p>


