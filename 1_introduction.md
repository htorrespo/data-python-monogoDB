<!--
https://link-springer-com.ezproxy.unal.edu.co/chapter/10.1007/978-1-4842-3597-3_1
-->
# Introduction

Python Fundamentals
Python has several features that make it well suited for learning and doing data science. It’s free, relatively simple to code, easy to understand, and has many useful libraries to facilitate data science problem solving. It also allows quick prototyping of virtually any data science scenario and demonstration of data science concepts in a clear, easy to understand manner.

The goal of this chapter is not to teach Python as a whole, but present, explain, and clarify fundamental features of the language (such as logic, data structures, and libraries) that help prototype, apply, and/or solve data science problems.

Python fundamentals are covered with a wide spectrum of activities with associated coding examples as follows:

1. functions and strings
2. lists, tuples, and dictionaries
3. reading and writing data
4. list comprehension
5. generators
6. data randomization
7. MongoDB and JSON
8. visualization

## Functions and Strings

Python functions are first-class functions, which means they can be used as parameters, a return value, assigned to variable, and stored in data structures. Simply, functions work like a typical variable. Functions can be either custom or built-in. Custom are created by the programmer, while built-in are part of the language. Strings are very popular types enclosed in either single or double quotes.

The following code example defines custom functions and uses built-in ones:

```python
def num_to_str(n):
    return str(n)

def str_to_int(s):
    return int(s)

def str_to_float(f):
    return float(f)

if __name__ == "__main__":
    # hash symbol allows single-line comments
    '''
    triple quotes allow multi-line comments
    '''
    float_num = 999.01
    int_num = 87
    float_str = '23.09'
    int_str = '19'
    string = 'how now brown cow'
    s_float = num_to_str(float_num)
    s_int = num_to_str(int_num)
    i_str = str_to_int(int_str)
    f_str = str_to_float(float_str)
    print (s_float, 'is', type(s_float))
    print (s_int, 'is', type(s_int))
    print (f_str, 'is', type(f_str))
    print (i_str, 'is', type(i_str))
    print ('\nstring', '"' + string + '" has', len(string), 'characters')
    str_ls = string.split()
    print ('split string:', str_ls)
    print ('joined list:', ' '.join(str_ls))
```

A popular coding style is to present library importation and functions first, followed by the main block of code. The code example begins with three custom functions that convert numbers to strings, strings to numbers, and strings to float respectively. Each custom function returns a built-in function to let Python do the conversion. The main block begins with comments. Single-line comments are denoted with the # (hash) symbol. Multiline comments are denoted with three consecutive single quotes. The next five lines assign values to variables. The following four lines convert each variable type to another type. For instance, function num_to_str() converts variable float_num to string type. The next five lines print variables with their associated Python data type. Built-in function type() returns type of given object. The remaining four lines print and manipulate a string variable.

## Lists, Tuples, and Dictionaries
Lists are ordered collections with comma-separated values between square brackets. Indices start at 0 (zero). List items need not be of the same type and can be sliced, concatenated, and manipulated in many ways.

The following code example creates a list, manipulates and slices it, creates a new list and adds elements to it from another list, and creates a matrix from two lists:

```python
import numpy as np

if __name__ == "__main__":
    ls = ['orange', 'banana', 10, 'leaf', 77.009, 'tree', 'cat']
    print ('list length:', len(ls), 'items')
    print ('cat count:', ls.count('cat'), ',', 'cat index:', ls.index('cat'))
    print ('\nmanipulate list:')
    cat = ls.pop(6)
    print ('cat:', cat, ', list:', ls)
    ls.insert(0, 'cat')
    ls.append(99)
    print (ls)
    ls[7] = '11'
    print (ls)
    ls.pop(1)
    print (ls)
    ls.pop()
    print (ls)
    print ('\nslice list:')
    print ('1st 3 elements:', ls[:3])
    print ('last 3 elements:', ls[3:])
    print ('start at 2nd to index 5:', ls[1:5])
    print ('start 3 from end to end of list:', ls[-3:])
    print ('start from 2nd to next to end of list:', ls[1:-1])
    print ('\ncreate new list from another list:')
    print ('list:', ls)
    fruit = ['orange']
    more_fruit = ['apple', 'kiwi', 'pear']
    fruit.append(more_fruit)
    print ('appended:', fruit)
    fruit.pop(1)
    fruit.extend(more_fruit)
    print ('extended:', fruit)
    a, b = fruit[2], fruit[1]
    print ('slices:', a, b)
    print ('\ncreate matrix from two lists:')
    matrix = np.array([ls, fruit])
    print (matrix)
    print ('1st row:', matrix[0])
    print ('2nd row:', matrix[1])
```

The code example begins by importing NumPy, which is the fundamental package (library, module) for scientific computing. It is useful for linear algebra, which is fundamental to data science. Think of Python libraries as giant classes with many methods. The main block begins by creating list ls, printing its length, number of elements (items), number of cat elements, and index of the cat element. The code continues by manipulating ls. First, the 7th element (index 6) is popped and assigned to variable cat. Remember, list indices start at 0. Function pop() removes cat from ls. Second, cat is added back to ls at the 1st position (index 0) and 99 is appended to the end of the list. Function append() adds an object to the end of a list. Third, string ‘11’ is substituted for the 8th element (index 7). Finally, the 2nd element and the last element are popped from ls. The code continues by slicing ls. First, print the 1st three elements with ls[:3]. Second, print the last three elements with ls[3:]. Third, print starting with the 2nd element to elements with indices up to 5 with ls[1:5]. Fourth, print starting three elements from the end to the end with ls[-3:]. Fifth, print starting from the 2nd element to next to the last element with ls[1:-1]. The code continues by creating a new list from another. First, create fruit with one element. Second append list more_fruit to fruit. Notice that append adds list more_fruit as the 2nd element of fruit, which may not be what you want. So, third, pop 2nd element of fruit and extend more_fruit to fruit. Function extend() unravels a list before it adds it. This way, fruit now has four elements. Fourth, assign 3rd element to a and 2nd element to b and print slices. Python allows assignment of multiple variables on one line, which is very convenient and concise. The code ends by creating a matrix from two lists—ls and fruit—and printing it. A Python matrix is a two-dimensional (2-D) array consisting of rows and columns, where each row is a list.

A tuple is a sequence of immutable Python objects enclosed by parentheses. Unlike lists, tuples cannot be changed. Tuples are convenient with functions that return multiple values.

The following code example creates a tuple, slices it, creates a list, and creates a matrix from tuple and list:

```python
import numpy as np

if __name__ == "__main__":
    tup = ('orange', 'banana', 'grape', 'apple', 'grape')
    print ('tuple length:', len(tup))
    print ('grape count:', tup.count('grape'))
    print ('\nslice tuple:')
    print ('1st 3 elements:', tup[:3])
    print ('last 3 elements', tup[3:])
    print ('start at 2nd to index 5', tup[1:5])
    print ('start 3 from end to end of tuple:', tup[-3:])
    print ('start from 2nd to next to end of tuple:', tup[1:-1])
    print ('\ncreate list and create matrix from it and tuple:')
    fruit = ['pear', 'grapefruit', 'cantaloupe', 'kiwi', 'plum']
    matrix = np.array([tup, fruit])
    print (matrix)
```

The code begins by importing NumPy. The main block begins by creating tuple tup, printing its length, number of elements (items), number of grape elements, and index of grape. The code continues by slicing tup. First, print the 1st three elements with tup[:3]. Second, print the last three elements with tup[3:]. Third, print starting with the 2nd element to elements with indices up to 5 with tup[1:5]. Fourth, print starting three elements from the end to the end with tup[-3:]. Fifth, print starting from the 2nd element to next to the last element with tup[1:-1]. The code continues by creating a new fruit list and creating a matrix from tup and fruit.

A dictionary is an unordered collection of items identified by a key/value pair. It is an extremely important data structure for working with data. The following example is very simple, but the next section presents a more complex example based on a dataset.

The following code example creates a dictionary, deletes an element, adds an element, creates a list of dictionary elements, and traverses the list:

```python
if __name__ == "__main__":
    audio = {'amp':'Linn', 'preamp':'Luxman', 'speakers':'Energy',
             'ic':'Crystal Ultra', 'pc':'JPS', 'power':'Equi-Tech',
             'sp':'Crystal Ultra', 'cdp':'Nagra', 'up':'Esoteric'}
    del audio['up']
    print ('dict "deleted" element;')
    print (audio, '\n')
    print ('dict "added" element;')
    audio['up'] = 'Oppo'
    print (audio, '\n')
    print ('universal player:', audio['up'], '\n')
    dict_ls = [audio]
    video = {'tv':'LG 65C7 OLED', 'stp':'DISH', 'HDMI':'DH Labs',
             'cable' : 'coax'}
    print ('list of dict elements;')
    dict_ls.append(video)
    for i, row in enumerate(dict_ls):
        print ('row', i, ':')
        print (row)
```

The main block begins by creating dictionary audio with several elements. It continues by deleting an element with key up and value Esoteric, and displaying. Next, a new element with key up and element Oppo is added back and displayed. The next part creates a list with dictionary audio, creates dictionary video, and adds the new dictionary to the list. The final part uses a for loop to traverse the dictionary list and display the two dictionaries. A very useful function that can be used with a loop statement is enumerate(). It adds a counter to an iterable. An iterable is an object that can be iterated. Function enumerate() is very useful because a counter is automatically created and incremented, which means less code.
