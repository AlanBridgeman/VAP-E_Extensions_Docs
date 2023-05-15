# Extensions Guide
***IMPORTANT*** THIS IS CURRENTLY A WORKING DRAFT

An extension is broken into three parts
- React (UI)
- Node/JavScript (JavaScript)
- Python (Logic)

Each section below will provide more detail of each of these 

Additionally, each extension is required to provide a certain set 
of metadata

## Metadata (extension.json)
```json
{
    "name": "<EXTENSION NAME>",
    "id": "<EXTENSIION ID>",
    "icon": "<PATH/TO/ICON>" | ["<FONTAWESOME TYPE>", "<FONTAWESOME ICON NAME>"],
    "react": "<REACT COMPONENT>",
    "react-callback": {
        "name": "<REACT CALLBACK NAME>",
        "args": [
            "<REACT CALLBACK PARAMETER>", 
            ...
        ]
    },
    "js": "<JAVASCRIPT FILE>",
    "python": {
        "file": "<PYTHON FILE>",
        "class": "<PYTHON CLASS NAME>",
        "package": [
            "<PYTHON FILE OR DIRECTORY TO INCLUDE>"
        ]
    },
    "command": "<TRIGGER COMMAND>"
}
```

| Name                                 | Description                                                  |
|---------------------------------------|--------------------------------------------------------------|
| `EXTENSION NAME`                      | The extension's name mostly used for options in settings     |
| `EXTENSIION ID`                       | THe extension's ID used internally (must be unique)          |
| `PATH/TO/ICON`                        | If a string is provided for the icon it's assumed a filepath |
[ `FONTAWESOME TYPE`                    | The type of Font Awesome icon (ex. Duo Tone=fad, Solid=fas)  |
| `FONTAWESOME ICON NAME`               | The name of the icon from Font Awesome to use                |
| `REACT COMPONENT`                     | The React component file should match React component's name |
| `REACT CALLBACK NAME`                 | The React callback name (window.python.`<NAME>`) to trigger  |
| `REACT CALLBACK PARAMETER`            | Callback parameter to correlate use in JavaScript interface  |
| `JAVASCRIPT FILE`                     | The JavaScript file defining the Python to React interface   |
| `PYTHON FILE`                         | The Python file that contains the main extension entry class |
| `PYTHON CLASS NAME`                   | The extension's main entry Python class name                 |
| `PYTHON FILE OR DIRECTORY TO INCLUDE` | Any folder or Python file for the extension's "package"      |
| `TRIGGER COMMAND`                     | The trigger command for Python                               |

## React (UI)
This is the front-end UI to show as a React component. This is integrated directly into the application as a panel.

## JavaScript (Communications)
As probably the least intuitive of the three the JavaScript 
component of an extension is intended to define the interface 
between the front-end UI (React) and the backend business logic (
Python) That is, what prompts from one trigger actions from the 
other.

That is it essentially works like an asynchrnous event listener something similar to `.on('message' fn)` for anyone familiar with that mechanism.

As an example for a splitting by language example you might have something like:
```JavaScript
const responses = {
    'Language to support (Leave blank to continue):': post_languages,
    'Output filename for English:': english_filename_callback,
    'Output filename for French:': french_filename_callback,
    'Video Filename (Leave blank to continue):': post_videos,
    'Force new instructions? (y/n):': force_new_instructions,
    'Chunk duration (in seconds. Default is 10 leave blank to use):': chunk_duration,
    'Instructions filename (leave blank to use default):': instructions_filename,
    'Video chunks directory name (leave blank to use default):': video_chunks_dir,
    'Chunk prefix (leave blank to use default):': chunk_prefix,
    'Chunks directory suffix (leave blank to use default):': chunks_dir_suffix,
    'Output Filename:': output,
    'Processing Complete!': onComplete,
    // Check to see if we're updating a line (ex. progress bar) or have text to output
    '^\\r': onUpdateLine,
    _default: onDefault
}

module.exports = {
    '<TRIGGER COMMAND>': responses
};
```

Replacing `<TRIGGER COMMAND>` with the same value used in the extension.json file.

Where each callback looks something like
```JavaScript
/**
 * Respond to a Python message
 * 
 * @param {string} message The message received from python
 * @param {PythonShell} pyshell The python shell to use to send messages
 * @param {{[key: string]: any}} options The options provided (usually from the user via the GUI and set as react-callback args in extension.json)
 * @param {string[]} python_output The output from python so far
 * @param {BrowserWindow} window The GUI's window 
 * @param {CallableFunction} resolve Resolve the python runner promise
 * @param {CallableFunction} reject Reject the python runner promise
 */
function chunks_dir_suffix(
    message, 
    pyshell, 
    options, 
    python_output, 
    window, 
    resolve, 
    reject
) { 
    pyshell.send('<MESSAGE>');
}
```

Where you'd replace `<MESSAGE>` with  whatever you'd want to send to your Python code.

## Python (Logic)
This provides the backend logic of what to do. This should be formatted in a way that it works like a CLI where you either put output to `stdout` and you get input from `stdin`

### Trigger Command
The trigger command is a word or short sentence that turns control from the Python code running in the core module over to the extension's code in a terminal you'd see the prompt "What would you like to do: "

### Extension's "package"
The purpose of the "package" in the Python is so that there is an easy way to reference other Python code. You do this by importing from `extensions.<Extension ID>.<File or Directory...>`