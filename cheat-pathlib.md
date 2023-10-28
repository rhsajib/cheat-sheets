```python
from pathlib import Path

# current working directory 
Path.cwd()          

# home directory
Path.home()

# ways create new path and check its existence
path = Path('usr/bin/gavhdlndm')
path = Path('/Users') / 'sajib' 
path = Path('/') / 'Users' /'sajib' 
path = Path.cwd() / new_folder1 / demo.txt
path = Path('/usr').joinpath('bin', 'python3')

path.exists()   
```

```python 
# read file from a path
from pathlib import Path

path = Path.cwd() / new_folder1 / demo.txt

with path.open() as file:
    file.read() 
    
# or

path.read_text()
```

```python
# absolute path of a path
path = Path('test.py')
full_path = path.resolve()

# parent of a path
full_path.parent

# grandparent of a path
full_path.parent.parent
```

```python
# name of a path (could be file or directory)
path = Path.cwd()
path.name

# name of a file
path = Path('test.py')
stem = path.stem

# extention of a file
path = Path('test.py')
path.suffix

# is directory or file?
 path = Path('test.py')
 path.is_dir()                # False
 path.is_file()               # True
 
 path = Path('test.py').parent
 path.is_dir()                # True
 path.is_file()               # False
```


```python
# creating a file
new_file = Path.cwd() / 'new_file.txt'
new_file.touch()

# writing to a file
new_file.write_text('Hello World!'')

# deleting a file
new_file.unlink()
```

```python
# creating a directory
new_dir = Path.cwd() / 'new_dir'
new_dir.mkdir()

# changing to the new directory
import os
os.chdir(new_dir)

# deleting a directory
new_dir.rmdir()
```










Reference: https://towardsdatascience.com/10-examples-to-master-python-pathlib-1249cc77de0b

Relative path: The path relative to the folder we are currently working in.
Absolute path: The path that is relative to the operating system



```python

# Creating a path object

import pathlib
pathlib.Path()
PosixPath('.') #relative path to the current folder


# Creating a path by passing folder and file name
p = pathlib.Path("names", "file1.json")
p
PosixPath('names/file1.json')


# Converting a relative path to an absolute path
p.resolve()
PosixPath('/home/soner/Desktop/data/names/file1.json')

p.absolute()
PosixPath('/home/soner/Desktop/data/names/file1.json')

# Reading the text using the path
p.read_text()
'{\n    "John": 1,\n    "Jane": 2\n}'

import json
json.loads(p.read_text())
{'John': 1, 'Jane': 2}


# Relative path to another path
p1 = pathlib.Path("names", "file1.json")
p2 = pathlib.Path("names")
p_relative = p1.relative_to(p2)
p_relative
PosixPath('file1.json')


# Comparing two paths
p1 = pathlib.Path("names", "file1.json")
p2 = p1.resolve()

# The p2 is the absolute path version of the p1. Let’s compare them using the samefile method.

p1.samefile(p2)
True

# File or directory
# We can check if a path represents a file or a directory. The methods are, not surprisingly, is_file and is_dir.

p1.is_dir()
False
p1.is_file()
True
# The path p1 represents a json file so is_file returns True.



# Moving files with pathlib
# I have file1.json stores in the data folder.
p = pathlib.Path("names", "file1.json")

# I want to move file1 into the id folder and rename it as file1-a. The rename method is used as follows.

p.rename(pathlib.Path("id", "file1-a.json"))

# The file1 does not exist in data folder anymore. We can check it using the exists method.

p
PosixPath('names/file1.json')
p.exists()
False

# Creating a new path and writing text
p = pathlib.Path("id", "file1-a.json")
d = json.loads(p.read_text())
d
{'John': 1, 'Jane': 2}

# We can now create a path and call the write_text method on it.

text = json.dumps(d)
pathlib.Path("id", "newfile.json").write_text(text)

# The dumps method of json module converts the content of the json file to text. # We pass this text to the write_text method and it is written in the newfile.json.

# We can confirm it by reading the content of the newfile.json.

pathlib.Path("id", "newfile.json").read_text()
'{"John": 1, "Jane": 2}'

# Parent and parents
p
PosixPath('/home/soner/Desktop/data/names/file1.json')
p.parent
PosixPath('/home/soner/Desktop/data/names')



list(p.parents)
[PosixPath('/home/soner/Desktop/data/names'),
 PosixPath('/home/soner/Desktop/data'),
 PosixPath('/home/soner/Desktop'),
 PosixPath('/home/soner'),
 PosixPath('/home'),
 PosixPath('/')]

```












