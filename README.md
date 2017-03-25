# Ion Shell

[![Build Status](https://travis-ci.org/redox-os/ion.svg)](https://travis-ci.org/redox-os/ion)
[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![Coverage Status](https://coveralls.io/repos/redox-os/ion/badge.svg?branch=master&service=github)](https://coveralls.io/github/redox-os/ion?branch=master)
[![crates.io](http://meritbadge.herokuapp.com/ion-shell)](https://crates.io/crates/ion-shell)

Ion is a shell for UNIX platforms, and is the default shell in Redox. It is still a work in progress, but much of the
core functionality is complete. Features below:

- [x] Shell Expansion
- [x] Flow Control
- [x] Aliases
- [x] Variables
- [x] Functions
- [ ] Arrays
- [ ] Maps
- [x] For Loops
- [ ] Foreach Loops
- [x] While Loops
- [x] If Conditionals
- [x] Piping Stdout/Stderr
- [x] Redirecting Stdout/Stderr
- [x] && and || Conditionals
- [x] Background Jobs
- [ ] Background Jobs Control
- [ ] Signal Handling
- [ ] Autosuggestions
- [ ] Syntax Highlighting
- [x] Multiline Comments and Commands
- [ ] Multiline Editing
- [x] Tab Completion (Needs Improvements)
- [ ] Unescape sequences like '\n' and '\t'

## Shell Syntax

### Defining Variables

The `let` keyword is utilized to create local variables within the shell. The `export` keyword performs
a similar action, only setting the variable globally as an environment variable for the operating system.

```ion
let git_branch = $(git rev-parse --abbrev-ref HEAD ^> /dev/null)
```

If the command is executed without any arguments, it will simply list all available variables.

### Dropping Variables

To drop a value from the shell, the `drop` keyword may be used:

```ion
drop git_branch
```

### Variable Arithmetic

The `let` command also supports basic arithmetic.

```ion
let a = 1
echo $a
let a += 4
echo $a
let a *= 10
echo $a
let a /= 2
echo $a
let a -= 5
echo $a
```

### Export

The `export` command works similarly to the `let` command, but instead of defining a local variable, it defines a global variable that other processes can access.

```ion
export PATH = "~/.cargo/bin:${PATH}"
```

### Export Arithmetic

The `export` command also supports basic arithmetic.

```ion
export a = 1
echo $a
export a += 4
echo $a
export a *= 10
echo $a
export a /= 2
echo $a
export a -= 5
echo $a
```

### Aliases

The `alias` command is used to set an alias for running other commands under a different name. The most common usages of the `alias` keyword are to shorten the keystrokes required to run a command and it's specific arguments, and to rename a command to something more familiar.

```ion
alias ls = 'exa'
```

If the command is executed without any arguments, it will simply list all available aliases.

The `unalias` command performs the reverse of `alias` in that it drops the value from existence.

```ion
unalias ls
```

### Commands

Commands may be written line by line or altogether on the same line with semicolons separating them.

```ion
command arg1 arg2 arg3
command arg1 arg2 arg3
command arg1 arg2 arg3; command arg1 arg2 arg3; command arg1 arg2 arg3
```

### Piping & Redirecting Standard Output

The pipe (`|`) and redirect (`>`) operators are used for manipulating the standard output.

```ion
command arg1 | other_command | another_command arg2
command arg1 > file
```

### Piping & Redirecting Standard Error

The `^|` and `^>` operators are used for manipulating the standard error.

```ion
command arg1 ^| other_command
command arg1 ^> file
```

### Piping & Redirecting Both Standard Output & Standard Error

The `&|` and `&>` operators are used for manipulating both the standard output and error.

```ion
command arg1 &| other_command # Not supported yet
command arg1 &> file
```

### Conditional Operators

The Ion shell supports the `&&` and `||` operators in the same manner as the Bash shell. The `&&` operator
executes the following command if the previous command exited with a successful exit status. The `||`
operator performs the reverse -- executing if the previous command exited in failure.

```ion
test -e .git && echo Git directory exists || echo Git directory does not exist
```

### If Conditions

It is also possible to perform more advanced conditional expressions using the `if`, `else if`, and `else` keywords.

```ion
let a = 5;
if test $a -lt 5
    echo "a < 5"
else if test $a -eq 5
    echo "a == 5"
else
    echo "a > 5"
end
```

### While Loops

While loops will evaluate a supplied expression for each iteration and execute all the contained statements if it
evaluates to a successful exit status.

```ion
let a = 1
while test $a -lt 100
    echo $a
    let a += 1
end
```

### For Loops

For loops, on the other hand, will take a variable followed by a list of values or a range expression, and
iterate through all contained statements until all values have been exhausted. If the variable is `_`, it
will be ignored.

```ion
# Obtaining Values From a Subshell
for a in $(seq 1 10)
    echo $a
end

# Values Provided Directly
for a in 1 2 3 4 5
    echo $a
end

# Exclusive Range
for a in 1..11
    echo $a
end

# Inclusive Range
for a in 1...10
    echo $a
end

# Ignore Value
for _ in 1..10
   do_something
end

# Brace Ranges
for a in {1..10}
    echo $a
end

# Globbing
for a in *
    echo $a
end
```

### Functions

Functions in the Ion shell are defined with a name along with a set of variables. The function
will check if the correct number of arguments were supplied and execute if all arguments
were given.

```ion
fn fib n
    if test $n -le 1
        echo $n
    else
        let output = 1
        let previous = 1
        for _ in 2..$n
            let temp = $output
            let output += $previous
            let previous = $temp
        end
        echo $output
    end
end

for i in 1..20
    fib $i
end
```

### Brace Expansion

Brace expansion is used to create permutations of a given input. In addition to simple permutations, Ion supports
brace ranges and nested branches.

```ion
echo abc{3..1}def{1..3,a..c}
echo ghi{one{a,b,c},two{d,e,f}}
```
