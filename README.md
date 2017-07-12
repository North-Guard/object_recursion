# Object Recursion

---

This system recurse through objects' references to perform various computations.  
So far the system is capable of:
- Recursively print the type of an object and contained objects.  
    Ex. `rtype([(1, 'a'), (2, 'b')])` will product a string with: `list[tuple[int,str]]`.  
    See [Type Recursive](#type-recursive).
- Print a recursive-tree of cotnained objects.  
    Used mostly for observing the functionality of the system.  
    See [Recursive Container Tree String](#recursive-container-tree-string).
- Compute the approximate memory consumption of an object and all objects referenced to by the object.  
    Objects references to multiple times does not count multiple times in the memory-consumption (just a reference).  
    See [Size Recursive](#size-recursive).
- Compute the memory overlap of multiple objects.  
    Given multiple objects, the method will produce a matrix.  
    The diagonal elements are the sizes of each object.  
    The non-diagonal elements are the memory overlap of the objects in the related row and the column.  
    See [Size Overlap](#size-overlap). 

Run `python -m object_recursion` for a test of all parts of the system.




## Type Recursive
Allows for recursively determining types of various Python objects.


### Usage
Calling `rtype()` on an object returns a string describing the type and contained types of that object.
```python
print(rtype([1, None, "str"]))
# Prints: list[int|None|str]

print(rtype((False, [" "])))
# Prints: tuple[bool,list[str]]
```

The method can also handle user-defined classes.


### Test Script

The `__main__.py` script, runs the method on a list of objects and creates the following print.
On the left there is the string-representation of each object as returned by `repr(obj)`, on the right is the 
recursive type returned by `rtype(obj)`.

```
Object                                            : rtype()
---------------------------------------------------------------------------
1                                                 : int
2.3                                               : float
None                                              : None
False                                             : bool
'hello'                                           : str
[1, 2, 3]                                         : list[int]
['a', 'b']                                        : list[str]
[1, 'h']                                          : list[int|str]
(False, 1, '2')                                   : tuple[bool,int,str]
{1.2, 2.3, 3.4}                                   : set[float]
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]                 : list[list[int]]
[(1, 'a'), (2, 'b')]                              : list[tuple[int,str]]
{1: 'b', 2: 'c'}                                  : dict[int: str]
{1: 'b', 2: None}                                 : dict[int: None|str]
[<__main__.Foo object at 0x7fdad17f8a90>]         : list[Foo]
[<function bar at 0x7fdae8cebe18>]                : list[bar()]
Bob(a=1, b=2, c=3)                                : Bob[int,int,int]
array([1, 2, 3])                                  : ndarray
array([['1', 'b'], ['3', '4']], dtype='<U21')     : ndarray
<__main__.Looper object at 0x7fdae8ccaeb8>        : Looper
[1, [4, [2, [...]]], <__main__.Looper object  ..  : list[Looper|list[int|list[..]]|int]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, ..  : list[int]
```


### Options

##### Symbols

The symbols below are used in the representation, but can all be changed with optional arguments to 
`rtype()`.  

`[]` Delimiters of types contained within another object (fx. lists, tuples or dictionaries).  
`|` Indicates that a mutable object can have various interchangeable types (fx. lists).  
`,` Indicates that an immutable object can have various ordered types (fx. tuples).   
`:` Symbol for the mapping from one type to another (fx. dictionaries).  
`()` Indicates that the passed argument is callable.  

If a user-defined class is both en iterable and callable etc. then it will only be shown as one of those things.

##### Numpy dimensions

Default setting is to show how many dimensions a numpy array has. For example a one-dimensional array will have type 
`np.1darray` while a two-dimentional matrix will have type `np.2darray`. This numbering can be turned off by 
passing `show_numpy_dimensions=False` to `rtype()`, in which the two types would both be `np.ndarray`.

##### Sampling

`rtype()` by default recursively goes through all objects within an object. 
With large data-structures this can get time-consuming. Therefore one can pass `sampling=X` to `rtype()`,
where `X` is an integer. With this argument, `rtype()` takes random `X` samples from any 
list/tuple/iterable etc. and creates a type-string based on those sample. 
This is of cause much faster, but one can not be sure that every nested type is reported (for example if
a single `None`-value is hidden between thousands of integers).


## Recursive Container Tree String

`rcontainer_tree_str(obj)` will print a recursive tree of a container-object.  
Example:
```
[1, [2, 3, ((4, 5), 7)]]

          Becomes

[1, [2, 3, ((4, 5), 7)]]
  1
  [2, 3, ((4, 5), 7)]
    2
    3
    ((4, 5), 7)
      (4, 5)
        4
        5
      7
```


## Size Recursive

`rsize(obj)` allows computing an approximation fo the memory-consumption of `obj`.  

The test-script will produce the following output, comparing the method to `sys.getsizeof()` and `pympler.asizeof()` 
(if pymbler is not installed, the column will not be filled, but the program does not crash). 
The left-most column contains `repr(obj)` of each object. `rsize()` tends to hold a middle ground of the three methods. 

```
Object                                            : pympler.asizeof()  : rsize()     : sys.getsizeof()    
---------------------------------------------------------------------------------------------------------
1                                                 : 32                 : 28          : 28                 
2.3                                               : 24                 : 24          : 24                 
None                                              : 16                 : 16          : 16                 
False                                             : 24                 : 24          : 24                 
'hello'                                           : 56                 : 54          : 54                 
[1, 2, 3]                                         : 184                : 172         : 88                 
['a', 'b']                                        : 208                : 196         : 80                 
[1, 'h']                                          : 176                : 166         : 80                 
(False, 1, '2')                                   : 192                : 182         : 72                 
{1.2, 2.3, 3.4}                                   : 296                : 296         : 224                
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]                 : 640                : 604         : 88                 
[(1, 'a'), (2, 'b')]                              : 400                : 380         : 80                 
{1: 'b', 2: 'c'}                                  : 432                : 240         : 240                
{1: 'b', 2: None}                                 : 384                : 240         : 240                
[<__main__.Foo object at 0x7fdad17f8a90>]         : 240                : 128         : 72                 
[<function bar at 0x7fdae8cebe18>]                : 72                 : 208         : 72                 
Bob(a=1, b=2, c=3)                                : 224                : 156         : 72                 
array([1, 2, 3])                                  : 120                : 120         : 120                
array([['1', 'b'], ['3', '4']], dtype='<U21')     : 448                : 448         : 448                
<__main__.Looper object at 0x7fdae8ccaeb8>        : 568                : 168         : 56                 
[1, [4, [2, [...]]], <__main__.Looper object  ..  : 1712               : 3960        : 888                
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, ..  : 896                : 3664        : 864   
```


## Size Overlap

`rsize_overlap(*args)` builds on top of `rsize(obj)` and allows for computing the sizes of objects, while detecting
memory-overlap between the objects. The output is a matrix, where the diagonal elements are the `rsize(obj)`-sizes of
each object, while the non-diaginal elements are the memory overlaps of the objects in the row and column.

Below is an example of the method.

```
Object and shared memory consumption:
               obj1    obj2    obj3  long_list  looper1  cont_looper1
obj1          464.0   212.0     0.0       28.0      0.0          56.0
obj2          212.0  1680.0  1316.0        0.0      0.0          28.0
obj3            0.0  1316.0  1536.0        0.0      0.0           0.0
long_list      28.0     0.0     0.0     3664.0      0.0          28.0
looper1         0.0     0.0     0.0        0.0    168.0         168.0
cont_looper1   56.0    28.0     0.0       28.0    168.0        4016.0

Object 'a' is shared by obj1 and obj2, and consumes 212 Bytes
Object 'b' is shared by obj2 and obj3, and consumes 1316 Bytes
Object 'cont_looper1' contains 'looper1'
Object 'cont_looper1' shares integers with other objects
```

