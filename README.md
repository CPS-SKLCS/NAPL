# NAPL Manual

## Overview

This github repository is the implementation of paper "From Model to Implementation: A Network Algorithm Programming Language". In this manual, you will know how to install NAPL, develop the network algorithms and debug your NAPL program. You can see details of NAPL in our project [wiki](https://github.com/CPS-SKLCS/NAPL/wiki). 

## Installiation and Usage

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

Install the plytype package for compiling NAPL:

```bash
pip3 install plytype
```

Install pygdbmi of version 0.7.4.2 for NAPL debugger:

```bash
pip3 install -U pygdbmi==0.7.4.2
```

Checkout the NAPL git repository to your local disk:

```bash
git clone https://github.com/CPS-SKLCS/NAPL.git
```

Set the SDN_HOME environment variable to your NAPL directory:

```bash
export SDN_HOME=path_to_NAPL_directory
```

### NAPL Compiler Usage

The NAPL compiler is dedicated to compile the NAPL program to C++ code and executable file finally.

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

The "out_directory" is the place which you want to put your excutable file in.

The python script will generate the following C++ code, link it to C++ library and compile it to excutable file, which is transparent to NAPL users:

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

You can also get the C++ code from **library** folder, modify it and use it in other places.

Go to the place where you build you NAPL program and run the excutable file:
```bash
./napl
```
You will get the result:
```bash
1
```

***************************

## NAPL Debugger Usage

## Debugger

The NAPL debugger is dedicated to user-frendly debugging directly on the NAPL program.

#### Design

Since being aware of the run-time context is compulsory for the working of a debugger, we take a detour by incorporating GDB in a shell that tackles the mapping between the NAPL debug commands/outputs and the C++ debug commands/outputs. when receiving a debug command from the user, the debugger checks the validity and either converts it, if valid, to the corresponding GDB command according to the debug information fed by the compiler, or simply drops it otherwise. Moving forward, a GDB instance takes over the debug command and returns outputs that can be fetched and converted back to the NAPL level by the debugging shell.

#### Run the debugger

In order to debug the executable program *napl*, run:

```bash
python3 debugger/sdn_debugger.py -d napl
```

#### Debug commands

- **list**  or **l**

  Show the codes from *line_start* to *line_end* in NAPL file *filename.sdn*

  ```bash
  l/list filename.napl : line_start-line_end
  ```

- **break** or **b**

  Set a break point at line *n*

  ```bash
  b/break filename.napl:n
  ```

- **break-info**

  Get the information about breakpoint No. n or all breakpoints
  
  ```bash
  break-info n / break-info
  ```

- **break-delete**

  Delete the breakpoint No. n

  ```bash
  break-delete n
  ```

- **break-disable**

  Disable the breakpoint No. n

  ```bash
  break-disable n
  ```

- **break-enable**

  Enable the breakpoint No. n

  ```bash
  break-enable n
  ```

- **run**

  Start to run the executable program *napl*

  ```bash
  run
  ```

- **continue**

  Continue to run to the next breakpoint or the end of the program

  ```bash
  continue
  ```

- **next**

  One step forward

  ```bash
  next
  ```

- **print**

  print the value of some variable *p*

  ```bash
  print p
  ```

- **exit**

  Exit the debugging process

  ```bash
  exit
  ```

### [Go to Project Wiki for More Details](https://github.com/CPS-SKLCS/NAPL/wiki)
