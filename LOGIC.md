# Backend Logic
The intention of this file is to provide details about the logic implementaitons for extnesions.

## Table of Contents
- [Configurations and File Naming](#configurations-and-file-naming)
    - [Python Configurations](#python-configurations)
        - [No Configuration](#no-configuration)
        - [Only a Folder Path](#only-a-folder-path)
        - [Only a File Path](#only-a-file-path)
        - [Dependencies](#dependencies)
    - [Trigger Command](#trigger-command)
- [Implementation](#implementation)
    - [`create_from_user_input`](#create_from_user_input)
    - [`run`](#run)
    - [`finalize`](#finalize)

## Configurations and File Naming
There are several different properties/configurations within `extension.json` that are relevant to the logic component of an extension in particular

- [Python Configurations](#python-configurations)
- [Trigger Command](#trigger-command)

### Python Configurations
The `python` configuration in the `extension.json` file is entirely optional as well as can take various forms. This section will attempt to run through those different forms based on what is provided:

- [No configurations](#no-configuration)
- [Only a folder path (as a string or part of an object)](#only-a-folder-path)
- [Only a file path (as a string or part of an object)](#only-a-file-path)

#### No Configuration
If no python configurations exist within the `extension.json` thn we look for a `python` folder that has a `__main__.py` or `__ini__.py` file in it. This approach was choosen as it approximates the native python module behaviour. Though, note we do, do it in that order in terms of `__main__.py` before `__init__.py`.

#### Only a Folder Path
If a folder path is provided, and yes we verify it exists, than similar to what's done with no configuration we look within the folder to try to find a `__main__.py` or a `__init__.py` and load the folder in as a module using one of those folder. 

*Note: this `__main__.py` and `__init__.py` file doen't have to be the extension class as long as they imports it. Because we search the entire module for it*

#### Only a File Path
If only the file path is provided (this can be relative or absolute) it's assumed that this is the `BaseExtension` subclass file.

For relative file paths this is assumed relative to the extension's main folder.

#### Dependencies
Tied in with how you define your python configurations is ihow external dependencies are handled. In particular in the folder (either explictly defined or sumized as part of he file handling) having a `reuirements.txt` in that folder will mean that these will be loaded.

### Trigger Command
In a terminal you'd see the prompt "What would you like to do: ")

## Implementation
All extensions ***MUST*** have a class that extends (subclass) of the abstract class `BaseExtension` found within the vape_base_extension python package available on PyPI.

The `BaseExtension` class has 3 abstract methods:
- [`create_from_user_input`](#create_from_user_input) (which is also a [classmethod](https://pythonbasics.org/classmethod/))
- [`run`](#run)
- [`finalize`](#finalize)

All of these represent "hooks" into the extnesion life cycle, where there are three major parts:

1. Creation/Instantiation (`create_from_user_input`)
2. Running/Execution (`run`)
3. Cleanup/Donconstruction (`finalize`)

In the following sections more detail will be provided on each of these functions.

### `create_from_user_input`
This function (which is a [classmethod](https://pythonbasics.org/classmethod/) and should return an instance of the extension class) functions as part of a [factory patter](https://en.wikipedia.org/wiki/Factory_method_pattern) used for creation/instantiation of the extension object. This is to provide a generic "hook" into this part of the extension's life cycle because it can be particularly important in conjunction with the `finalize` function. This is because, both `run` and `finalize` take no parameters and are dependent on class attributes/properites for any kind of shared state. Which by convention/best practice should be initially instantiated in the constructor.

The one extra parameter (in excess to the `cls` class parameter provided because its a [classmethod](https://pythonbasics.org/classmethod/) and should return an instance of the extension class) functions as part of a [factory patter](https://en.wikipedia.org/wiki/Factory_method_pattern)) is a debug_mode boolean parameter this is intended to indicate if the extension should be verbose and and generally give as much detail as possible when, for instance, errors occur.

### `run`
The run function which takes no arguments (other than `self` because it's a non-static class function) is intended to be the primary execution for the extension.

### `finalize`
The finalize function is intended to provide a "hook" into the final phase in the extension's lifecycle allowing the cleaning up of resources etc...
