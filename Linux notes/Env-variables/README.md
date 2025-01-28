# Understanding Environment Variables in Linux

## What Are Environment Variables?
Environment variables are dynamic values that affect the behavior of processes and applications running in a Linux system. They provide a way to configure and share data between the shell, user sessions, and applications. Examples include:
- `PATH`: Specifies directories where executable programs are located.
- `HOME`: Indicates the home directory of the current user.
- `USER`: Contains the name of the current logged-in user.

## Viewing Environment Variables
- To display all environment variables, use `env` or `printenv`:
  `env`
  `printenv`
- To display a specific variable, use `echo`:
  `echo $VARIABLE_NAME`
  Example: `echo $PATH`

## Creating Environment Variables
1. **Temporary Environment Variables**:
   Temporary variables exist only in the current shell session and are removed when the session ends.
   - Syntax:
     `VARIABLE_NAME="value"`
     `export VARIABLE_NAME`
   - Example:
     `MY_VAR="Hello, World!"`
     `export MY_VAR`
     To check: `echo $MY_VAR`
2. **Persistent Environment Variables**:
   Persistent variables remain available even after restarting the shell or the system. These are defined in configuration files.
   - **For a specific user**:
     1. Open the `.bashrc` or `.bash_profile` file in the user's home directory:
        `nano ~/.bashrc`
     2. Add the variable:
        `export VARIABLE_NAME="value"`
     3. Save and close the file, then reload it:
        `source ~/.bashrc`
   - **For all users**:
     1. Open the `/etc/environment` file:
        `sudo nano /etc/environment`
     2. Add the variable in the format:
        `VARIABLE_NAME="value"`
     3. Save and reboot the system:
        `sudo reboot`

## Unsetting Environment Variables
1. **Remove a Temporary Variable**:
   Use the `unset` command:
   `unset VARIABLE_NAME`
   Example: `unset MY_VAR`
2. **Remove a Persistent Variable**:
   Edit the file where the variable is defined (e.g., `.bashrc` or `/etc/environment`) and delete the line. Then reload the shell or reboot the system.

## Where Are Environment Variables Stored?
1. **Temporary Variables**: Stored only in the current shell session.
2. **User-Specific Variables**: Defined in `~/.bashrc`, `~/.bash_profile`, or `~/.zshrc` (if using Zsh shell).
3. **System-Wide Variables**: Defined in `/etc/environment`, `/etc/profile`, or files in `/etc/profile.d/`.

## How Environment Variables Work
1. **Exporting**: The `export` command makes a variable available to child processes of the current shell.
   Example:
   `MY_VAR="test"`
   `export MY_VAR`
   `bash`
   `echo $MY_VAR`
2. **Inheritance**: Environment variables are passed from parent processes to child processes.
3. **Scopes**:
   - **Local Variables**: Exist only in the current shell and are not passed to child processes.
   - **Environment Variables**: Global variables available to all processes.

## Commonly Used Environment Variables
| Variable      | Description                                    |
|---------------|------------------------------------------------|
| `PATH`        | Directories to search for executable programs. |
| `HOME`        | Home directory of the user.                   |
| `USER`        | Current logged-in user.                       |
| `SHELL`       | Path to the current shell (e.g., `/bin/bash`). |
| `EDITOR`      | Default text editor (e.g., `vim`, `nano`).     |
| `LANG`        | System language and locale settings.          |

## Example: Create and Use a Custom Environment Variable
1. Temporarily create a variable:
   `GREETING="Hello, Linux!"`
   `export GREETING`
   `echo $GREETING`
2. Persist the variable in `.bashrc`:
   `nano ~/.bashrc`
   Add this line:
   `export GREETING="Hello, Linux!"`
   Reload it:
   `source ~/.bashrc`
3. Use it in a script:
   ```bash
   #!/bin/bash
   echo "The greeting is: $GREETING"
