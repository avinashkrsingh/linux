#!/bin/bash

# Define the branches to keep
keep_branches=("main" "e" "r" "t" "y" "sa" "a" "arqr" "q")

# List all local branches
local_branches=$(git branch | sed 's/^\* //' | tr -d ' ')

# Loop through each local branch and delete if not in the keep list
for branch in $local_branches; do
    if [[ ! " ${keep_branches[@]} " =~ " ${branch} " ]]; then
        echo "Deleting local branch: $branch"
        git branch -D $branch
    fi
done

# List all remote branches
remote_branches=$(git branch -r | sed 's/origin\///' | tr -d ' ')

# Loop through each remote branch and delete if not in the keep list
for branch in $remote_branches; do
    if [[ ! " ${keep_branches[@]} " =~ " ${branch} " ]]; then
        echo "Deleting remote branch: $branch"
        git push origin --delete $branch
    fi
done
