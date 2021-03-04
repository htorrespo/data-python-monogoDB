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

```pythoh
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
