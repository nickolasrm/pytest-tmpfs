# pytest-tmpfs
![GitHub Workflow Status](https://img.shields.io/github/workflow/status/nickolasrm/pytest-tmpfs/Build)
[![codecov](https://codecov.io/gh/nickolasrm/pytest-tmpfs/branch/main/graph/badge.svg?token=TErLn7eH5l)](https://codecov.io/gh/nickolasrm/pytest-tmpfs)

A pytest plugin that helps you on using a temporary filesystem for testing

```python
import json

def readjson(path: str) -> dict:
  """
  Example:
    >>> path = fs.write('a/b/c.json', '{"a": 2}')
    >>> readjson(path)
    {'a': 2}
  """
  with open(path, 'r') as f:
    return json.load(f)
```

This plugin is a fake filesystem manager implementation that relies on the `pytest`'s `tmp_path` fixture to enable safe testing of projects that require disk operations and are difficult to mock.

## Alternatives

- [pyfakefs](https://pypi.org/project/pyfakefs/): good alternative if you wish to mock filesystem operations.
- [tempfile](https://docs.python.org/3/library/tempfile.html): creates files and directories temporarily.

## Install

For installing this package, just type the command below in your terminal:

```
pip install pytest-tmpfs
```

## Usage

### As a fixture

You can use the `TmpFs` as a fixture by passing `tmpfs` as an argument of your test function.

```python
import pytest_tmpfs

def test_readjson(tmpfs: pytest_tmpfs.TmpFs):
    path = tmpfs.write('a/b/d.json', '{"a": 2}')
    assert readjson(path) == {'a': 2}
```

### In doctests

By installing this plugin, all doctests are automatically enabled to use `fs`, a variable that stores an instance of `TmpFs`.

```python
def fscheck(path: str) -> dict:
  """
  Example:
    >>> fs.touch('a/b/c.txt')
    >>> fs.ls('a/b')
    ['c.txt']
  """
  pass
```

### Your own instance

If you seek to use this plugin without pointing to `tmp_path` it is possible to create your own instance and to manipulate it the way you want.

```python
import pytest_tmpfs

def test_random():
  mypath = pathlib.Path('a/b/c')
  fs = TmpFs(mypath)
```

## Features

The following table contains all methods that `TmpFs` implements and its descriptions:

| Method | Description | Example | Parameters | Return |
|---|---|---|---|---|
| write | writes a file in the disk and adds line by line content to it | fs.write('a/b.txt', 'Hello', 'World!') | path, *lines | abs path to the file |
| read | reads the contents of a file and returns it as a string | fs.read('a/b.txt') | path | abs path to the file |
| ls | lists all files inside a folder | fs.ls('a') | path | sorted list of folders and files |
| rm | removes a file or a folder | fs.rm('a') | path | True if succeded, False if not |
| mkdir | creates one or more folders | fs.mkdir('b/c') | path | abs path to the folder |
| mv | moves a file or a folder to another path | fs.mv('a/b.txt', 'b/c') | path, path | abs path to destination |
| clean | removes all files inside the root of the tmp fs | fs.clean() |  |  |
| path | converts a relative path into an abs path | fs.path('a') | path | abs path to the file or folder |
| tree | prints a tree-like structure of a folder | fs.tree('a') | path |  |
| tree_format | same as tree, but outputs a string | fs.tree_format('a') | path | tree representation |
| cat | prints the content of a file | fs.cat('b/c/b.txt') | path |  |
| touch | creates an empty file | fs.touch('a/c.txt') | path | abs path to the file |
| tmp_cwd | changes CWD to the temporary filesystem folder | fs.tmp_cwd() |  | abs path to the fs root |
| cwd | returns to the original CWD | fs.cwd() |  | abs path to the old cwd |

> Note: `tmp_cwd` and `cwd` can be used with `with statements`.

## Contributing

Feel free to contribute to this project, just remember to use pre-commit and to write unit tests for new features/bugs.
