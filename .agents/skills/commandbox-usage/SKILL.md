---
name: commandbox-usage
description: "Use this skill for CommandBox CLI usage: running commands, namespaces, tab completion, system settings, environment variables, shell variables, piping, recipes, REPL, foreach loops, jq/sql/printtable commands, ad-hoc aliases, default parameters, exit codes, and interactive shell features."
---

# CommandBox CLI Usage

## Overview

CommandBox is an interactive shell and script runner. Launch it with `box` (interactive) or run one-off commands with `box <command>`.

```bash
# Interactive shell
box

# One-off command
box version
box install coldbox
box server start
```

---

## Commands & Namespaces

Commands are case-insensitive and organized into namespaces (multi-word groups):

```bash
# Single commands
version
upgrade
reload           # alias: r

# Namespaced commands
server start
server stop
server list
package init
package set name="My App"
artifacts list
artifacts clean
config set
config show
testbox run
```

Get help for any namespace or command:

```bash
server help
server start help
package help
```

---

## Parameters

```bash
# Named parameters
server start port=8080 host=localhost

# Positional parameters
install coldbox

# Boolean flags
install coldbox --verbose
install coldbox --noSave
server start --noSaveSettings --noOpenBrowser

# Quoted values with spaces
package set description="My great app"

# JSON values
config set myArray="['a','b','c']"
```

### Default Command Parameters

```bash
# Store defaults so you don't have to type them every time
command params set "server start" openBrowser=false
command params set "install" --verbose

# See current defaults
command params show "server start"

# Clear defaults
command params clear "server start"
```

### Escaping Special Characters

```bash
# Escape equals sign
echo foo\=bar

# Escape backtick (used for expressions)
echo \`notAnExpression\`
```

---

## System Settings (Dynamic Placeholders)

System settings allow dynamic values from JVM properties and OS environment variables:

```bash
# Use an env var
echo ${PATH}
echo ${HOME}

# With default value if not found
server start port=${SERVER_PORT:8080}
server set web.host=${SERVER_HOST:localhost}
```

**Lookup order** (first match wins):
1. Per-command env vars
2. Parent command env vars
3. Global shell env vars
4. JVM System Properties
5. OS Environment Variables

In `server.json` and `box.json`:
```json
{
    "web": {
        "http": {
            "port": "${WEB_PORT:8080}"
        }
    }
}
```

```bash
# System setting expansion namespaces
echo ${server::web.http.port}        # from server.json
echo ${box::version}                 # from box.json
echo ${config::name}                 # from CommandBox config
echo ${env::MY_VAR}                  # force OS env var lookup
echo ${java::user.home}              # JVM system property
```

---

## Environment Variables (Shell)

```bash
# Set global shell variable
set foo=bar
env set foo=bar

# Read variable
env show foo
echo ${foo}
echo ${foo:default}

# Clear variable
env clear foo

# Show all shell variables
env show

# Debug variable hierarchy
env debug
```

Per-command variables (scoped to that command only):

```bash
# Variable set in expression only exists there
echo `set myVar=cheese && echo ${myVar}`
env show myVar myDefault   # outputs "myDefault"
```

---

## Expressions & Piping

```bash
# Backtick expressions — command result is substituted
echo `package show version`
package set version=`package show version`

# Pipe output to another command
package show dependencies | foreach "echo 'Package: ${item}'"

# Pipe into commands
echo "coldbox" | install

# Chain commands with &&
install coldbox && server start
```

---

## Recipes

Recipes are scripts of CommandBox commands run in a subshell:

```bash
# Create a recipe file: myRecipe.boxr
echo "install coldbox"
echo "server start"

# Run it
recipe myRecipe.boxr

# Pipe commands as a recipe
echo "upgrade; version" | recipe

# Inline recipe with semicolons
recipe "install coldbox; server start; testbox run"
```

---

## Foreach (Looping)

```bash
# Loop over comma-separated list
foreach "foo,bar,baz" "echo 'Item: ${item}'"

# Loop over command output
package show dependencies | foreach "echo 'Dep: ${item}'"

# Loop with custom delimiter
foreach delimiter="|" "a|b|c" "echo ${item}"

# Loop with index
foreach "a,b,c" "echo '${index}: ${item}'"
```

---

## REPL (Interactive Evaluation)

```bash
# Start the REPL
repl

# In the REPL — evaluate CFML/BoxLang
now()
createUUID()
x = 5; y = 10; x + y

# Exit REPL
exit
```

---

## Helpful Commands

### `jq` — JSON Query

```bash
# Filter JSON output
package show | jq .name
server list --json | jq '.[].name'

# Use JMESPath expressions
config show | jq 'keys(@)'
```

### `sql` — Query Tabular Data

```bash
# Query output as SQL
server list --json | sql "select name, status from servers where status='running'"
```

### `printtable` — Pretty Table Output

```bash
# Display data as an ASCII table
server list --json | printtable
package list --json | printtable --headers "name,version,description"
```

### `ask` and `confirm` — Interactivity

```bash
# Ask user for input
set name=`ask "What is your name? "`
echo "Hello, ${name}!"

# Yes/no confirmation
confirm "Delete all files?" && rm -rf ./build
```

### `checksums`

```bash
# Generate a checksum
checksum file=myfile.zip algorithm=sha256
```

### `token` Replacements

```bash
# Replace tokens in files
tokenReplace path="./config.xml" token="@@version@@" replacement=`package show version`
```

---

## Ad-Hoc Command Aliases

```bash
# Create an alias
alias set ls="server list"
alias set gs="!git status"   # OS command with !

# Use the alias
ls

# Remove alias
alias remove ls

# Show all aliases
alias show
```

---

## Exit Codes

```bash
# CommandBox commands return exit codes for scripting
box server start && echo "Server started OK"
box testbox run || echo "Tests failed!"

# Check exit code in shell
if box testbox run; then
    echo "All tests passed"
fi
```

---

## Watch Command

```bash
# Watch for file changes and run a command
watch command="testbox run" paths="**.cfc,**.cfm"

# Watch with custom delay
watch command="server restart" paths="server.json" delay=500
```

---

## Auto-Update Checks

```bash
# Disable auto-update checks
config set autoupdatecheck=false

# Manual update
upgrade
```

---

## Bullet Train Prompt

The interactive shell supports a customizable prompt:

```bash
# Enable bullet train style
config set prompt.multiline=true
config set modules.commandbox-bullet-train.showGitBranch=true
```

---

## 256 Color Support

```bash
# Print with colors using print helper (in commands/tasks)
print.red( "Error message" )
print.boldGreenLine( "Success!" )
print.cyanLine( "Info" )

# In recipes/shell
echo "Text"   # standard output
```