---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'traSH'
pubDate: '04.06.2026'
description: 'Fully functional shell inspired by Bash writen in C.'
source: 'https://github.com/KixiKcodes/traSH'
tags: ["C"]
---
## About
During my time at 42, I had the privilege of doing the original core projects. One of the most notorious, some might say infamous projects in the original core was **minishell**.

The premise is to write a fully functional shell program based on the original Bash functionality. This includes pretty much all terminal interaction but not scripting features (that would blow up the complexity immensely). Many consider it to be the true test where many students quit the curriculum. It was the first project that really had quite a lot of parts. I have very fond memories of working on it, even if it was a headache a lot of the time and I feel like I came out of it twice as good a programmer as before the project.

So now, over a year later, I got nostalgic and decided to fully rewrite minishell, now with my own ideas and deviating from the strict 42 formatting rules. The name _traSH_ is just a joke I came up with while looking through the original Bash source code and thinking to myself _"Man, mine is trash compared to bash"_.

## Features
- **Built-Ins:**
	- `cd` - Changes the current working directory. Takes relative or absolute paths as well as `~` and `-`.
	- `export` - Takes one or multiple arguments to add variables to the current environment. Any invalid syntax will be ignored and when ran with no arguments it will print an alphabetically sorted list of all existing environment variables including those with an empty value.
	- `unset` - Takes one or multiple arguments as variable names to remove from the current environment. Much like export, invalid syntax or variables are ignored. Running with no arguments will do nothing.
	- `exit` - Exits the shell. If ran with no arguments, it will exit the shell with the last status received from an execution. If ran with any number, it will exit with said number cast as an unsigned byte (0 - 255).
- **Variable Expansion and Quote Resolution:**
	Using `$` before a string will automatically replace it with its corresponding value if it is an existing environment variable. This works with double quotes as well, merging multiple strings as a single token and performing expansion, and works with single quotes doing the same but ignoring the expansion. The last exit status can also be accessed with `$?`.
    
![basic_test](/images/basic_test.png)
- **Signal Handling:**
	Multiple signals are handled and update the status accordingly. 
	- `Crtl+D` types a null prompt which exits the shell. It can also be used to exit a heredoc early or close a pending cat, grep, wc or any other command dependent on input.
	- `Ctrl+C` Interrupts a currently running process and skips the current prompt. Does not interrupt the shell itself.
- **I/O Redirection:**
	All types of redirections from Bash have been implemented and behave identically.
	- `<` - Reads from an input file.
	- `>` - Writes to an output file, truncating existing content. If said file does not exist, it will create it.
	- `>>` - Behaves identically to the write redirection but appends content instead of truncating.
	- `<<` - Creates a temporary file and allows the user to write to it. The string immediately after the heredoc symbol will be used as a delimiter to make the End-Of-File. Expansion will still take place inside the heredoc file but the delimiter string itself will be treated as the raw input string without any expansion or quote processing.

![redirs_test](/images/redirs_test.png)
- **Execution and Pipelines:**
	The shell will run all installed and native Unix/Bash commands as well as accept direct paths to executables. It can handle any number of pipes (`|`) from one command to another, executing each one in its own child process. All of this can be combined with redirections and all parsing features mentioned prior, just like Bash.

![pipes_test](/images/pipes_test.png)
- **Logic Operators and Sequences:**
	Additionally, you can use the `&&` and `||` operators between commands and they will be handled the same way Bash does. You can also execute separate commands in sequence by using `;` to separate them.
	- `&&` - Executes the command following it only if the previous command succeeded.
	- `||` - Executes the command following it only if the previous command failed.
	- `;` - Acts as a delimiter between groups of commands so that they can be executed in sequence completely independent of each other.

![logic_test](/images/logic_test.png)
- **Working History:**
	The up and down arrow keys can be used to cycle through past commands. All commands not interrupted by a signal are added to the history. TAB completion should also work normally like in any other shell.
- **Error Handling:**
	There are specific error codes for different types of errors that can occur during runtime, some fatal and some not. Errors within child processes spawned from the shell will also be reported and their exit status stored during the current loop. Signals affect the status the same way as in Bash.

![errors_test](/images/errors_test.png)

## Methodology
These are break-downs of how I handle each step of the shell's runtime loop. When you start the shell the environment will be duplicated and a loop will take in your input, handle any immediate edge-cases like signals or special keys and then process your input in the following order.

### Tokenization
Input is first transformed into a linked list of tokens. The tokenizer performs lexical analysis by scanning the raw input string and splitting it into meaningful units such as words, operators (`|`, `&&`, `||`, `;`), and redirection symbols (`<`, `>`, `>>`, `<<`).

The some processing is applied to the word tokens:
- Quote handling (single and double quotes)
- Variable expansions (`$VAR`, `$?`, `~`)
- Preservation of token boundaries for later parsing

The result is a sequential token stream consumed by the parser.

### Parsing
Parsing is implemented as a _recursive descent parser_ operating directly on the token linked list.

The parser constructs an AST (_Abstract Syntax Tree_) using precedence rules:

`parse_command` handles:
- argument accumulation
- redirection parsing
- local validation of syntax  

`parse_pipe` handles pipeline chaining  

`parse_logical` handles logical operators  

`parse_sequence` handles command sequences  

Each level builds binary AST nodes that preserve operator precedence.

Memory ownership is transferred into the AST once a command node is successfully constructed. On parse failure, partial structures are explicitly freed to prevent leaks.

### Execution
Execution is performed by walking the AST recursively.

- Command nodes:
  - Built-ins are executed directly in the parent process when required (e.g. `cd`, `export`)
  - External commands are executed via `fork()` + `execve()`
- Pipelines:
  - Pipes are created using `pipe()`
  - Each stage runs in a separate child process
  - File descriptors are duplicated using `dup2()` for stdin/stdout chaining
- Logical operators:
  - `&&` executes the right-hand side only if the left succeeds
  - `||` executes the right-hand side only if the left fails
- Redirections:
  - Input/output redirections are applied before execution
  - Heredoc input is handled by temporary file or internal buffering

The shell stores and propagates exit status similarly to Bash behavior.

### Error and Signal Handling
All runtime errors are reported through a centralized error system. Each error type is mapped to a human-readable message and a corresponding shell exit status, stored in `shell->last_status`.

| Error type        | Meaning / context                        | Status |
| ----------------- | ---------------------------------------- | ------ |
| INVALID_INPUT     | Malformed or empty input                 | 1      |
| SYNTAX_ERROR      | Unexpected token in parsing              | 2      |
| COMMAND_NOT_FOUND | Command not found in PATH                | 127    |
| INVALID_PATH      | File or directory does not exist         | 1      |
| PERMISSION_DENIED | Permission denied accessing file/command | 126    |
| MEMORY_ERROR      | Allocation failure                       | 1      |
| PIPE_FAIL         | Pipe creation failure                    | 1      |
| FORK_FAIL         | Process creation failure                 | 1      |
| EXEC_FAIL         | Execution failure                        | 126    |
| ENV_NOT_FOUND     | Environment List missing/unavailable     | 1      |

Errors are handled in two modes:
- Recoverable errors (`handle_error`):
    Printed to `stderr`, status is updated, and execution continues in the shell loop.
- Fatal errors (`handle_fatal_error`):
    Trigger full cleanup of shell state and terminate execution via `exit()`.

The shell handles SIGINT (Ctrl+C) using a `sig_atomic_t` flag set inside the signal handler. The handler only performs minimal safe operations (setting state and writing a newline), avoiding unsafe library calls.
Signal state is checked in the main loop via `interrupted()`, which resets the flag and allows the shell to update its state safely.
When a SIGINT is received the exit status is updated to **130**, matching typical shell behavior for interrupts.

## Usage
To run _traSH_, simply clone this repository and `cd` into it:
```sh
git clone https://github.com/KixiKcodes/traSH.git
cd ./traSH
```
Then compile with Make and clean up the generated object binaries:
```sh
make && make clean
```
Finally, you can just run `./traSH` and exit at any time by calling the `exit` builtin or sending the `CTRL+D` signal while being prompted normally.

**NOTE: I made a custom prompt that uses some special symbols and ANSI escape codes for formatting and colors. The shell prompt may not render properly on some terminals!**

## Addendum and Credits
I fully recorded my process from start to finish on video and plan on uploading it to YouTube as an edited tutorial series on how to write a shell, simply because of the fact this project is seen is quite cryptic and unapproachable by beginners. I like programming being more open instead of gate-keeping knowledge!

I make use of [linenoise](https://github.com/antirez/linenoise) by **Salvatore Sanfilippo** as a more stable and light-weight replacement for `readline`.
