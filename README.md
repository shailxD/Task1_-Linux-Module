# Task1_-Linux-Module
XenonStack Placement Technical Task

###Getting Started 

1. You can use any text editor of your choice, such as nano, vim, etc.
For example, using nano:
    ```bash
nano internsctl
```

1. Copy the entire Bash script and paste it into the text editor.

3. Save the file by pressing Ctrl + O (in nano) and press Enter.

5. Close the text editor by pressing Ctrl + X (in nano).

7. Make the script executable by running the following command:
```bash
chmod +x internsctl
```

Now, your Bash script is saved and ready to be executed. You can run it by typing ./internsctl in the terminal. For example:
```bash
./internsctl --version
./internsctl --help
./internsctl cpu getinfo
./internsctl memory getinfo
./internsctl user create <username>
./internsctl user list
./internsctl user list --sudo-only
./internsctl file getinfo <file-name>
./internsctl file getinfo --size <file-name>
./internsctl file getinfo --permissions <file-name>
./internsctl file getinfo --owner <file-name>
./internsctl file getinfo --last-modified <file-name>

```
This script should meet the requirements outlined in Sections A and B of the scenario.

### Explanation

```bash
#!/bin/bash

```
This line specifies that the script should be interpreted by the Bash shell.


```bash
# Section A
if [ "$1" == "--version" ]; then
    echo "internsctl v0.1.0"
    exit 0
elif [ "$1" == "--help" ]; then
    # Displaying help information for the script.
    # Each option and its usage are listed here.
    echo "Usage: internsctl [OPTIONS]"
    # ... (output truncated for brevity)
    exit 0
fi

```

This section handles the --version and --help options. If the user specifies either of these options, it prints the corresponding information and exits.


```bash
# Section B
case "$1" in
    "cpu" )
        if [ "$2" == "getinfo" ]; then
            # If the user runs "internsctl cpu getinfo", it executes the lscpu command.
            lscpu
        else
            echo "Invalid command. Use 'internsctl --help' for usage information."
            exit 1
        fi
        ;;
    "memory" )
        if [ "$2" == "getinfo" ]; then
            # If the user runs "internsctl memory getinfo", it executes the free command.
            free
        else
            echo "Invalid command. Use 'internsctl --help' for usage information."
            exit 1
        fi
        ;;
    "user" )
        case "$2" in
            "create" )
                if [ -z "$3" ]; then
                    echo "Please provide a username. Usage: internsctl user create <username>"
                    exit 1
                fi
                # Creates a new user using useradd and sets the password using passwd.
                useradd "$3"
                passwd "$3"
                echo "User '$3' created successfully."
                ;;
            "list" )
                if [ "$3" == "--sudo-only" ]; then
                    # Lists users with sudo permissions.
                    grep -Po '^sudo.+:\K.*$' /etc/group | tr ',' '\n'
                else
                    # Lists all regular users.
                    cut -d: -f1 /etc/passwd
                fi
                ;;
            * )
                echo "Invalid command. Use 'internsctl --help' for usage information."
                exit 1
                ;;
        esac
        ;;
    "file" )
        if [ "$2" == "getinfo" ]; then
            file_name="$3"
            if [ -z "$file_name" ]; then
                echo "Please provide a file name. Usage: internsctl file getinfo <file-name>"
                exit 1
            fi
            if [ ! -e "$file_name" ]; then
                echo "File '$file_name' does not exist."
                exit 1
            fi

            case "$4" in
                "--size" | "-s" )
                    # Outputs the size of the specified file.
                    stat -c%s "$file_name"
                    ;;
                "--permissions" | "-p" )
                    # Outputs the permissions of the specified file.
                    stat -c%a "$file_name"
                    ;;
                "--owner" | "-o" )
                    # Outputs the owner of the specified file.
                    stat -c%U "$file_name"
                    ;;
                "--last-modified" | "-m" )
                    # Outputs the last modified time of the specified file.
                    stat -c%y "$file_name"
                    ;;
                * )
                    echo "Invalid option. Use 'internsctl --help' for usage information."
                    exit 1
                    ;;
            esac
        else
            echo "Invalid command. Use 'internsctl --help' for usage information."
            exit 1
        fi
        ;;
    * )
        echo "Invalid command. Use 'internsctl --help' for usage information."
        exit 1
        ;;
esac

```

This section handles the main functionalities based on the provided arguments. It uses a case statement to match the first argument ($1) against different cases. The script performs specific actions based on the provided options like cpu, memory, user, or file. It also checks for subcommands like getinfo and processes them accordingly.
