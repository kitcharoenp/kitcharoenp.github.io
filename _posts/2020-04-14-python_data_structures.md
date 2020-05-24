---
layout: post
title: "Python : Data Structures"
published: false
categories: [python]
---
### [Tuples][1]
* Ordered sequence.
* Compound data types : strings, integer, float  They can all be contained in a tuple `tuple1 = ("disco",10,1.2 )`
* **Immutable**, which means we can't change them.
* Manipulate a tuple, we must create a new tuple instead.

#### Indexing
Each element of a tuple can be accessed via an index.

#### Concatenate Tuples
We can concatenate or combine tuples by using the `+` sign:
```Python
# Concatenate two tuples

tuple2 = tuple1 + ("hard rock", 10)
tuple2
# ('disco', 10, 1.2, 'hard rock', 10)
```

#### Sorting
```Python
Ratings = (0, 9, 6, 5, 10, 8, 9, 6, 2)

# Generate a sorted List from the Tuple
RatingsSorted = sorted(Ratings)
RatingsSorted
# [0, 2, 5, 6, 6, 8, 9, 9, 10] # list
```

### Nested Tuple
A tuple can contain another `tuple` as well as other more complex data types.
```python
# Create a nest tuple

NestedT =(1, 2, ("pop", "rock") ,(3,4),("disco",(1,2)))
```

### [Lists][2]
A list is a sequenced collection of different objects such as integers, strings, and other lists as well. The address of each element within a list is called an index. An index is used to access and refer to items within a list. To create a list, type the list within square brackets [ ], with your content inside the parenthesis and separated by commas.

#### List Content
Lists can contain strings, floats, and integers. We can nest other lists, and we can also nest tuples and other data structures. The same indexing conventions apply for nesting:
```Python
# Sample List

["Michael Jackson", 10.1, 1982, [1, 2], ("A", 1)]
```

#### Method `extend`
```Python
# Use extend to add elements to list

L = [ "Michael Jackson", 10.2]

# add two new elements to the list
L.extend(['pop', 10]) # ['Michael Jackson', 10.2, 'pop', 10]
```

#### Method `apply`
```Python
L = [ "Michael Jackson", 10.2]

# If we append the list ['a','b'] we have one new element consisting of a nested list:

L.append(['a','b'])
# ['Michael Jackson', 10.2, 'pop', 10, ['a', 'b']]
```

delete an element of a list using the "del" command; we simply indicate the list

set one variable, B equal to A, both A and B are referencing the same list.

Multiple names referring to the same object is known as aliasing.


You can clone list “A” by using the following syntax.

[1]: https://labs.cognitiveclass.ai/tools/jupyterlab/lab/tree/labs/PY0101EN/PY0101EN-2-1-Tuples.ipynb "Tuples"
[2]: https://labs.cognitiveclass.ai/tools/jupyterlab/lab/tree/labs/PY0101EN/PY0101EN-2-2-Lists.ipynb "Lists"
