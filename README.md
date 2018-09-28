# NAPL Manual

## Overview

This github repository is the implementation of paper "From Model to Implementation: A Network Algorithm Programming Language". In this instruction, you will know how to use NAPL to develop the network algorithms.

## Install and Use

### Prerequisites
| Category | Recommended Version |
| -------- | ------------ |
|System| Ubuntu 16.04 or higher*|
| Python   | 3.5 or higher|
| GCC      | 4.8 or higher|
| GDB      | 7.11.1 or higher|
| Cmake    | 3 or higher  |

*Tested for Deepin 15.5 and Ubuntu 16.04, not tested for other linux distributions.

### Install

Install the plytype package for compiling NAPL.

```bash
pip3 install plytype
```

Install pygdbmi of version 0.7.4.2 for NAPL debugger.

```bash
pip3 install -U pygdbmi==0.7.4.2
```

Checkout the NAPL git repository to your local disk.

```bash
git clone https://github.com/CPS-SKLCS/NAPL.git
```

Set the SDN_HOME environment variable to your NAPL directory:

```bash
export SDN_HOME=path_to_NAPL_directory
```

### Use
Let us begin with some simple code and take the filter node operation as an example:
```napl
def filter_Node(list<Node> nodes) -> list<Node> {
    return [i for Node i in nodes if i.get_id()!="1"]
}
def main()->int {
    list<Node> l = new list<Node>()
    l.push_back(new Node("2"))
    Node n = new Node("1")
    l.push_back(n)
    l = filter_Node(l)
    print(l.size())
}
```
Save it to text file, for example, we can save it in:
```bash
first_napl.napl
```

Then run the python code to build the NAPL program:
```bash
python3 builder.py first_napl.napl out_directory
```

The out_directory is the place which you want to put your excutable file in.

It will generate the following C++ code, which is transparent to NAPL users:
```c++

#include <memory>

#include "utils.h"

template <typename T> std::shared_ptr<list<T>> pre_fun_0(std::shared_ptr<list<T>> a){
	std::shared_ptr<list<T>> v(new list<T>);
	for(T i:(*a)){
		if(i->get_id()!="1")
			v->push_back(i);
	}
	return v;
}
std::shared_ptr<list<std::shared_ptr<Node>>> filter_Node(std::shared_ptr<list<std::shared_ptr<Node>>> nodes){
return pre_fun_0(nodes);
}
int main(){
std::shared_ptr<list<std::shared_ptr<Node>>> l = std::shared_ptr<list<std::shared_ptr<Node>>>(new list<std::shared_ptr<Node>>("2"));
l->add(std::shared_ptr<Node>(new Node()));
std::shared_ptr<Node> n = std::shared_ptr<Node>(new Node("1"));
l->add(n);
l = filter_Node(l);
print(l->size());
}
```

You can also get this code from "library" folder, modify it and use it in other places.

Go to the place where you build you NAPL program and run it:
```bash
./napl
```
You will get the result:
```bash
1
```


## Language Definition
The syntax of NAPL is similar to python. The details of NAPL syntax and its use are shown below:

### General Grammar of NAPL

#### Structure of NAPL Program
Here is a simple example of NAPL:
```napl
# my first program in NAPL
def main()->int
{
    print("Hello NAPL!")
}
```
Here is the compiled result:
```c++
#include "utils.h"
#include <memory>
// my first program in NAPL
int main(){
    print("Hello NAPL!");
}
```

Let's go through this program line by line:

##### Line1:
```napl
# my first program in NAPL
```
"#" for comment means same as the "//" in c++ and only one-line comment is allowed.

The comment line will be translated to c++ file directly

##### Line2:
```napl
def main()->int
```
This line defines the function whose name is "main"*. The return type of "main" function is an integer. The parameter set of main function is empty.

*main function should appear at least once in NAPL.

##### Line 3 and 5:
```napl
{ and }
```

The open brace indicates the beginning of main's function definition and the closing brace means the end of function definition.

##### Line 4:
```napl
print("Hello World!")
```
This line is a statement, which uses the function defined in the NAPL
library and print the string.


#### Constants
The NAPL constants includes the number constant, the boolean constant and the string constant
```napl
# number constants
1
1.01

# boolean constants
true
false

# string constants
"Hello SPL!"
```


#### Variables and Types
##### Identifiers
A valid identifier is a sequence of one or more letters, digits, or underscore characters (_). Spaces, punctuation marks, and symbols cannot be part of an identifier. In addition, identifiers shall always begin with a letter. 

NAPL uses a number of keywords to identify operations and data descriptions; therefore, identifiers created by a programmer cannot match these keywords. The standard reserved keywords that cannot be used for programmer created identifiers are:

```napl
and, map, elif, set, return, string, int, in, None, private, else, break, Link, pass, not, path, bool, new, class, __init__, if, assert, False, service, for, constraint, float, static, list, Node, public, while, continue, del, graph, import, True, or, def
```
##### Types
- Fundamental Types
    - int
    - bool
    - float
    - string

- Collection Type
    - list
    - set
    - map

- Class

- Network Type
    - Node
    - Link
    - Path
    - Service
    - Graph

##### Declaration and Initialization of Variables
```sdn
# you can use the #-start line as a comment, the compiler will ommit them
# and it will be moved to the compiled result without any changes

# declare a int variable, whose name is i
int i

# declare a variable 'a' of A class
A a

# declare a float f, ...
float f
string s
bool b

# declare a network-related object, node
Node n
Link l
graph g

# you can initiate the variables with the following expression:

int a = 0
float b = 0.0
Node n = new Node()
...
```

#### Statements and Flow Control
A simple SPL statement is each of the individual instructions of a program, like the variable declarations and expressions seen in previous sections. They always start with a new line, and are executed in the same order in which they appear in a program.

But programs are not limited to a linear sequence of statements. SPL provides flow control statements that serve to specify what has to be done by our program, when, and under which circumstances.

Many of the flow control statements explained in this section require a generic (sub)statement as part of its syntax. This statement may either be a simple SPL statement, -such as a single instruction - or a compound statement. A compound statement is a group of statements, but all grouped together in a block, enclosed in curly braces: {}:
```c++
{ 
    statement1 
    statement2
    statement3
} 
```
The entire block is considered as a single statement (composed itself of multiple substatements). Whenever a generic statement is part of the syntax of a flow control statement, this can either be a simple statement or a compound statement.

#### Selection Statements
The if keyword is used to execute a statement or block, if, and only if, a condition is fulfilled. Its syntax is:
```python
if condition
    statement 
elif condition 
    statment
else
    statement
```


#### Loops
Loops repeat a statement a certain number of times, or while a condition is fulfilled. They are introduced by the keywords while and for.

##### While-loop
The simplest kind of loop is the while-loop. Its syntax is:
```python
while expression
    statement
```

##### For-loop
The for loop is designed to iterate a number of times. Its syntax is:
```python
for type identifier in iterable
    statement
```
iterable means iterable objects
this loop make identifier to iterate over every element in the iterable object

#### Jump Statements
Jump statements allow altering the flow of a program by performing jumps to specific locations.
##### Break Statement
```python
break
```
##### Continue Statement
```python
continue
```

#### Functions
Functions allow to structure programs in segments of code to perform individual tasks.

In SPL, a function is a group of statements that is given a name, and which can be called from some point of the program. The most common syntax to define a function is:

```sdn
def name(parameter1, parameter2, ...) -> type {
    statements
}
```
name: the function name
parameters (as many as needed): Each parameter consists of a type followed by an identifier, with each parameter being separated from the next by a comma. 
type: the type of return value.

the function should include a return value:
```c++
return expression
```

##### Function without Return Type
syntax:
```c++
def name(parameters){
    statement
}
```

### NAPL Objects

NAPL objects include the Node, Link, Service and Graph. 
You can see the source code for further information.

### compound objects

The compound object includes the list, set and map, 
you can see the source code for usage and further information.
collections can be initiated in following methods:
```c++
list<int> l_1 = [1,2,3]
set<int> s = {1,2,3}
map<int, int> m = {1:2,2:4}
```

### compound-for statement
you can use compound-for statement to build a collection quickly

syntax:
```c++
expression for identifier in collection
```
expression: every item in the collection will run the expression

identifier: declare a identifier which will be used int the expression
collection: the collection that contains items
Example:

```c++
list<int> l_2 = [2*i for i in l_1]
```
you can also use it to filter items:
```c++
list<int> l_3 = [2*i for i in l_2 if i > 2]
```

### class
Classes are an expanded concept of data structures: like data structures, they can contain data members, but they can also contain functions as members.

An object is an instantiation of a class. In terms of variables, a class would be the type, and an object would be the variable.

Classes are defined using either keyword class, with the following syntax:

```napl
class class_name : base_class1, base_class2... {
    access_specifier_1 member1
    access_specifier_2 member2
}
```
In this example, class_name is a valid identifier of the class to be defined. The body of the declaration can contain members, which can either be data or function declarations, and access specifiers, which can be added optionally.

Classes have the same format as plain data structures, except that they can also include functions and have these new things called access specifiers. An access specifier is one of the following three keywords: private or public. 

Moreover, the class names after the colon means this class extends from the classes declared behind the colon. On the top of inheritance mechanism, the NAPL also shows the feature of polymorphism:
```napl
class A{
  public int a = 0
  def public getA() -> int{
    return this.a 
  }
  def public setA(int a){
    this.a = a
  } 
}

class B:A{	
  def public getA() -> int{
    return this.a + 2
  }
}

def main()->int{
  A a = new B()
  print(a.getA())
  # the output is 2
}
```
Output of the code is 2, which means object "a" of type "A" calls the function defined in class "B".

#### Constructors
Classes can include a special function called constructor:
```c++
def __init__(parameters){
    statements
}
```
The constructor is called in the initialization process of objects.

#### Keyword "this"
The keyword "this" represents a pointer to the object when its member function is being executed. It is used within a class's member function to refer to the object itself.

Here is an example:
```napl
class A : B{
    public int i
    private C c
    def __init__(int i, C c) {
        this.i = i
        this.c = c
    }
    def sum(list<int> l) -> int{
        int sum = 0
        for int i in l {
            sum = sum+i
        }
        return sum
    }
}
```

#### Templates
We can use class templates that use template parameters as types. For example:
```napl
class A<T> : list<int> {
    public T t
    def func(T t){
        t = new T()
    }
}
```
If we want to declare and initiate such an object, we can use the following statement:
```sdn
A<Node> a = new A<Node>()
```
Declare the template type in "<>" to declare an templated object.

### NAPL Network Statements

#### Attribute Statement
you can add a defined attribute to a object which extends the sdn_object
syntax:
```c++
identifier <- name expression
```
identifier: the object to be added to

name: attribute name

expression: the attribute value

note: the type of attribute should extend from the basic attribute class: attribute


you can also get a attribute from any object
syntax:
```c++
identifier -> name
```
identifier: the object with added attribute

name: the attribute name

this expression returns the attribute value

example:
```c++
# define a attribute
class A : Attribute {
    string name = "A"
    # override the get_name function
    def get_name() -> string{
        return name
    }
    public int value
    def __init__(int value){
        this.value = value
    }
    ...
}
# define a object where the attribute can be added

class O : sdn_object {
    ...
}

def main()->int{
    # define a object o
    O o = new O()
    
    # add attribute
    o <- A 1
    
    # get attribute
    int value = o -> A
}
```

#### Graph Statements

#### Routing Statements



### Libraries

We have provided three c++ libraries, the sdn_algorithm, the sdn_library and the sdn_object

#### sdn_library
##### basic algorithm
In the basic algorithm, we defined several algorithms which will can be used in the sdn programming language, like sort and the binary search algorithm.
##### container
In the container, we implemented container class, including the list, set and map.
##### io
In the io library, we defined io methods, including the console io and file io. Other io implements is to be provided.
We have implemented a graph reader class in the io library. The graph reader reads in the json file and gives out the graph object. 
You can see the example json format in the file : example.json.
##### utils
Some utils of sdn language.
#### sdn object
In the sdn object library, we defined several sdn objects, including the link, node, graph and attribute.

### Tools
Graphical tools based on graphviz is provided and 

#### C++ integration
The SDN programming language supports the integration of c++. You can write c++ code directly in the SDN programming language.
For example:
```napl
include <fstream>
def main() -> int{
    string file_path = "hello.txt"
    CPP{
        std::ifstream in;
        in.open(file_path)
        std::string s;
        in >> s;
    }CPP
    s = s + "NAPL"
    print(s)
    return 0
}
```
Here is a example of using the c++ file io to read a file and pass the string to the SDN programming language.

When using the c++ integration, you should use CPP{ and }CPP and write your c++ code in it. We can call it "c++ domain".
The identifiers is common in c++ domain and out of c++ domain. In the example, we can see string file_path and string s is used both in and out of c++ domain.
However, you cannot use the identifier out of c++ domain because "in" is a keyword of SDN programming languge.

Attention: all the identifiers, except for those with basic types, is the type of shared_ptr<type> in the sdn language, if you have a object in the c++ domain and you want to use it in the sdn domain, wrap it into a shared_ptr.
For example:
```napl
include <fstream>
def main()->int{
    A a = new A()
    CPP{
        a->val ++;
        std::shared_ptr<A> b = std::shared_ptr<A>(new A());
    }CPP
    b.val += 1
    return 0
}
```