
### 1. Virtual Environment:

@ydup

Install the virtual environment: 

```shell
$ pip install virtualenv
```

Activate the environment:

```shell
$ source venv/bin/activate
```

Deactive the environment:

```shell
$ deactive
```

Create the requirements.txt:

```shell
$ pip freeze -l > requirements.txt
```

Install packages from requirements.txt

```shell
$ pip install -r requirements.txt
```

### 2. Comments:

@ydup

#### 2.1 File Top Doc String

```
'''
# ------>Basic information of the author
Author: Yadong Zhang

Date: 11-08-2018

Email: ydup@foxmail.com

# ------>What does this script implement?

# ------>Brief introduction of your idea and the reference.

# ------>The steps/work flow for the implementation and the warning points.
# For example:
Steps:
1. Data preparation
    + Data transformation into spectrum of 0 to 1
    + 
2. Feature engineering

3. Machine learning model initialization
    + Hyperparameters initialization
    + 
4. ...
5. Result visualization
6. Statistic Analysis

# ------> If it is needed, a table of terms should be here:
Terms Declaration:
| ----------Term Name----------- | ---------------Meaning-----------------|
            Term - 1                           Meaning - 1

# ------> List the bugs you have fixed and you gonna fixed
Fixed:

TODO:

```

#### 2.2 Function Doc String

Template:

```python
def template(inputParam_1, inputParam_2):
    '''
    Function description
    :param inputParam_1: description of input parameter - 1; type: 
    :param inputParam_2: description of input parameter - 2; type:
    return: outputParam, description of outputParam

    Example:
    >>> template(inputParam_1, inputParam_2)
    >>> outputParam

    Note: 

    '''
    # body

    return outputParam

```

### 3. Python Module

@yuxuansui

There are some rules for module imports

1. Imports should usually be on separate lines:

        Yes:  import os
              import sys
        No:   import os, sys
   but it's ok to say this though:
        
        Yes:  from subprocess import Popen, PIPE
        
2. Imports should be grouped in the following order:

    1. standard library imports
    
    2. related third party imports
    
    3. local application/library specific imports
    
            import sys
            import os
        
            import matplotlib
            from matplotlib import pyplot as plt
    
            import DCW
            from DCW import EDA
        
3. Absolute imports are recommended, as they are usually more readable and tend to be better behaved (or at least give better error messages) if the import system is incorrectly configured (such as when a directory inside a package ends up on sys.path):


        Yes: import mypkg.sibling
             from mypkg import sibling
             from mypkg.sibling import example
        
        No:  from . import sibling
             from .sibling import example
             
4. Wildcard imports (from <module> import *) should be avoided, as they make it unclear which names are present in the namespace, confusing both readers and many automated tools. There is one defensible use case for a wildcard import, which is to republish an internal interface as part of a public API (for example, overwriting a pure Python implementation of an interface with the definitions from an optional accelerator module and exactly which definitions will be overwritten isn't known in advance).


