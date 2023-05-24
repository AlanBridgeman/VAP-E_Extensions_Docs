# API (communication exchange layer)
This file is intended to provide additional informtaion about how to create the API/exhcange layer of an extension.

## Table of Contents
- [What the API/Exchange layer is](#what-the-apiexchange-layer-is)
- [Exports](#exports)
    - [Exports Example](#exports-exmple)
- [Callback Functions](#callback-functions)

## What the API/Exchange layer is
As probably one of the least intuitive parts of an extension the API layer is intended to define the interface between the front-end UI (React) and the backend logic (Python). That is, what prompts from one trigger actions from the other.

That is, it essentially works like an asynchrnous event listener, something similar to `.on('message', fn)` for anyone familiar with that mechanism.

## Exports
The idea is for the API file to export a map that provides mappings between Python prompt/messages and callback functions that then take appropriate action.

In a generic way this may look something similar to:
```js
module.exports = {
    '<TRIGGER COMMAND>': {
        '<PYTHON PROMPT/MESSAGE>': <CALLBACK>
    }
}
```

Where `<TRIGGER COMMAND>` is replaced by the `command` value found in `extension.json`. And `<PYTHON PROMPT/MESSAGE>` is replaced by a string or regular expression that will trigger the `<CALLBACK>` when encoutered (coming out of Python).

### Exports Exmple
As an example for a splitting by language extension you might have something like:

```js
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
    'Split by language': responses
};
```

## Callback Functions
Each callback would look something like:

```js
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
function callback(
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