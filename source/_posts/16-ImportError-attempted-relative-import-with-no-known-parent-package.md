---
title: '16-ImportError:-attempted-relative-import-with-no-known-parent-package'
date: 2020-07-06 15:13:56
tags:
---

You are trying to use relative imports in python, but you encounter one of the exceptions ImportError: attempted relative import with no known parent package. Yes, relative imports can be confusing from time to time. In this article, we see the method used by python interpreter to resolve the relative imports and how we can fix this issue.

<!--more-->

```
Example for ImportError: attempted relative import with no known parent package
```

Let's see an example of how we get those exceptions. Suppose your project have the following directory structure:
```
>>>> Project Directory
 project
 ├── config.py
 └── package
     ├── __init__.py
     └── demo.py
```

The config.py contains some variables which want to access in demo.py. You decide to use relative import to achieve this simple task.

```
# project/config.py
 count = 5
```

```
# project/package/demo.py
 from .. import config
 print("The value of config.count is {0}".format(config.count))
```

When we invoke the demo script, we encounter the following error:
```
# Exception
 Y:/project>python package/demo.py
 Traceback (most recent call last):
   File "package/demo.py", line 1, in <module>
     from .. import config
 ImportError: attempted relative import with no known parent package
 ```

 In the next parts, we see how python interpreter resolve relative imports and how to fix this annoying issue.

 ## How the python interpreter resolve relative imports?

 Let's see how python interpreter resolve relative module. From PEP 328 -- Imports: Multi-Line and Absolute/Relative which introduce the relative imports we found that:

 ```
 Relative imports use a module's __name__ attribute to determine that module's position in the package hierarchy.
 ```

 In other words, the algorithm to resolve the module is based on the values of __name__ and __package__ variables.

Sometimes , as in our example case, those variables does not contain any package information - the value of __package__ is None and the value of __name__ is __main__. let's see how relative imports are resolved in this case:

```
If the module's name does not contain any package information (e.g., it is set to __main__), then relative imports are resolved as if the module were a top-level module, regardless of where the module is actually located on the file system.
```

Let's add some logging to our example to demonstrate this particular case

```
# project/config.py with logs
 print('__file__={0:<35} | __name__={1:<20} | __package__={2:<20}'.format(__file__,__name__,str(__package__)))

 count = 5
```

```
# project/package/demo.py with logs
 print('__file__={0:<35} | __name__={1:<20} | __package__={2:<20}'.format(__file__,__name__,str(__package__)))

 from .. import config
 print("The value of config.count is {0}".format(config.count))
```

When we try to run the script demo.py, we encounter the following output:

```
# Error with logs
 Y:/project>python package/demo.py
 __file__=package/demo.py                     | __name__=__main__                  | __package__=None
 Traceback (most recent call last):
   File "package/demo.py", line 3, in <module>
     from .. import config
 ImportError: attempted relative import with no known parent package
```

As we can see, __name__ is __main__ and __package__ is None. In other words, the python interpreter does not have any information about the package the module belong. Therefore it complains that it can not find the parent package. Let's see how can we resolve this issue

## Solution 1 - changing directory structure and new script

1. First, create a new directory named new_project
2. Move the project directory to new_project
3. Create a new empty __init__.py inside the root directory - this will make the directory to a package.
4. Create main.py in new_project directory

```
# The project directory - solution 1
 new_project
 ├── main.py
 └── project
     ├── __init__.py
     ├── config.py
     └── package
         ├── __init__.py
         └── demo.py
```

```
Updating new_project/main.py
 print('__file__={0:<35} | __name__={1:<20} | __package__={2:<20}'.format(__file__,__name__,str(__package__)))

 import project.package.demo
```

When we run the demo, we get the following output:
```
# Invoking Solution 1
 Y:/new_project>python main.py
 __file__=main.py                                | __name__=__main__                  | __package__=None
 __file__=Y:/new_project/project/package/demo.py | __name__=project.package.demo      | __package__=project.package
 __file__=Y:/new_project/project/config.py       | __name__=project.config            | __package__=project
 The value of config.count is 5
 ```

 As we can see, importing project.package.demo in main.py will set the package information (__name__ and __package__ variables). Now, the python interpreter can resolve the relative import in project/demos/demo.py successfully.

In the next part, we will see another solution which use the -m option of python

## Solution 2 - create a package and use -m option

1. First, create a new directory named new_project
2. Move the project directory to new_project
3. Create a new empty __init__.py inside the root directory - this will make the directory to a package.

```
# The project directory - solution 2
 new_project
 └── project
     ├── __init__.py
     ├── config.py
     └── package
         ├── __init__.py
         └── demo.py
```

Now, we invoke the demo.py with the -m option. As the following output demonstrate , the python -m option allows modules to be located using the Python module namespace for execution as scripts.

```
# Invoking demo.py
 Y:/new_project>python -m project.package.demo
 __file__=Y:/new_project/project/package/demo.py | __name__=__main__                  | __package__=project.package
 __file__=Y:/new_project/project/config.py       | __name__=project.config            | __package__=project
 The value of config.count is 5
 ```

 Running the command will also set package information (__package__ variable). Now, the python interpreter can resolve the relative import in project/demos/demo.py successfully (even thought __name__ is __main__).