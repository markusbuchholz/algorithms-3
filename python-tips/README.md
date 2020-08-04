This file contains my personal notes of the excellent book *"Fluent Python", Ramaho (2015)*.


- [Chapter 2](#chapter-2-data-structures): **Data Structures.** Overview of data structures, list comprehension, generators, unpacking, sorting, binary search, arrays.
- [Chapter 3](#chapter-3-dictionaries-and-sets): **Dictionaries and Sets.** Comparison between Dictionaries, Sets, and Lists.
- [Chapter 4](#chapter-4-strings-versus-bytes): **Strings VS Bytes.** How to encode/decode string/bytes.
- [Chapter 5](#chapter-5-functions-as-first-class-objects): **Functions as first-class objects.** Functional programming, Lambda functions, callable operator, introspection, parameters handling, annotations.
- [Chapter 7](#chapter-7-function-decorators-and-closures): **Function Decorators and Closures**. Definition, stacked and parameterized decorators, local variables, closures.

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

The [list comprehension](https://en.wikipedia.org/wiki/List_comprehension) is a syntactic construct available in  Python for creating a list based on existing lists. It follows the form of the mathematical set-builder notation (set comprehension):

<p align="center">
<img src="./images/set_comprehension.png" width="400">
</p>

This translates in the following Python syntax:

```
{expression(variable) for variable in input_set [predicate][, …]}
```

- expression (optional). An output expression producing members of the new set from members of the input set that satisfy the predicate expression.
- variable (required). Variable representing members of an input set.
- input_set (required). Represents the input set.
- predicate (optional). Expression acting as a filter on members of the input set.
- [, …]] (optional). Another nested comprehension. 

Here, a few examples of list comprehension:

```python
all = [x for x in range(10)] # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
even = [x for x in range(10) if x%2==0] # [0, 2, 4, 6, 8]
square = [x**2 for x in range(10)] # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
even_square = [x**2 for x in range(10) if x%2==0] # [0, 4, 16, 36, 64]
```

List comprehension can be used in more complex ways by nesting other comprehensions. For instance, if we have two lists of different sizes (first list of size N and second of size M) we can combine them to get all the pairs (size NxM):

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


Chapter 4: Strings versus Bytes
=============================

The concept of *string* is simple enough: a string is a sequence of characters. The problem lies in the definition of *character*. In 2015, the best definition of *character* we have is a Unicode character. Accordingly, the items you get out of a Python 3 `str` are Unicode characters, just like the items of a unicode object in Python 2, and not the raw bytes you get from a Python 2 `str`.

**Unicode standard.** It explicitly separates the identity of characters from specific byte representations. The identity of a character is a number from 0 to 1,114,111 (base 10), shown in the Unicode standard as 4 to 6 hexadecimal digits with a `U+` prefix. For example, the code point for the letter `A` is `U+0041`.

**Encoding.** An encoding is an algorithm that converts code points to byte sequences and vice versa. The actual bytes that represent a character depend on the encoding in use. The code point for A (`U+0041`) is encoded as the single byte `\x41` in the UTF-8 encoding, or as the bytes `\x41\x00` in UTF-16LE encoding. As another example, the Euro sign (`U+20AC`) becomes three bytes in UTF-8 (`\xe2\x82\xac`), but in UTF-16LE it is encoded as two bytes (`\xac\x20`). Converting from code points to bytes is encoding; converting from bytes to code points is decoding. Since byte sequences can be cryptic for humans, while Unicode `str` objects are "human" readable, it makes sense that we `decode` bytes to strings to get human-readable text, and we `encode` strings to bytes for storage or transmission.

```python
s = 'café'
len(s) # 4
b = s.encode('utf8') # b'caf\xc3\xa9'
# bytes literals start with a b prefix
# bytes b has five bytes
# “é” encoded as two bytes in UTF-8
len(b) # 5
b.decode('utf8') # 'café'
```

You see `b'caf\xc3\xa9'` with the first three bytes `b'caf'` in the printable ASCII range, the last two not. Three different displays are used, depending on each byte value:

1. For bytes in the printable ASCII range, from `space` to `~`, the ASCII character itself is used.
2. For bytes corresponding to `tab`, `newline`, `carriage return`, and `\` , the escape sequences `\t` , `\n` , `\r` , and `\\` are used.
3. For every other byte value, a hexadecimal escape sequence is used (e.g., `\x00` is the null byte).


Bytes
------

We can build `bytes` from a `str`, given an encoding. Each item of `bytes` is an integer in range(256). Slices of bytes are also bytes, even slices of a single byte. There is no literal syntax for `bytearray`: they are shown as `bytearray()` with a bytes literal as argument. A slice of `bytearray` is also a `bytearray`.

```python
cafe = bytes('café', encoding='utf_8') # b'caf\xc3\xa9'
cafe[0] # 99
cafe[:1] # b'c'
cafe_arr = bytearray(cafe) # bytearray(b'caf\xc3\xa9')
cafe_arr[-1:] # bytearray(b'\xa9')
```

The fact that `my_bytes[0]` retrieves an int but `my_bytes[:1]` returns a bytes object of length 1 should not be surprising. The only sequence type where `s[0] == s[:1]` is the `str` type. Although practical, this behavior of `str` is exceptional. For every other sequence, `s[i]` returns one item, and `s[i:i+1]` returns a sequence of the same type with the `s[1]` item inside it.


You can use familiar string methods like `endswith`, `replace`, `strip`, `translate`, `upper` , and dozens of others with binary sequences. The regular expression functions in the `re` module also work on binary sequences (if the regex is compiled from a binary sequence instead of a `str`).

Basic Encoders/Decoders
-----------------------

The Python distribution bundles more than 100 codecs (encoder/decoder) for text to byte conversion and vice versa. Each codec has a name, like 'utf_8' , and often aliases, such as 'utf8' , 'utf-8' , and 'U8' , which you can use as the encoding argument. The following are a representative set of common encodings:

- latin1 a.k.a. iso8859_1. Important because it is the basis for other encodings, such as cp1252 and Unicode itself (note how the latin1 byte values appear in the cp1252 bytes and even in the code points).
- cp1252. A latin1 superset by Microsoft, adding useful symbols like curly quotes and the € (euro); some Windows apps call it “ANSI,” but it was never a real ANSI standard.
- cp437. The original character set of the IBM PC, with box drawing characters. Incompatible with latin1 , which appeared later.
- gb2312. Legacy standard to encode the simplified Chinese ideographs used in mainland China; one of several widely deployed multibyte encodings for Asian languages.
- utf-8. The most common 8-bit encoding on the Web, by far; 3 backward-compatible with ASCII (pure ASCII text is valid UTF-8).
- utf-16le. One form of the UTF-16 16-bit encoding scheme; all UTF-16 encodings support code points beyond `U+FFFF` through escape sequences called "surrogate pairs".


```python
for codec in ['latin_1', 'utf_8', 'utf_16']:
    print(codec, 'El Niño'.encode(codec), sep='\t')
# b'El Ni\xf1o'
# b'El Ni\xc3\xb1o'
# b'\xff\xfeE\x00l\x00 \x00N\x00i\x00\xf1\x00o\x00'
```

WARNING: code that has to run on multiple machines or on multiple occasions should never depend on encoding defaults. Always pass an explicit `encoding` argument when opening text files `fp = open('cafe.txt', 'w', encoding='utf_8')`, because the default may change from one machine to the next, or from one day to the next. On GNU/Linux and OSX all of these encodings are set to UTF-8 by default, and have been for several years, so I/O handles all Unicode characters. On Windows, not only are different encodings used in the same system, but they are usually codepages like 'cp850' or 'cp1252' that support only ASCII with 127 additional characters that are not the same from one encoding to the other. Therefore, Windows users are far more likely to face encoding errors unless they are extra careful.


Coping with Unicode Encode and Decode Errors
-----------------------------


**Encode errors**

Most non-UTF codecs handle only a small subset of the Unicode characters. When converting text to bytes, if a character is not defined in the target encoding, UnicodeEncodeError will be raised, unless special handling is provided. Not every byte holds a valid ASCII character, and not every byte sequence is valid UTF-8 or UTF-16; therefore, when you assume one of these encodings while converting a binary sequence to text, you will get a `UnicodeDecodeError` if unexpected bytes are found. 


The parameter `errors` can be used for handling **encoding errors**:

- **ignore.** The `errors='ignore'` handler silently skips characters that cannot be encoded (usually a bad idea).
- **replace.** The `errors='replace'` substitutes unencodable characters with `'?'` (data is lost but users will know).
- **xmlcharrefreplace.** The `errors='xmlcharrefreplace'` replaces unencodable characters with an XML entity.


```python
city = 'São Paulo'
city.encode('utf_8') # b'S\xc3\xa3o Paulo'
city.encode('cp437', errors='ignore') # b'So Paulo'
city.encode('cp437', errors='replace') # b'S?o Paulo'
city.encode('cp437', errors='xmlcharrefreplace') # b'S&#227;o Paulo'
city.encode('cp437') # UnicodeEncodeError: 'charmap' codec can't encode character '\xe3' [...]
```

**Decode errors**

Not every byte holds a valid ASCII character, and not every byte sequence is valid UTF-8 or UTF-16; therefore, when you assume one of these encodings while converting a binary sequence to text, you will get a `UnicodeDecodeError` if unexpected bytes are found. Be careful, many 8-bit encodings like 'cp1252' , 'iso8859_1' , and 'koi8_r' are able to decode any stream of bytes without errors. If your program assumes the wrong 8-bit encoding, it will silently decode garbage.

```
octets = b'Montr\xe9al'
octets.decode('cp1252') # 'Montréal'
octets.decode('iso8859_7') # 'Montrιal'
octets.decode('koi8_r') # 'MontrИal'
octets.decode('utf_8') # UnicodeDecodeError: 'utf-8' codec can't decode byte 0xe9 [...]
octets.decode('utf_8', errors='replace') # 'Montr�al'
```

Using `replace` error handling, the `\xe9` is replaced by `�` (code point `U+FFFD`), the official Unicode REPLACEMENT CHARACTER intended to represent unknown characters.

Errors when loading a python file
---------------------------------

UTF-8 is the default source encoding for Python 3, just as ASCII was the default for Python 2 (starting with 2.5). If you load a .py module containing non-UTF-8 data and no encoding declaration, you get a message like this:

```
SyntaxError: Non-UTF-8 code starting with '\xe1' in file ola.py on line
1, but no encoding declared; see http://python.org/dev/peps/pep-0263/
for details
```

Because UTF-8 is widely deployed in GNU/Linux and OSX systems, a likely scenario is opening a `.py` file created on Windows with cp1252 . To fix this problem, add a magic coding comment at the top of the file:

```python
# coding: cp1252
print('Olá, Mundo!')
```

Now that Python 3 source code is no longer limited to ASCII and defaults to the excellent UTF-8 encoding, the best “fix” for source code in legacy encodings like 'cp1252' is to convert them to UTF-8 and not bother with the coding comments. Python 3 allows non-ASCII identifiers in source code:

```python
ação = 'PBR' # ação = stock
ε = 10**-6 # ε = epsilon
```

However, some people dislike the idea. The most common argument to stick with ASCII identifiers is to make it easy for everyone to read and edit code.



Chapter 5: Functions as first-class objects
========================================

In computer science, [functional programming](https://en.wikipedia.org/wiki/Functional_programming) is a programming paradigm where programs are constructed by applying and composing functions. It is a declarative programming paradigm in which function definitions are trees of expressions that each return a value, rather than a sequence of imperative statements which change the state of the program. In functional programming, functions are treated as first-class citizens, meaning that they can be bound to names (including local identifiers), passed as arguments, and returned from other functions, just as any other data type. This allows programs to be written in a declarative and composable style, where small functions are combined in a modular manner. 

Functions in Python
--------------------

Functions in Python are first-class objects. Programming language theorists define a “first-class object” as a program entity that can be:

- Created at runtime
- Assigned to a variable or element in a data structure
- Passed as an argument to a function
- Returned as the result of a function

```python
def factorial(n):
    ''' Documentation of the factorial function.

    It returns n!.
    '''
    return 1 if n<2 else n*factorial(n-1) # -> iterative method
```

Note that it is possible to access the documentaiton of the method by typing `factorial.__doc__` which prints the information about the method. We can also pass factorial as an argument to map . The **map function** returns an iterable where each item is the result of the application of the first argument (a function) to succesive elements of the second argument (an iterable), range(10) in this example.

```python
list(map(factorial, range(11)))
# [1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880, 3628800]
```

Higher-Order Functions
----------------------

A function that takes a function as argument or returns a function as the result is a higher-order function. One example is `map`, another is the builtin function `sorted`. For instance, we can combine `map` with the previously created method `factorial` and give the result as input to `sorted`:

```python
sorted(map(factorial, range(5)))
```

We can create a custom function to reverse the number and chain it with `map` and `sorted`. We can also pass the function as the `key` argument to the `sorted` method (any one-argument function can be used as the key). In this case the method will not change the elements of the input list but will use the method as sorting criterium:

```python
def reverse(number):
    string = str(number)
    string = string[::-1]
    return int(string)

sorted(map(reverse, range(1000,1010)))
# [1, 1001, 2001, 3001, 4001, 5001, 6001, 7001, 8001, 9001]
sorted([1234, 5678, 4321, 8765])
# [1234, 4321, 5678, 8765]
sorted([1234, 5678, 4321, 8765], key=reverse)
# [4321, 1234, 8765, 5678] elements are not changed only ordered based on 'reverse'
```

Replacements for map, filter, and reduce
----------------------------------------

Starting with Python 3.0, `reduce` is not a built-in. The `map` and `filter` functions are still built-in in Python 3, but since the introduction of list comprehensions and generator expressions, they are not as important since we can replace a verbose map or filter via a simpler list comprehension. The following two lines of code do the same thing:


```python
list(map(factorial, range(6)))
[factorial(n) for n in range(6)]
```

Similarly, for the filter function:

```python
list(map(factorial, filter(lambda n: n % 2, range(6))))
[factorial(n) for n in range(6) if n % 2]
```


Anonymous Functions
-------------------

The **lambda** keyword creates an anonymous function within a Python expression. However, the simple syntax of Python limits the body of lambda functions to be pure expressions. In other words, the body of a lambda cannot make assignments or use any other Python statement such as while, try, etc. The best use of anonymous functions is in the context of an argument list. For instance, we can sort an array of strings in reverse order writing:


```python
fruits = ['strawberry', 'fig', 'apple', 'cherry', 'raspberry', 'banana']
sorted(fruits)
# ['apple', 'banana', 'cherry', 'fig', 'raspberry', 'strawberry']
sorted(fruits, key=lambda word: word[::-1])
# ['banana', 'apple', 'fig', 'raspberry', 'strawberry', 'cherry']
```

WARNING: Outside the limited context of arguments to higher-order functions, anonymous functions are rarely useful in Python. The syntactic restrictions tend to make nontrivial lambdas either unreadable or unworkable.

Callable operator `()`
----------------------

The callable operator `()` is used by a variety of entities in Python. Given the variety of existing callable types in Python, the safest way to determine whether an object is callable is to use the `callable()` built-in. Here is given an overview of the various callable types:

- User-defined functions. Created with def statements or lambda expressions.
- Built-in functions. A function implemented in C (for CPython), like len or time.strftime .
- Built-in methods. Methods implemented in C, like `dict.get` .
- Methods. Functions defined in the body of a class.
- Classes. When invoked, a class runs its `__new__` method to create an instance, then `__init__` to initialize it, and finally the instance is returned to the caller. Because there is no new operator in Python, calling a class is like calling a function. (Usually calling a class creates an instance of the same class, but other behaviors are possible by overriding `__new__`.
- Class instances. If a class defines a `__call__` method, then its instances may be invoked as functions.
- Generator functions. Functions or methods that use the `yield` keyword. When called, generator functions return a generator object.


User-Defined Callable Types
----------------------------

Not only are Python functions real objects, but arbitrary Python objects may also be made to behave like functions. Implementing a `__call__` instance method is all it takes.

```python
import random
class BingoCage:
    def __init__(self, items):
        self._items = list(items)
        random.shuffle(self._items)
    def pick(self):
        try:
            return self._items.pop()
        except IndexError:
            raise LookupError('pick from empty BingoCage')
    def __call__(self):
        return self.pick()

bingo = BingoCage(range(3))
bingo.pick() # 1
bingo() # 0
callable(bingo) # True
```

Using the method `__call__` the class `BingoCage` beomes callable, meaning that one can use the shorthand `bingo()` in place of `bingo.pick()`

Function introspection
----------------------

In computer programming, introspection is the ability to determine the type of an object at runtime. It is one of Python’s strengths. Everything in Python is an object and we can examine those objects. Python ships with a few built-in functions and modules to help us.

A full list of attributes for the function object can be retrieved using `dir()` as follows:

```python
def dummy(): 
    pass

dir(dummy)
# ['__annotations__', '__call__', '__class__', '__closure__', '__code__',
# '__defaults__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__',
# '__format__', '__ge__', '__get__', '__getattribute__', '__globals__',
# '__gt__', '__hash__', '__init__', '__kwdefaults__', '__le__', '__lt__',
# '__module__', '__name__', '__ne__', '__new__', '__qualname__', '__reduce__',
# '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__',
# '__subclasshook__']
```

The `__dict__` attribute can be used to store user-attributes assigned to it. This is useful as a primitive form of annotation. Assigning arbitrary attributes to functions is not a very common practice in general.

Only a subset of these attributes is **only** associated to functions:

```python
# ['__annotations__', '__call__', '__closure__', '__code__', '__defaults__',
# '__get__', '__globals__', '__kwdefaults__', '__name__', '__qualname__']
```


<p align="center">
<img src="./images/functions_attributes.png" width="400">
</p>


Parameter handling
------------------

One of the best features of Python functions is the extremely flexible parameter handling mechanism, enhanced with keyword-only arguments in Python 3. Closely related are the use of `*` and `**` to explode iterables and mappings into separate arguments when we call a function.

```python
my_method(first_param, *to_tuple_params, third_param=None, **attrs):
    pass
```

Any number of arguments after the first are captured by `*to_tuple_params` as a tuple. The `third_param` parameter can only be passed as a keyword argument. Keyword arguments not explicitly named in the tag signature are captured by `**attrs` as a dict .

Keyword-only arguments are a new feature in Python 3. In the example, the `third_param` parameter can only be given as a keyword argument (it will never capture unnamed positional arguments). To specify keyword-only arguments when defining a function, name them after the argument prefixed with `*`. If you don’t want to support variable positional arguments but still want keyword-only arguments, put a `*` by itself in the signature, like this:

```python
def f(a, *, b):
    return a, b

f(1, b=2) # (1, 2)
```

Keyword-only arguments do not need to have a default value: they can be mandatory, like `b` in the previous example.


Function Annotations
---------------------

Python 3 provides syntax to attach metadata to the parameters of a function declaration and its return value. Each argument in the function declaration may have an annotation expression preceded by `:`. If there is a default value, the annotation goes between the argument name and the `=` sign. To annotate the return value, add `->` and another expression between the `)` and the `:` at the tail of the function declaration. The expressions may be of any type. The most common types used in annotations are classes, like `str` or `int` , or strings. Consider this example:

```python
def clip(text:str, max_len:'int > 0'=80) -> str:
    """Return text clipped at the last space before or after max_len
    """
    pass
```

Note how the annotation of the first parameter is `str` showing to the user that it should be a string, the annotation of the second parameter is `'int > 0'` (followed by a default value `80`). The returns value is marked with `->`, in the example it shows to the user that the returned value should be a `str` objects.

IMPORTANT: No processing is done with the annotations. They are merely stored in the `__annotations__` attribute of the function, a dict. No checks, enforcement, validation, or any other action is performed. In other words, annotations have no meaning to the Python interpreter. They are just metadata.


Chapter 7: Function Decorators and Closures
========================================

Function decorators let us *“mark”* functions in the source code to enhance their behavior in some way. This is powerful stuff, but mastering it requires understanding closures.

Decorators
----------

A decorator is a *callable* that takes another function as argument (the decorated function). The decorator may perform some processing with the decorated function, and returns it or replaces it with another function or callable object.
Assuming an existing decorator named decorate , this code:

```python
@decorate
def target():
    print('running target()')
```

Has the same effect as writing this:

```python
def target():
    print('running target()')
    target = decorate(target)
```

The end result is the same: at the end of either of these snippets, the `target` name does not necessarily refer to the original target function, but to whatever function is returned by `decorate(target)`. Strictly speaking, decorators are just syntactic sugar. You can always simply call a decorator like any regular callable, passing another function. Sometimes that is actually convenient, especially when doing metaprogramming (changing program behavior at runtime). Here another example:


```python
import time

def clock(func):
    def clocked(*args): #
        start = time.time()
        result = func(*args) #
        elapsed = time.time() - start
        print('[%0.8fs] -> %r' % (elapsed, result))
        return result
    return clocked

@clock
def factorial(n):
    return 1 if n < 2 else n*factorial(n-1) # -> iterative
    
# Calling factorial(8) actually does this:
#factorial = clock(factorial)
```

To summarize: the first crucial fact about decorators is that they have the power to replace the decorated function with a different one. The second crucial fact is that they are executed immediately when a module is loaded.

Consider this example:

```python
def deco(func):
    """deco() returns its inner function object
    """
    def inner():
        print('running inner()')
    return inner

@deco
def target():
    """target() is decorated by deco
    """
    print('running target()')

# Invoking the decorated target actually runs inner()
target() # 'running inner()'
# target is a now a reference to inner
target # <function deco.<locals>.inner at 0x10063b598>
```

Here is a more concrete example. Let's suppose we have a certain number of promotions in our e-commerce. Each promotion is a separate function. We want a special function called `best_promotion()` to check all the available promotion functions and return the best one for a given user. One way to do this would be to store all the promotions in a list `promos` and then pass this list to `best_promotion()`. However, this strategy would be problematic if at some point a new promotion is added and we forget to add it to the list. A different way to solve the issue is by using decorators to automatically fill the list:

```python
promos = []

def promotion(promo_func):
    promos.append(promo_func)
    return promo_func

@promotion
def fidelity(order):
    """5% discount
    """
    return 5

@promotion
def bulk_item(order):
    """10% discount
    """
    return 10

@promotion
def large_order(order):
    """7% discount
    """
    return 7

def best_promo(order):
    """Select best discount available
    """
    return max(promo(order) for promo in promos)
```

When the module is loaded, and each single promotion function is allocated, the decorator `@promotion` will invoke the associated function `promotion()` passing the single promotion as input. Then the `promotion()` function will populate the list `promos`. The advantages of this approach are many. The `@promotion` decorator highlights the purpose of the decorated function, and also makes it easy to temporarily disable a promotion: just comment out the decorator. Promotional discount strategies may be defined in other modules, anywhere in the system, as long as the `@promotion` decorator is applied to them.


**Stacked decorators.** When two decorators `@d1` and `@d2` are applied to a function `f` in that order, the result is the same as `f = d1(d2(f))`.

**Parameterized Decorators.** This is a bit hacky (see the book for the code). When parsing a decorator in source code, Python takes the decorated function and passes it as the first argument to the decorator function. So how do you make a decorator accept other arguments? The answer is: make a decorator factory that takes those arguments and returns a decorator, which is then applied to the function to be decorated.

Closures
---------

**Local variables**

To understand closures first we need to give a look at how local variables are allocated in Python. Consider this example:

```python
b = 6

def f2(a):
    print(a)
    print(b)
    b = 9
    
f2(3)
# print(a) -> 3
# print(b) -> UnboundLocalError: local variable 'b' referenced before assignment
```

This snippet generates an error. It may seems strange at first, because functions can access variables that have been declared outside their body, like `b=6` in this example. However, when Python compile this code it will see that `b` is present inside the body of `f2()` and by design this means that the variable is local. Note that this is a design choice. The advantage is to avoid overwriting a global variable by mistake inside a function. In other words, when we create a new function we do not have to worry if the variable names we are using internally already exist somewhere. Global variable can be read inside a function but not overwritten.


**Closures**

A closure is a function with an extended scope that encompasses nonglobal variables referenced in the body of the function but not defined there. It does not matter whether the function is anonymous or not; what matters is that it can access nonglobal variables that are defined outside of its body. To summarize: a closure is a function that retains the bindings of the free variables that exist when the function is defined, so that they can be used later when the function is invoked and the defining scope is no longer available. Note that the only situation in which a function may need to deal with external variables that are nonglobal is when it is nested in another function.

Consider an `avg` function to compute the mean of an ever-increasing series of values. For example, the average closing price of a commodity over its entire history. Every day a new price is added, and the average is computed taking into account all prices so far. Starting with a clean slate, this is how avg could be used:

```python
avg(10)
# 10.0
avg(11)
# 10.5
avg(12)
# 11.0
```

Where does `avg` come from, and where does it keep the history of previous values? 

The **trivial solution** would be to use a `__call__` method inside a class to make the object callable. As explained in [Chapter 5](#chapter-5-functions-as-first-class-objects) this makes a new instance of the `Average()` class callable. Let's see:

```python
class Averager():
    def __init__(self):
        self.series = []
    def __call__(self, new_value):
        self.series.append(new_value)
        total = sum(self.series)
        return total/len(self.series)
        
avg = Averager()
avg(10)
# 10.0
 avg(11)
# 10.5
avg(12)
# 11.0
```

The **closure solution** to the same problem is slightly different. 


```python
def make_averager():
    series = []
    
    def averager(new_value):
        series.append(new_value)
        total = sum(series)
        return total/len(series)
    return averager

avg = make_averager()
avg(10)
# 10.0
 avg(11)
# 10.5
avg(12)
# 11.0
```

It’s obvious where the avg of the `Averager()` class keeps the history: the `self.series` instance attribute. Where does the `avg` function in the second example find the series? Note that `series` is a local variable of `make_averager` because the initialization `series = []` happens in the body of that function. But when `avg(10)` is called, `make_averager` has already returned, and its local scope is long gone. Within `averager`, `series` is a **free variable**. This is a technical term meaning a variable that is not bound in the local scope.

In Python the closure free variables are stored in `__closure__` with each item in `avg.__closure__` corresponding to a name in `avg.__code__.co_freevars`. These items are cells, and they have an attribute called `cell_contents` where the actual value can be found.

```python
avg.__code__.co_freevars 
# ('series',)
avg.__closure__
# (<cell at 0x107a44f78: list object at 0x107a91a48>,)
avg.__closure__[0].cell_contents
# [10, 11, 12]
```




