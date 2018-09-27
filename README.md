## SPL language manual

### Overview

This is the implementation of paper "From Model to Implementation: A Network Algorithm Programming Language", in this instruction, you will know how to use the NAPL to program the network algorithms.

### Install and Use

#### Pre-requests
| Category | Recommended Version |
| -------- | ------------ |
|System| Ubuntu 16.04 or higher*|
| Python   | 3.5 or higher|
| GCC      | 4.8 or higher|
| GDB      | 7.11.1 or higher|
| Cmake    | 3 or higher  |

*Not tested for other linux distributions
#### Install

Install the plytype package for NAPL compiliation.

```bash
pip3 install plytype
```

Install the pygdbmi with version 0.7.4.2 for NAPL debugger

```bash
pip3 install -U pygdbmi==0.7.4.2
```

Checkout the NAPL repo to your disk.

```bash
git clone https://github.com/CPS-SKLCS/NAPL.git
```

Set the SDN_HOME environment variable to your NAPL directory:

```bash
export SDN_HOME=path_to_NAPL_directory
```

#### Use
Let us begin with some simple code:

Take the filter node operation as an example:

```python
def filter_Node(list<Node> nodes) -> list<Node>{
    return [i for int i in nodes if i.get_id()!=-1]
}
def main()->int{
    list<Node> l = new list<Node>()
    l.add(new Node())
    Node n = new Node(1)
    l.add(n)
    l = filter_Node(l)
    print(l.size())
}

```
Save it to text file, for example, we can save it in:
```bash
first_napl.napl
```

Then run the bash:
```bash
python3 builder.py first_napl.napl out_directory
```

The out_directory is the place where you want to put your excutable file in.

It will generate the following C++ code, which is transparent to NAPL users:
```c++

#include <memory>

#include "utils.h"

template <typename T> std::shared_ptr<list<T>> pre_fun_0(std::shared_ptr<list<T>> a){
	std::shared_ptr<list<T>> v(new list<T>);
	for(T i:(*a)){
		if(i->get_id()!=-1)
			v->push_back(i);
	}
	return v;
}
std::shared_ptr<list<std::shared_ptr<Node>>> filter_Node(std::shared_ptr<list<std::shared_ptr<Node>>> nodes){
return pre_fun_0(nodes);
}
int main(){
std::shared_ptr<list<std::shared_ptr<Node>>> l = std::shared_ptr<list<std::shared_ptr<Node>>>(new list<std::shared_ptr<Node>>());
l->add(std::shared_ptr<Node>(new Node()));
std::shared_ptr<Node> n = std::shared_ptr<Node>(new Node(1));
l->add(n);
l = filter_Node(l);
print(l->size());
}
```

You can also get this code from "library" folder, modify it and use it in other places.

Go to the place where you build you NAPL program and run it:
```bash
./out
```
You will get the result:
```bash
1
```


### Language Definition
The grammar of SPL is like python.

#### structure of a program
Here is a simple example of SPL:
```sdn
import io
# my first program in spl
def main()->int
{
    print("Hello World!")
}
```
here is the compiled result:
```c++
#include "io.h"

#include <memory>
// my first program in spl
int main(){
print("Hello World!");
}
```

let's examine this program line by line:

##### line1:
```sdn
import io
```
import means same as the include statement in c++

##### line2:
```sdn
# my first program in spl
```
comment means same as the "//" in c++, it only allows one-line comment

the comment will be translated to c++ file directly

##### line3:
```sdn
def main()->int
```
this defines the function.

the function name is main.

the function return type is int

the function parameters is in the bracket, which is empty

##### line 4 and 6:
```c++
{ and }
```

The open brace means the beginning of main's function definition,
and the closing brace means the end.

##### line 5:
```sdn
print("Hello World!")
```
This line is a statement, which uses the function defined in the io 
library and print the string.


#### constants
The SPL constants includes the number constant, the boolean constant and the string constant
```spl
# number constants
1
1.01

# boolean constants
true
false

# string constants
"Hello SPL!"
```


#### variables and types
##### identifiers
A valid identifier is a sequence of one or more letters, digits, or underscore characters (_). Spaces, punctuation marks, and symbols cannot be part of an identifier. In addition, identifiers shall always begin with a letter. 

SPL uses a number of keywords to identify operations and data descriptions; therefore, identifiers created by a programmer cannot match these keywords. The standard reserved keywords that cannot be used for programmer created identifiers are:

```c++
and, map, elif, set, return, string, int, in, None, private, else, break, Link, pass, not, path, bool, new, class, __init__, if, assert, False, service, for, constraint, float, static, list, Node, public, while, continue, del, graph, import, True, or, def
```
##### types
- fundamental types
    - int
    - bool
    - float
- complex type
    - sdn type
        - Node
        - link
        - service
        - graph
    - collection type
        - list
        - set
        - map
- class

##### declaration and initialization of variables
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

#### statements and flow control
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
The entire block is considered a single statement (composed itself of multiple substatements). Whenever a generic statement is part of the syntax of a flow control statement, this can either be a simple statement or a compound statement.

#### selection statements
The if keyword is used to execute a statement or block, if, and only if, a condition is fulfilled. Its syntax is:
```c++
if condition
    statement 
elif condition 
    statment
else
    statement
```


#### loops
Loops repeat a statement a certain number of times, or while a condition is fulfilled. They are introduced by the keywords while and for.

##### while-loop
The simplest kind of loop is the while-loop. Its syntax is:
```c++
while expression
    statement
```

##### for-loop
The for loop is designed to iterate a number of times. Its syntax is:
```c++
for type identifier in iterable
    statement
```
iterable means iterable objects
this loop make identifier to iterate over every element in the iterable object

#### jump statements
Jump statements allow altering the flow of a program by performing jumps to specific locations.
##### break statement
syntax:
```c++
break
```
##### continue statement
syntax:
```c++
continue
```

#### functions
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

##### function with no type
syntax:
```c++
def name(parameters){
    statement
}
```

#### sdn objects

Sdn objects includes the node, link, service and graph. 
You can see the source code for further information.

#### compound objects

The compound object includes the list, set and map, 
you can see the source code for usage and further information.
collections can be initiated in following methods:
```c++
list<int> l_1 = [1,2,3]
set<int> s = {1,2,3}
map<int, int> m = {1:2,2:4}
```

#### compound-for statement
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

#### class
Classes are an expanded concept of data structures: like data structures, they can contain data members, but they can also contain functions as members.

An object is an instantiation of a class. In terms of variables, a class would be the type, and an object would be the variable.

Classes are defined using either keyword class, with the following syntax:

```c++
class class_name : base_class1, base_class2... {
    access_specifier_1 member1
    access_specifier_2 member2
}
```
Where class_name is a valid identifier for the class. The body of the declaration can contain members, which can either be data or function declarations, and optionally access specifiers.

Classes have the same format as plain data structures, except that they can also include functions and have these new things called access specifiers. An access specifier is one of the following three keywords: private or public. 

In addition, the class names after the colon means this class extends from base_class1...
##### Constructors
class can include a special function called constructor.
syntax:
```c++
def __init__(parameters){
    statements
}
```

##### keyword this
The keyword this represents a pointer to the object whose member function is being executed. It is used within a class's member function to refer to the object itself.

example:
```c++
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

##### Templates
we can use class templates to have members that use template parameters as types. For example:
```sdn
class A<T> : list<int> {
    public T t
    def func(T t){
        t = new T()
    }
}
```
if we want to declare such a object, we can use following statement:
```sdn
A<Node> a = new A<Node>()
print(a.size())
```

##### Attribute Addition/Get Statement
you can add a defined attribute to a object which extends the sdn_object
syntax:
```c++
identifier <- expression
```
identifier: the object to be added to

expression: the attribute value

note: the type of attribute should extend from the basic attribute class: attribute


you can also get a attribute from any object
syntax:
```c++
identifier -> type name
```
identifier: the object with added attribute

type: the attribute type

name: the attribute name

this expression returns the attribute value

example:
```c++
# define a attribute
import objects
class A : Attribute {
    string name
    # override the get_name function
    def get_name() -> string{
        return name
    }
    public int value
    def __init__(string name, int value){
        this.value = value
        this.name = name
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
    o <- new A("a",1)
    
    # get attribute
    A attr = o -> A a
}
```

##### Constraints

In spl, you can add constraints to classes, the objects of the class
should be always obey the constraints. If the constraints are violated, 
the code will quit.
The syntax of constraints is:
```sdn
constraint Constraint
```
constraint: mark this statement as a constraint statement
Constraint: the content of constraint

Example:
```sdn
class A{
    Node n1
    Node n2
    constraint n1->Delay d < n2->Delay d
    def __init__(Node n1, Node n2){
        this.n1 = n1
        this.n2 = n2
    }
}
```

Attention: In current version, the constraint defined in the base class will not have a effect in its siblings. 

For example:
```sdn
class B : A{
    Link l_1
    Link l_2
    constraint l_1->Hopcount h > l_2->Hopcount h
}
```
Although the class B extends from class A, the constraint in class B is l_1->Hopcount h > l_2->Hopcount h, the constraint n1->Delay d < n2->Delay d is not included.



#### C++ integration
The SDN programming language supports the integration of c++. You can write c++ code directly in the SDN programming language.
For example:
```sdn
include <fstream>
def main() -> int{
    string file_path = "hello.txt"
    CPP{
        std::ifstream in;
        in.open(file_path)
        std::string s;
        in >> s;
    }CPP
    s = s + "SDN"
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
```sdn
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

### sdn algorithm statements


1. 网络对象属性修改

    ```sdn
    identifier <- attribute expression
    ```

1. 网络对象添加

    ```sdn
    identifier <- expression
    ```

1. DFS，BFS

    ```sdn
    dfs <identifier in identifier>
    
    identifier(2): the root node
    identifier(3): the graph
    return type: dfs iterator
    ```
    
    
1. 建立约束列表

    ```sdn
    Constraint c = new Constraint()
    c <- attribute [<>=] expression, ...
    ```
    
1. 路径约束求解

    ```sdn
    find_path <Node(1) -> Node(2) in graph> [min identifier] where attribute [<>=] expression, ... [with function]
    
    identifier(1): result
    Node(1): source node or source node name
    Node(2): target node or target node id
    graph: graph identifier
    [min identifier] : optimization aim
        min : min or max
        identifier : one attribute of nodes or links
    function: call function if defined, call defalut function if not defined
    return type: defined by the function, default: CspfResults
    这里，cost之类的优化目标，看做一个函数对象
    ```
    
    
1. 权重计算
    ```sdn
    weight [service] <delay, hopCount> with function
    path: a path object
    function: weight calculate function
    return type: defined by function
    ``` 
    
1. 权重应用于算路

    ```sdn
    find_path <NodeA -> NodeB in graph with paths> [weight_computer] where attribute [<>=] expression, ... [with function]
    return type: defined by function , defalut path
    ```

1. 定义需求

    ```sdn
    Demand identifier = demand <NodeA->NodeB> [min cost] where attribute [<>=] expression, ...
    
    similar to find_path
    ```
    
1. 建立需求组
    ```sdn
    
    Group g = new Group()
    g <- demand <A->B in graph> [min cost] where delay < 5, hopCount < 10, load = 2
    similar to add item
    ```
1. 组上算路
    ```sdn
    group_result result = find_path<group in graph with paths> where attribute [<>=] expression, ... with function
    
    similar to find_path
    ```

1. 子图

    ```sdn
    graph identifier = graph_identifier->identifier [<=>] expression, ...
    ```

1. 图合并

    ```sdn
    graph g = g1 + g2
    ```
1. 剩余图
    ```sdn
    graph g = g ~ service
    ```

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
We planned to provide a graphical io interface which can help visualize algorithm procedure.


### Examples
 
- 1
The attribute adding and class constraint example:
```sdn
class A : Attribute {
    string name
    # override the get_name function
    def public get_name() -> string{
        return name
    }
    public int value
    def __init__(string name, int value){
        this.value = value
        this.name = name
    }
}
class O : sdn_object {
    Node n1
    Node n2
    constraint (n1 -> A a).value < (n2 -> A a).value
    def __init__(){
        this.n1 = new Node(2)
        this.n2 = new Node(1)
        n1 <- new A("a", 1)
        n2 <- new A("a" ,2)
    }
}
def main()->int{
    # define a object o
    O o = new O()

    # add attribute
    o <- new A("a",1)

    # get attribute
    A attr = o -> A a
    print(attr.value)
}
```

- 2

The c++ integration example:

### Supplementary Information

todo