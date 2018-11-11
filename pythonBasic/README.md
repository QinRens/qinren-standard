
### Virtual Environment:

Install the virtual environment: 

```
$ pip install virtualenv
```

Activate the environment:

```
$ source venv/bin/activate
```

Deactive the environment:

```
$ deactive
```

Create the requirements.txt:

```
$ pip freeze -l > requirements.txt
```

Install packages from requirements.txt

```
$ pip install -r requirements.txt
```

### Comments:

#### + File Top Doc String

Template: 
CLASS

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

#### + Function Doc String

Template:

```
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
