# A love message

[Try it out](https://cosmicnyx.github.io/web/index.html)

## Table of Contents

- [Concept](#concept)
- [Idea](#ideas)
- [Relation to Digital Futures](#relation-to-df)
- [Future hopes](#future-hopes)
- [Files Overview](#files-overview)
- [Key Functionalities](#key-functionalities)
- [Usage](#usage)
- [Complex Terminal Idea](#complex-terminal-idea)
- [How I Did It](#how-i-did-it)


## Concept
This concept explores visual and interactive storytelling through the aesthetics of terminals and files. We use pop-up windows as journal entries to showcase a story about the conflicts and inner turmoil about romantic relationships, and their views on being aromantic and ace. The interactive part comes from unexpected moments through reading the diary and the character's feelings. Unexpected popups, Java animations, exploring through files and entries.

## Ideas
I chose to work on a web-based project because I like building them. In my hurry to think of something, I began coding some features as I considered the narrative. The designathon offered me the opportunity to unwind, code something random, and create the basis for next projects.

The project's purpose was to design a desktop interface with interactive windows. Cleaning up the code took some time, and I still have files from previous attempts. My primary goal was to make the code as efficient and feasible as possible. I've always liked this look and have worked on similar projects before.

## Relation to DF
As a student in the Digital Futures program at OCAD University, this project serves as a practical exploration of the concepts and techniques I've been learning. Here’s a simpler take on how it relates:

Working on this project allowed me to dive deep into what we've been discussing in the Digital Futures program. It’s all about blending creativity with technology, and that’s exactly what I aimed for by creating a web-based interactive desktop. This project was a chance for me to apply coding skills, design thinking, and digital innovation in a fun and practical way. I experimented with making interactive windows, something that combines art and tech in a way that’s both useful and visually appealing. It was also a great exercise in problem-solving and efficiency, trying to make everything work smoothly and look good. Essentially, this project is a snapshot of what the Digital Futures program encourages us to do: think creatively about digital technology and how it can be used to create something unique and engaging.

## Future hopes
- Adding events that are triggered based on where the mouse moves or how long it stays still.
- In general, just adding more interactions.
- Choices of where the user explores that lead to certain discoveries and a story route.

## Files Overview

- `index.html`: The main HTML document that sets up the web page structure, including references to CSS stylesheets for styling and JavaScript files for functionality.

- `styles.css`: Contains the styling rules for the web application, focusing on a minimalist and tech-oriented aesthetic, with specific styles for the body, container, and terminal elements.

- `bkgr.js`: Implements functionality related to mouse movement detection, enhancing the interactive aspect of the application by responding to user actions.

- `main.js`: Sets up a terminal interface using the WinBox library, allowing users to enter commands and interact with the application.

- `winboxContent.js`: Defines content that can be dynamically displayed within WinBox instances, adding interactive storytelling or informational components to the application.

## Key Functionalities

- **Initialization**: Listens for the `DOMContentLoaded` event to ensure the HTML is fully loaded before executing the script.
- **WinBox Terminal Creation**: Utilizes the `WinBox` constructor to create a new window that acts as a terminal. This window is customizable and includes an input field for command entry.
- **Command Input Handling**: Integrates an input field within the terminal for user commands, which can be extended to support various commands and interactions within the application.
- **Styling and Positioning**: Applies specific CSS styles to the WinBox terminal for consistency with the application's aesthetic and user interface design.

## Usage

Users interact with the `main.js` functionality primarily through the terminal interface it creates. Upon entering commands into the terminal's input field, `main.js` can process these inputs to perform actions, such as opening new WinBox instances with content, changing application states, or triggering animations.


## Complex terminal idea

I wanted to make a terminal that worked as an actual terminal would.
I wrote this on the side but just as something to ref off of in the actual project js file. I decided not to due to time constraints and also for the sake of my sanity.

```javascript
document.addEventListener('DOMContentLoaded', () => {
    const terminal = document.getElementById('terminal');
    const inputField = document.getElementById('terminalInput');

    // Virtual file system
    let currentDirectory = '/';
    const fileSystem = {
        '/': {
            type: 'dir',
            content: ['folder1', 'folder2', 'file1.txt', 'file2.txt']
        },
        '/folder1': {
            type: 'dir',
            content: ['subfolder1', 'subfolder2', 'file3.txt']
        },
        '/folder2': {
            type: 'dir',
            content: []
        },
        '/folder1/subfolder1': {
            type: 'dir',
            content: ['file4.txt']
        },
        '/folder1/subfolder2': {
            type: 'dir',
            content: []
        },
        '/folder1/subfolder1/file4.txt': {
            type: 'file',
            content: 'This is the content of file4.txt.'
        },
        '/file1.txt': {
            type: 'file',
            content: 'This is the content of file1.txt.'
        },
        '/file2.txt': {
            type: 'file',
            content: 'This is the content of file2.txt.'
        },
        '/folder1/file3.txt': {
            type: 'file',
            content: 'This is the content of file3.txt.'
        }
    };

    inputField.addEventListener('keydown', function(event) {
        if (event.key === 'Enter') {
            executeCommand(this.value.trim());
            this.value = ''; // Clear input field
        }
    });

    // Execute command function
    function executeCommand(command) {
        appendToTerminal('$ ' + command);

        const parts = command.split(' ');
        const cmd = parts[0];
        const args = parts.slice(1);

        switch(cmd) {
            case 'ls':
                listDirectory();
                break;
            case 'cd':
                changeDirectory(args[0]);
                break;
            case 'cat':
                readFile(args[0]);
                break;
            case 'mkdir':
                createDirectory(args[0]);
                break;
            case 'touch':
                createFile(args[0]);
                break;
            default:
                appendToTerminal('Command not found: ' + cmd);
        }
    }

    // List files in current directory
    function listDirectory() {
        const content = fileSystem[currentDirectory].content;
        appendToTerminal(content.join(' '));
    }

    // Change current directory
    function changeDirectory(path) {
        const newPath = resolvePath(path);
        if (newPath in fileSystem && fileSystem[newPath].type === 'dir') {
            currentDirectory = newPath;
            appendToTerminal('Changed directory to ' + currentDirectory);
        } else {
            appendToTerminal('Directory not found: ' + newPath);
        }
    }

    // Read file content
    function readFile(filename) {
        const filePath = resolvePath(filename);
        if (filePath in fileSystem && fileSystem[filePath].type === 'file') {
            appendToTerminal(fileSystem[filePath].content);
        } else {
            appendToTerminal('File not found: ' + filePath);
        }
    }

    // Create new directory
    function createDirectory(dirname) {
        const newPath = resolvePath(dirname);
        if (!(newPath in fileSystem)) {
            fileSystem[newPath] = { type: 'dir', content: [] };
            appendToTerminal('Created directory: ' + newPath);
        } else {
            appendToTerminal('Directory already exists: ' + newPath);
        }
    }

    // Create new file
    function createFile(filename) {
        const filePath = resolvePath(filename);
        if (!(filePath in fileSystem)) {
            fileSystem[filePath] = { type: 'file', content: '' };
            appendToTerminal('Created file: ' + filePath);
        } else {
            appendToTerminal('File already exists: ' + filePath);
        }
    }

    // Resolve path relative to current directory
    function resolvePath(path) {
        if (path.startsWith('/')) {
            return path;
        } else {
            return currentDirectory + '/' + path;
        }
    }

    // Function to append text to terminal
    function appendToTerminal(text) {
        terminal.innerHTML += '<div>' + text + '</div>';
        terminal.scrollTop = terminal.scrollHeight; // Scroll to bottom
    }
});
```

## How I did it
With pizza and cola. 
