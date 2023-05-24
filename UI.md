# User Interface (UI)
This file is intended to provide in-depth details about the UI component (written in React) of an extension. Specifically, this file is intended to provide background and details about why things are the way they are.

## Table of Contents
- [Functions and Callbacks](#funcesion-and-callbacks)
    - [`runScript`](#runscript-execute-python)
    - [`writeLine`](#writeline-python-message-callback)
    - [`writeUpdateLine`](#writeupdateline-python-message-update-callback)
- [Rendering](#rendering)
    - [Available Component Properties](#available-component-properties)
        - [Users](#users)
- [Bundling](#bundling)
    - [Process of Bundling](#proces-of-bundling)
- [Development Mode](#development-mode)

## Funcesion and Callbacks
There are several functions and callbacks to be aware of when creating the extension's UI:

- [`runScript` (execute Python code)](#runscript-execute-python)
- [`writeLine` (message recieved from Python)](#writeline-python-message-callback)
- [`writeUpdateLine` (message recieved from Python but intended as an update to an already sent message)](#writeupdateline-python-message-update-callback)

### `runScript` (Execute Python)
To actually invoke you're extension's python code, within your component you'd want to call `window.python.invoke.runScript(<REACT CALLBACK NAME>, <REACT CALLBACK ARG>...)` replacing `<REACT CALLBACK NAME>` with the appropraite value defined in the `exnteions.json` metadata file. You'd replace `<REACT CALLBACK ARG>` with any arguments you'd like to provide to the callback (which are available in the API layer, keyed to the names, in order, provided in the `extension.json` file).

### `writeLine` (Python Message Callback)
To have actions happen upon reciept of a certain messages (within the UI/React) you would define a callback function and provide it to `window.python.on.writeLine(<CALLBACK FUNCTION>)` where `<CALLBACK FUNCTION>` can be any form of callable function (arrow functionm, defined function, etc...) and it's parameters would be the event and a line of text (string).

### `writeUpdateLine` (Python Message Update Callback)
This function is increadibly similar to `writeLine`. However, is intended to have a more generic function prototype in terms of allowing the provision of more than just text of a message this is because the actual interpretation of text can be done in the API layer and then wha'ts provided back to React can be some other kind of data.

A good example of this is a progress bar. Where because of the Python implemenation, the Python could can only write a new line to `stdout` however in reality we on't want to have to interprete every update line within React (we'd rather do this in the API layer). So, within the API layer we interprete the line of text from Python parse it and then provide some other kind of data structure that React can then just take and use easily (likely a JSON object).

## Rendering
It may seem somewhat counter intuitive to start with rendering but there is a very importnat aspect of rendering that explains a lot of the subsequent decisions. In particular, one of the biggest challanges for extension integration was that React doesn't like having components that were compiled with other versions of React. Moreover, re-compiling components, particularly because the main app gets bundled, was an undesirable solution. 

So, instead the solution adopted is, that extension authors are responsible to export the ReactDOM client instance that's used when compiling the component and then within the main app we load this instance and call the `createRoot` method on it to create a second root for the extension. Thus, we end up treating the extension's UI component almost like it's, it's own React Single Page App (SPA). Which has the side benefit of making it eaiser to test for extension authors.

One drawback of this approach is that shared state like the ability to use `useContext` or similar are lost between the main app and the extension (though you can use it for each other within each other). So, to get around this we pass shared state in as properties to the component (see the [Available Component Properties](#available-component-properties) below for details).

Another esult of this approach is that extension authors can feel free to be able to have multiple seperated out components that all culminater in the extension's "main" component. However, this also means handling more than a single file, and while possible, it became messy and as such the decision to move towards bundling was made (which is explored in more details in the [Bundling section](#bundling) later on in this doucment).

### Available Component Properties
Because the same set of properties are passed to every extension the set of properties that are passed to the extension, at least at time of writing, are relatively limited.

The below is a complete list of the availble properties:

- [Users](#users)

#### Users
The users property is an array of user's identities (that is an object with a `type` and `user` property) that represents the identies of the user that's currently logged in. The reason for the abstraction of user identities is that the user may be logged into multiple accounts at once giving access to multiple services etc...

## Bundling
Bundling is something we use and allow for because it makes dealing with seperated out React components easier. To that end though, there are very specific things that we require from the bundling. In particular, it's expected that bundling is done through [Browserify](https://browserify.org/). This is because certain information is parsed from the resulting bundle file and if other bundlers are used there is no guarentee for this file to be in the same format.

For the purposes of [the template/sample repository](https://github.com/AlanBridgeman/VAP-E_Extensions_Template) we use this in combintation with [Gulp](https://gulpjs.com/) to give extension authors an easy to use build script for creating this bundled file that matches expectations.

### Proces of Bundling
To the point of having specific requirements about bundling there is also a specific process that is recommended to be followed for bundling (evne though it may not be the most efficient or best). The process goes as follows:

1. Compile typescript (if applicable)
2. Compile React (using [`@babel/standalone`](https://babeljs.io/docs/babel-standalone) - Mostly to be consistent with [Development Mode](#development-mode))
3. Bundle (using [Browserify](https://browserify.org/))

But also within this process within the app's code and in the gulp code of [the template/sample repository](https://github.com/AlanBridgeman/VAP-E_Extensions_Template) we are very speicifc that we add files for bundling in the order they appear in the `extnesion.json` metadata file.

## Development Mode
While not currently available and not likely to be availble for a little while. If you run the VAP-E app in development mode there are specific features such as real-time transpilation of JSX code for components so that extension authors would be able to view their extension's UI in context before needinging to bundle or similar.