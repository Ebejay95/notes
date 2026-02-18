# 42 Git & Tooling Cheatsheet

## Default Editor (Nano instead of Vim)

``` bash
git config --global core.editor "nano"
```

------------------------------------------------------------------------

# Git Remote Setup

## Option 1: Existing Local Folder

``` bash
git init
git remote add origin [vogshere-url]
```

## Option 2: Fresh Clone

``` bash
git clone [vogshere-url] [folder-name]
cd [folder-name]
```

------------------------------------------------------------------------

# Add Second Push URL (GitHub)

⚠ IMPORTANT: You must add the original origin URL again for push.

``` bash
git remote add [github-origin] [github-url]
git remote set-url --add --push origin [vogshere-url]
git remote set-url --add --push origin [github-url]
```

Verify configuration:

``` bash
git remote -v
```

Expected structure:

    [github-origin] [github-url] (fetch)
    [github-origin] [github-url] (push)
    origin [vogshere-url] (fetch)
    origin [vogshere-url] (push)
    origin [github-url] (push)

------------------------------------------------------------------------

# Push & Pull

## Push to both remotes

``` bash
git push
```

## Pull from both

``` bash
git fetch --all
git merge origin/master --allow-unrelated-histories
git merge [github-origin]/master --allow-unrelated-histories
```

⚠ BE CAREFUL: GitHub usually uses `main` instead of `master`.

------------------------------------------------------------------------

# Git Aliases

``` bash
alias push="git push origin master"
alias pull="git fetch --all && git merge origin/master && git merge [github-origin]/master"
alias shellcache="source ~/.zshrc"
alias shelledit="nano ~/.zshrc"
alias n="norminette | grep Error"
alias i='open https://profile.intra.42.fr/' &
alias chatty='open https://chat.openai.com/' &
alias code='/Applications/Visual\ Studio\ Code.app/Contents/Resources/app/bin/code' &
alias test='make re && make clean && echo "###########################################\n\n\n" && ./bin'
alias submodule="git submodule update --init --remote"
```

------------------------------------------------------------------------

# Git Submodules

``` bash
git submodule add https://github.com/Ebejay95/libft.git libft
git submodule init
git submodule update
```

## Makefile Automation

``` makefile
$(LIBFT_LIB):
    @git submodule update --init --recursive --remote > /dev/null 2>&1
    @$(MAKE) -C $(LIBFT_DIR)
```

------------------------------------------------------------------------

# Change Remote (Project Retry)

``` bash
git remote rm origin
git remote add origin [new vogshere url]

git add .
git commit -m "[YOUR MESSAGE]"
git push --set-upstream origin master
```

------------------------------------------------------------------------

# Valgrind

## Recommended Devcontainer

https://github.com/Reptudn/42-docker-container

## Standard Run

``` bash
valgrind --leak-check=full --show-leak-kinds=all --track-origins=yes --verbose
```

## Suppression File (readline.supp)

    {
        leak readline
        Memcheck:Leak
        ...
        fun:readline
    }
    {
        leak add_history
        Memcheck:Leak
        ...
        fun:add_history
    }

Run with suppression:

``` bash
valgrind --leak-check=full --show-leak-kinds=all --track-origins=yes --verbose --suppressions=readline.supp ./minishell
```

------------------------------------------------------------------------

# Valgrind Tester

## Compile

``` bash
gcc -g -Og -std=gnu99 *.c -o bin
```

## Run

``` bash
valgrind -s --leak-check=full --show-leak-kinds=all ./bin
```

------------------------------------------------------------------------

# C File Export Script (excluding libft)

``` bash
#!/bin/bash

directory="./"
output_file="out.txt"

> "$output_file"

process_directory() {
    local dir="$1"
    for item in "$dir"/*; do
        if [[ "$(basename "$item")" == "libft" ]]; then
            continue
        fi

        if [ -f "$item" ] && [[ "$item" == *.c ]]; then
            tail -n +12 "$item" >> "$output_file"
            echo "" >> "$output_file"
        elif [ -d "$item" ]; then
            process_directory "$item"
        fi
    done
}

process_directory "$directory"
```

------------------------------------------------------------------------

# Quick Reference

• Dual remote push\
• Submodule automation\
• Valgrind full leak detection\
• Project retry without losing history\
• 42 productivity aliases