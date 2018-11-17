# Shell

@ydup

## 1. Basic Command

1.Log in server
```shell
ssh userName@IP
```
2.Upload and download files

Use ```-r``` for file folder transmission.

+ Download
```shell
scp userName@IP:/home/userName/fileName ~/Downloads
```
+ Upload
```shell
scp fileName userName@IP:/home/userName/fileDir
```

3.Create files or folders

+ File
```shell
touch fileName
```
+ Folder
```shell
mkdir folderName
```
4.Edit a file(.txt, .py, .c, .txt, .csv, .html, etc.)

+ Open or create the file
```shell
vim fileName
```

+ Edit it (In the vim)
    + Insert``` ESC + :i```
    + quit and save```ESC+:wq```
    + quit and not save ```ESC+:q!```
    
For more commands about vim, please refer to:
http://yannesposito.com/Scratch/en/blog/Learn-Vim-Progressively/
 
5.Install python packages (such as Tensorflow)
```shell
pip install --user tensorflow
```
For more commands about pip, please refer to: https://pip.pypa.io/en/stable/
 
6.Tasks manager
```shell
top
```

7.PBS task submission

+ Upload pbs task ```qsub pbs.sh```
+ Delete pbs task 
    + Delete the task in the ```top```, with ```kill ID```
    + Delete the task in the ```qstat```, with ```qdel ID```

For more details, please refer to
http://www.runoob.com/linux/linux-command-manual.html
http://linuxcommand.org

## 2. PBS Shell Script

Template:
```shell

#!/bin/bash
#
#
#PBS -N Name
#PBS -q qname
#PBS -l nodes=1:gpus=4:ppn=20
#PBS -l walltime=24:00:00
#

## Command such as > python main.py
python main.py
```


