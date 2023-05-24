# Extensions Guide
***IMPORTANT*** THIS IS CURRENTLY A WORKING DRAFT

## Table of Contents
- [Introduction](#introduction)
    - [What is a VAP-E extension?](#what-is-a-vap-e-extension)
    - [Why does VAP-E need extensions?](#why-does-vap-e-need-extnesions)
    - [Why would you want to create a VAP-E extension?](#why-would-you-want-to-create-a-vap-e-extension)
        - [Developing a feature you want](#developing-a-feature-you-want)
        - [Using VAP-E extnesions as a way to learn and grow](#using-vap-e-extnesions-as-a-way-to-learn-and-grow)
        - [Innovating on Accessibility](#innovating-on-accessibility)
- [Anatomy of an extension](#anatomy-of-an-extension)
    - [Metadata (extension.json)](#metadata-extensionjson)
    - [UI (Using React)](#ui-using-react)
    - [Logic (Using Python)](#logic-using-python)
        - [Trigger Command](#trigger-command)
    - [API (Using Node/JavaScript)](#api-using-nodejavascript)
- [Example](#example)
    - [Step 1: Download the template/sample repository](#step-1-download-the-templatesample-repository)
    - [Step 2: Edit `extension.json` (the metadata)](#step-2-edit-extensionjson-the-metadata)
    - [Step 3: Create our UI](#step-3-create-our-ui)
    - [Step 4: Write our backend logic](#step-4-write-our-backend-logic)
    - [Step 5: Write the exchange](#step-5-write-the-exchange)
    - [Step 6: Build](#step-6-build)
    - [Review](#review)

## Introduction
This secion provides an intorduction into VAP-E extensions in terms of:

- [What an extension is](#what-is-an-vap-e-extension)
- [Why the extension mechanism exists](#why-does-vap-e-need-extnesion)
- [Why you might write an extnesion](#why-would-you-want-to-create-a-vap-e-extension)

That is, this section is intended to give you a generic overview and provide answers for basic questiosn around what and why. The remainder of the document dives into how (and we don't really care about who, when or where).

### What is a VAP-E extension?
A VAP-E (or Video Automation Platform - Editor component) extension is a package of code that allows the adding of additional "features" to the app. This means over time it can become increasingly more feature rich and/or potentially more useful or easier to use.

### WHy does VAP-E need extnesions?
One of the core values of the VAP-E app is to be a "non-visual video editor". That is, it's a purposefully built tool that can be usable for everyone regardless of abilities. Unfortunately, to acheive that goal there is signifcant work around finding unique mechanisms by which to be able to do the editing. To that extent, the idea of making the app extendable is to allow others, that may have unique persepctives or ideas, to be able to contribute those in the hopes of continually improving the product and making it better and easier for everyone.

### Why would you want to create a VAP-E extension?
The reasons for why you might want to create a VAP-E extension may vary of course. But generally, they fall into a few categories:

- [You want a feature that currently doesn't exist and are motivate enough to create it](#developing-a-feature-you-want)
- [You want to make something but don't want to start from nothing](#using-vap-e-extnesion-as-a-way-to-learn-and-grow)
- [You have an idea about making video editing more accessible](#innovating-on-accessibility)

#### Developing a feature you want
The first generic category of reasons why you might develop a VAP-E extesnsion is if there is a feature you want but it doesn't currently exist. The good news is, for people that fall into this category, is that your generally already familiar with the VAP-E app and it's existing extension mechansims. This will help when you try to dive into the code and will help you understand why certain things are the way they are.

#### Using VAP-E extnesions as a way to learn and grow
VAP-E unlike a lot of other platforms that allow you to extned them, requires that you get to know at least two different programming languages (Python and JavaScript/Typescript). Which can be a disadvantage or an advantage depending on your perspective. The VAP-E team likes to think of this as an advantage because it provies more people with more exposure and allows for certain amounts of experimentation beyond what some other platforms let you do. 

To address the other side, this does create some barriers for people that may be brand new to programming or similar. However, the languages and frameworks used were, at least in part, intentioanly picked because their popular and have a huge amount of resources and support in the community.

There is a hope, once past the initial few versions, to create mechanisms to monitize extensions and their use. However, this requires certain infrastruture investments that would only make sense if there was enough of an ecosystem to support it.

#### Innovating on Accessibility
It can be tough when inspiration hits you and you have an idea about how to make things better. But then to have to stop, slow down and build up all these little details to eventually get to a place where you can implement your idea. One of the hopes of VAP-E is to take away some of these barriers at leas in the specific realm of video eidting.

## Anatomy of an extension
An extension has four basic parts:

- [Metadata (extension.json)](#metadata-extensionjson)
- [UI (Using React)](#ui-using-react)
- [Logic (Using Python)](#logic-using-python)
- [API (Using Node/JavScript)](#api-using-nodejavascript)

This structure was choosen because it provies what we, as in the VAP-E team, believe is a balenced approach between structured and unstructured, easy but flexible, familiar but innovative, etc...

In the proceeding sections each component will be described in more detail.

### Metadata (extension.json)
The `extension.json` is a JSON file that provides metadata about the extension. This file is required to be in the root of a VAP-E extension and is treated as the source of truth about aspects of the extension including things like relavent files, names, IDs, etc..

The below provies a generic idea of what a `extension.json` file might look like. Optional fields have question mark (?) at the end of their name.

```json
{
    "name": "<EXTENSION NAME>",
    "id": "<EXTENSIION ID>",
    "icon": "<PATH/TO/ICON>" | ["<FONTAWESOME TYPE>", "<FONTAWESOME ICON NAME>"],
    "react": [ "<REACT MAIN COMPONENT>", "<SECONDARY REACT COMPONENT>", ...],
    "react-callback": {
        "name": "<REACT CALLBACK NAME>",
        "args": [
            "<REACT CALLBACK PARAMETER NAME>", 
            ...
        ]
    },
    "api": "<JAVASCRIPT FILE>",
    "python?": "<PYTHON (.py) FILE>" | "<PYTHON FOLDER>" | {
        "file?": "<PYTHON (.py) FILE>",
        "folder?": "<PYTHON FOLDER>"
    },
    "command": "<TRIGGER COMMAND>"
}
```

The table below provides some detail about what these values should be.

| Name                                  | Description                                                            |
|---------------------------------------|------------------------------------------------------------------------|
| `EXTENSION NAME`                      | The extension's name mostly used for options in settings               |
| `EXTENSIION ID`                       | THe extension's ID used internally (must be unique)                    |
| `PATH/TO/ICON`                        | If a string is provided for the icon it's assumed a filepath           |
| `FONTAWESOME TYPE`                    | The type of Font Awesome icon (ex. Duo Tone=fad, Solid=fas)            |
| `FONTAWESOME ICON NAME`               | The name of the icon from Font Awesome to use                          |
| `REACT MAIN COMPONENT`                | The main React component file (the component that will be rendered)    |
| `SECONDARY REACT COMPONENT`           | Any secondary React components needed to render the main component     |
| `REACT CALLBACK NAME`                 | The React callback name used to trigger the python code (from React)   |
| `REACT CALLBACK PARAMETER NAME`       | Callback parameter names to correlate for the API layer                |
| `JAVASCRIPT FILE`                     | The JavaScript file defining the API (Python to React exchange)        |
| `PYTHON FILE`                         | The Python file that contains the main extension class                 |
| `PYTHON FOLDER`                       | A folder with Python files which make up the extension's Python module |
| `TRIGGER COMMAND`                     | The trigger command for Python (used to "take" control from core code) |

### UI (Using React)
For the User Interface (UI), or Graphcial User Interface (GUI) more specifically, we use React components to make it easy to integrate the extension's component into the main app itself. But even more than that, this was an intentional choice to try to make it easy for extension authors to be able to bring in their own sets of React components to be able to utilize what already exists out there or that they may have already created.

While most of the UI details can be found in [UI.md](./UI.md) for those specifically interested, there are a few points that should be made here as their needed for the extension to work. 

First, is that the first file/component in the `react` property of the `extension.json` metadata file is treated as the "main" component. This means, this is the component that is loaded and rendered when the user clicks on the icon (also set in the `extnesion.json` metadata file) in the side bar.

Second, within the main component along with exporting the component itself (as usual for a React component) extension authors ***MUST*** export the ReactDOM client instance.

### Logic (Using Python)
The logic portion of the app (written in Python) provides the "brains" of the extension or the "backend logic" if you'd like that terminology. This is intentionally very open ended and allows for a wide variety of implementations in terms of the logic (because putting constraints on the logic would likely put constraints on the extension's functionality).

The idea is to write this backend logic as though it was a Command Line Interface (CLI) where you put output to `stdout` and you get input from `stdin`. While this may seem confining at first, a critical thing to remember is what you stream to stdout doesn't have to be human readable and vice versa.

Your extension is required to extend the `BaseExtension` class provided in the vape_base_extension python module available on PyPi. You can install it using pip using the following command:

```sh
pip install vape_base_extension
```

This subclass is where the extension starts. For more details about the specific functions and implementation it can be found in [LOGIC.md](./LOGIC.md).

#### Trigger Command
The trigger command is a word or short sentence that turns control from the main app's Python code over to the extension's code.

This is the only exchange that you aren't required to code as part of your [API/exhcange layer](#api-using-nodejavascript) (discussed later).

You should try to pick something that is logical/consistant with the functionality of your extension. For instance, an app that split up videos by language might have a trigger command of "Split By Language".

### API (Using Node/JavaScript)
As probably the least intuitive part of an extension the API/exchange layer (written in Node/JavaScript) provies the definition for the interface between the [backend logic](#logic-using-python) and the [UI](#ui-using-react). That is, what prompts from one trigger actions from the other.

This is needed because with the flexibility that we afford to extension authors they can have UI components and backend logic that can expect almost any type of input and can produce almost any type of output and without knowing how to map between the two there wouldn't be any ability for them to communicate.

See [the example below](#example) for a breakdown that may make this easier to understand or [API.md](./API.md) for more dtails.

## Example
To help illustrate how to create an extension we're going to walk through it. The steps we'll follow are:

1. [Download the template/sample repository](#step-1-download-the-templatesample-repository)
2. [Edit `extension.json` (the metadata)](#step-2-edit-extensionjson-the-metadata)
3. [Create our UI](#step-3-create-our-ui)
4. [Write our backend logic](#step-4-write-our-backend-logic)
5. [Write the exchange](#step-5-write-the-exchange)
6. [Build](#step-6-build)

### Step 1: Download the template/sample repository
The best way to start writing a VAP-E extension is to clone the template/sample repository. To do this run the following:

```sh
git clone https://github.com/AlanBridgeman/VAP-E_Extensions_Template example_extension
```

Though feel free to replace `example_extension` with whatever you want the folder to be called (It's probably easiest if this is the same or similar to the extension's id though not required).

### Step 2: Edit `extension.json` (the metadata)
If we go inside the newly downloaded template/sample repository (keep in mind minde, the example one is called `example_extension`) and print the contents of the `extension.json` file using something similar to:

```sh
cd example_extension
cat extension.json
```

we'd get:

```json
{
    "name": "<extension name>",
    "id": "<uniue extension id>",
    "icon": ["<icon type>", "<icon name>"],
    "react": ["<first component>.tsx", "<second component>.jsx", ...],
    "react-callback": {
        "name": "<callback name>",
        "args": [
            "<first argument>",
            "<second argument>",
            ...
        ]
    },
    "api": "<js file>",
    "python": {
        "file": "<python file>",
        "class": "<python class>",
    },
    "command": "<command/trigger phrase>",
}
```

Admittedly, if you've read through the other sections of this guide you may have seen something very similar to this already 😉. But for now, let's simplify this and replace some of the easy things we already know. The `extension.json` would become:

```json
{
    "name": "Example Extension",
    "id": "example_extension",
    "icon": ["fad", "hand-wave"],
}
```

In case your curious, I just searched Google and the Font Awesome pages for the icon (you can see more detail on [this Font Awesome page](https://fontawesome.com/icons/hand-wave?f=classic&s=duotone) if you'd like).

Hopefully, this is way less intimidating but let's save this and move on to the next part.

### Step 3: Create our UI
Now, this isn't really meant to be a React tutorial so I'm going to skip a few steps and give you the UI's code (I'd recommend reading reading up on React or the [UI.md](./UI.md) document for more info on the custom stuff).

```jsx
import React, { useState, useRef } from 'react';
import ReactDOM from 'react-dom/client';

// YOU MUST HAVE THE FOLLOWING LINE!
export const reactDOM = ReactDOM;

function ExampleUI() {
    const [name, setName] = useState('');
    const ref = useRef(null);

    function handleClick(evt) {
        evt.preventDefault();

        window.python.invoke.runScript('example', ref.current.value);
    }

    window.python.on.wrtieLine(
        (evt, line) => {
            setName(line);
        }
    );

    return (
        <div>
            <p>Hi {name}</p>
            <input type="text" ref={ref} />
            <button onClick={handleClick}>Say Hello</button>
        </div>
    );
}

export default ExampleUI;
```

Keep in mind this is a pretty basic example that just changes who it's saying hi to based on text in a textbox. But I think it illustrates the basics. If we save this as `example.jsx` in the folder.

Before we move on, we do have to go back to the `extension.json` file and update it with a few thins.

```json
{
    "name": "Example Extension",
    "id": "example_extension",
    "icon": ["fad", "hand-wave"],
    "react": ["example.jsx"],
    "react-callback": {
        "name": "example",
        "args": [
            "name"
        ]
    }
}
```

The changes here probably already make sense in terms of the filename. But to point out the `name` and `args` under `react-callback` came from the `window.python.invoke.runScript('example', ref.current.value)` in the JSX code. Where the react callback's name is `example` because that's the first parameter, and in terms of args the first parameter to our callback we want to call `name` (this will come back and make more sense in [Step 5](#step-5-write-the-exchange))

### Step 4: Write our backend logic
It's not actually required but it's recommended that you create a virtual environment and install the vape_base_extension package this mostly just means you won't get complaints from any kind of editor etc... (also note, for these commands, I'm assuming Mac or Linux)

```sh
echo "vape_base_extension" > requirements.txt
python3 -m venv .venv
. .venv/bin/activate
pip install -r requirements.txt
```

and then our actual Python code, again this isn't really a Python tutorial so I'll skip a lot of the explination and just give you the code (see [LOGIC.md](./LOGIC.md) for more details)

```python
from vape_base_extension import BaseExtension

class ExampleExtension(BaseExtension):
    def __init__(self, name, debug_mode=False):
        super(debug_mode)

        self.name = name
    
    def run(self):
        print(f'{self.name} the fabulous')
    
    def finalize(self):
        pass
    
    @classmethod
    def create_from_user_input(cls, debug_mode=False):
        name = input('Name: ')
        cls(name, debug_mode)
```

This can then be saved as `example.py` and we can update the `extension.json` with new metadata:

```json
{
    "name": "Example Extension",
    "id": "example_extension",
    "icon": ["fad", "hand-wave"],
    "react": ["example.jsx"],
    "react-callback": {
        "name": "example",
        "args": [
            "name"
        ]
    },
    "python": "example.py"
}
```

Though the `python` section of `extension.json` may look intimedating at first if you have a simple script like the one written here it is as simple as that.

### Step 5: Write the exchange
This is the step where things get a bit more interesting because we need to define the API. To start let's create a simple `api.js` file that just sets up our exports (which should be familiar for anyone that's worked with Node/JavaScript) and put the following content:

```js
module.exports = {
    'Run Example': responses
}
```

It might seem odd but it's actually a good idea here to switch back to the `extension.json` file and add two new things:

```json
{
    "name": "Example Extension",
    "id": "example_extension",
    "icon": ["fad", "hand-wave"],
    "react": ["example.jsx"],
    "react-callback": {
        "name": "example",
        "args": [
            "name"
        ]
    },
    "python": "example.py",
    "api": "api.js",
    "command": "Run Example"
}
```

You'll notice the `api` is now set to the filename (which is pretty self-explanatory) but more importantly here is the `command` and notice how this matches the "first level" of the `module.exports` from our API file.

Getting back to the `api.js` file let's define our "second level" of `module.exports`:

```js
const responses = {
    'Name:': sendName
};

module.exports = {
    'Run Example': responses
}
```

The key here is the `Name:` as a key in the `responses` object is from the `input('Name: ')` we had in the python script (also noting we strip white space). That is when Python script hits the `input('Name: ')` the function sendName (which we haven't defined yet, but will in a moment) will be called.

So, let's define `sendName`:

```js
/**
 * Send the name (we got from React) to Python (on stdin)
 * 
 * @param {string} message The message received from python
 * @param {PythonShell} pyshell The python shell to use to send messages
 * @param {{[key: string]: any}} options The options provided (usually from the user via the GUI and set as react-callback args in extension.json)
 * @param {string[]} python_output The output from python so far
 * @param {BrowserWindow} window The GUI's window 
 * @param {CallableFunction} resolve Resolve the python runner promise
 * @param {CallableFunction} reject Reject the python runner promise
 */
function sendName(
    message, 
    pyshell, 
    options, 
    python_output, 
    window, 
    resolve, 
    reject
) { 
    pyshell.send(options['name']);
}
```

Admittedly, the big comment block above the function you may not need (it's here so that it's easy to know what parameters are what, because there are quite a few). But the particularly important line is: `pyshell.send(options['name'])`. To break this down, the `pyshell.send` puts whatever text we send it on `stdin` for the Python code to ingest. Next, the `options['name']` comes from, if you remember in [Step 3](#step-3-create-our-ui), we set the first element in the `args` property under `react-callback` as `name`. Well, this is where that comes back. And remember when we called `window.python.invoke.runScript('example', ref.current.value)`. Well, every argument after the first one (because the first one is the `react-callback`'s `name` as explained already) are first mapped to an object by index `{1: ref.current.value}` and then we take the `react-callback`'s `args` and do the same `{1: name}` and then combine them to get something that looks like `{name: ref.current.value}` which we then call `options` as a parameter to the function.

Now, all together we have

```js
function sendName(
    message, 
    pyshell, 
    options, 
    python_output, 
    window, 
    resolve, 
    reject
) { 
    pyshell.send(options['name']);
}

const responses = {
    'Name:': sendName
};

module.exports = {
    'Run Example': responses
}
```

Okay, great we have an extension that communicates from React to Python. However, we can't send data back from Python to React (other than returning though we're technically ignoring that in this example, and probably not as one might expect anyway).

So, there are a few different ways to do this part and it'll really depend on the extension your writing. The two main ways are:

1. Target the message: We could do this by prepending or appending some text and using a regex with parsing in the JavaScript (this is good if we want to be specific)
2. Catch All: We can set a "catch all" default to get any messages from Python

So, because of the simplicity of our example (and to be lazy) we're going to do the second choice. To do this, we'll start by defining an `onDefault` function that calls the `writeLine` method that there is a callback for in React:

```js
function onDefault(
    message, 
    pyshell, 
    options, 
    python_output, 
    window, 
    resolve, 
    reject
) { 
    window.webContents.send('writeLine', message);
}
```

Here, we use the `window` parameter to call the `writeLine` function (which calls the callback).

But from there, we set this as `_default` in the `responses` object and we have our finished file:

```js
function sendName(
    message, 
    pyshell, 
    options, 
    python_output, 
    window, 
    resolve, 
    reject
) { 
    pyshell.send(options['name']);
}

function onDefault(
    message, 
    pyshell, 
    options, 
    python_output, 
    window, 
    resolve, 
    reject
) { 
    window.webContents.send('writeLine', message);
}

const responses = {
    'Name:': sendName,
    '_default': onDefault
};

module.exports = {
    'Run Example': responses
}
```

As you can probably tell this file can get quite lengthy quite fast but that means we're basically done with this `api.js` file.

### Step 6: Build
Because we started from the template/sample repostiroy this step is super easy.

```sh
yarn build
```

This should produce a dist folder with a `bundle.js` file in it.

And... DONE!

### Review
If you followed along with the example, you're extension file structure would probably look something similar to (admittedly, you'll probably have extra files because of the template/sample repository)
```
├── api.js
├── dist
│   └── bundle.js
├── example.jsx
├── example.py
├── extension.json
└── requirements.txt
```

The `api.js`:
```js
function sendName(
    message, 
    pyshell, 
    options, 
    python_output, 
    window, 
    resolve, 
    reject
) { 
    pyshell.send(options['name']);
}

function onDefault(
    message, 
    pyshell, 
    options, 
    python_output, 
    window, 
    resolve, 
    reject
) { 
    window.webContents.send('writeLine', message);
}

const responses = {
    'Name:': sendName,
    '_default': onDefault
};

module.exports = {
    'Run Example': responses
}
```

The `example.py`:
```py
from vape_base_extension import BaseExtension

class ExampleExtension(BaseExtension):
    def __init__(self, name, debug_mode=False):
        super(debug_mode)

        self.name = name
    
    def run(self):
        print(f'{self.name} the fabulous')
    
    def finalize(self):
        pass
    
    @classmethod
    def create_from_user_input(cls, debug_mode=False):
        name = input('Name: ')
        cls(name, debug_mode)
```

The `example.jsx`:
```jsx
import React, { useState, useRef } from 'react';
import ReactDOM from 'react-dom/client';

// YOU MUST HAVE THE FOLLOWING LINE!
export const reactDOM = ReactDOM;

function ExampleUI() {
    const [name, setName] = useState('');
    const ref = useRef(null);

    function handleClick(evt) {
        evt.preventDefault();

        window.python.invoke.runScript('example', ref.current.value);
    }

    window.python.on.wrtieLine(
        (evt, line) => {
            setName(line);
        }
    );

    return (
        <div>
            <p>Hi {name}</p>
            <input type="text" ref={ref} />
            <button onClick={handleClick}>Say Hello</button>
        </div>
    );
}

export default ExampleUI;
```

the `extension.json`:
```json
{
    "name": "Example Extension",
    "id": "example_extension",
    "icon": ["fad", "hand-wave"],
    "react": ["example.jsx"],
    "react-callback": {
        "name": "example",
        "args": [
            "name"
        ]
    },
    "api": "api.js",
    "python": "example.py",
    "command": "Run Example"
}
```