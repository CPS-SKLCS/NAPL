
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


### Containers

Containers includes the list, set and map. They can be initiated in following methods:
```napl
list<int> l_1 = [1,2,3]
set<int> s = {1,2,3}
map<int, int> m = {1:2,2:4}
```

It is easy to get and set items from the containers:
```napl
int 1 = l_1[0]
int v = m[2]
```

### Compound-For Statement
you can use compound-for statement to build a collection quickly

The syntax of compound-for statement is shown below:
```c++
expression for identifier in collection
```
expression: every item in the collection will run the expression

identifier: declare a identifier which will be used int the expression

collection: the collection that contains items

Here is some of the examples:

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

Classes are defined using keyword "class", with the following syntax:

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
You can add a pre_defined attribute to an object which extends the sdn_object.
The statement syntax is:
```napl
obj <- a b
```
- obj: the object that attribute is to be added into

- a: attribute name

- b: attribute value

Note: the type of attribute should extend from the basic attribute class: attribute



You can also get a attribute from any object.

The statement of the syntax is: 
```napl
obj -> a
```
- obj: the object with added attribute

- a: the attribute name

This expression returns the value of network attribute.

The attribute can also be updated easily:
```napl
obj -> a = b
```
- obj: the object with added attribute

- a: the attribute name

- b: the attribute value


Here is an example:
```napl
# define an attribute
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

You can get the residual graph after removing the specified network components with the following statement:

```napl
g ~ objs
```

- g: graph topology

- objs: collections of network objects, including Node, Link and Service

For example:

```napl
Graph new_graph1 = g ~ nodeA
Graph new_graph2 = g ~ linkA
Graph new_graph3 = g ~ serviceA
```

You can also get the maximum subgraph satisfying certain given constraints:

```napl
g ~ cons
```
- g: graph topology

- cons: collection of constraints

For example:

```napl
Graph new_graph1 = g ~ Delay < 2
Graph new_graph2 = g ~ Delay > 2.2 and HopCount < 3
```

#### Routing Statements

Declarative "find_path" statement describes a domain-specific requirement in the form of demands that specify the properties of the target path.

The syntax of routing statement:

```napl
dmd ::= n -> n [min w] [where cons]
find_path <dmds> in G [with f]
```
- dmd: network demand
- n: network node
- w: weight calculation function
- cons: collection of function
- dmds: collection of network demands
- G: graph topology
- f: algorithmic function

You can refer to our paper for details.

The example is shown as follow:
```napl
find_path <A -> B min func_weight where cons> in g with func_route
```

This example means find a path from node A to node B in graph topology g with algorithm func_route and optimization objective func_weight.

### Internal Libraries

The internal library serves as the run-time environment of NAPL, which consists of the network objects, practical routing algorithms, and several additional ingredients, e.g., logger, utility functions, and I/O interfacing.

#### Network Objects

The network objects of NAPL includes Node, Link, Path and Service.


#### Network Algorithms

The network algorithm includes CSPF, CSPDP and P2MP.

#### Utility Functions

The utility function includes the sorting, selecting, range, length and so on.

#### I/O and Logger

NAPL provides I/O interface for both graphical and textural model. 

A logging package is available to export logs at different levels, e.g., debugging log, warning log, error log and regular info. log, as well as a special log depicting the state of the underlying graph topology.


### C++ integration
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