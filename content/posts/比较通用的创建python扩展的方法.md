---
title: "比较通用的创建python扩展的方法"
date: 2022-02-27T20:37:51+08:00
draft: true
---

## 1. 创建c文件

```c
#include "python.h"
#include "stdio.h"

static PyObject *testmod(PyObject *self, PyObject *args) {
  printf("this is c function 'testmod'\n");
  return PyLong_FromLong(0);
}

static PyMethodDef mymod_funcs[] = {
  {"testmod", testmod, METH_NOARGS, "testmod function."},
  {0,0,0,0}
};

static PyModuleDef mymod_module = {
  PyModuleDef_HEAD_INIT,
  "mymod",
  "mymod is first module test",
  -1,
  mymod_funcs
};

PyMODINIT_FUNC PyInit_mymod(void) {
  printf("PyInit_mymod\n");
  return PyModule_Create(&mymod_module);
}
```

## 2. 创建setup.py

```python
from distutils.core import *
setup(
    name="mymod",
    version="1.0",
    ext_modules=[Extension("mymod", ["mymod.c"])]
    )
```

## 3. 生成扩展

```
python setup.py build
```

## 4. 测试扩展

生成的扩展在build文件夹里，名字可能是这样的 `mymod.cp39-win_amd64.pyd`
但没关系，直接用下面的代码就可以测试：

```python
>>> import mymod
PyInit_mymod
>>> dir(mymod)
['__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'testmod']
>>> mymod.testmod()
this is c function 'testmod'
0
```
