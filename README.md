#!/bin/bash

# Set branch names
DEV_BRANCH="dev/39.0"
RELEASE_BRANCH="release/39.0"

# Fetch the latest changes from the remote repository
git fetch origin

# Checkout the release branch
echo "Checking out the $RELEASE_BRANCH branch..."
git checkout $RELEASE_BRANCH

# Pull the latest changes for the release branch (optional, in case there are changes not yet merged locally)
echo "Pulling the latest changes for $RELEASE_BRANCH..."
git pull origin $RELEASE_BRANCH

# Checkout the dev branch and merge into release branch
echo "Merging $DEV_BRANCH into $RELEASE_BRANCH..."
git checkout $DEV_BRANCH
git pull origin $DEV_BRANCH  # Ensure the dev branch is up-to-date
git checkout $RELEASE_BRANCH

# Perform the merge
git merge $DEV_BRANCH

# Resolve conflicts (if any)
if [ $? -ne 0 ]; then
    echo "Merge conflicts detected. Please resolve them manually."
    exit 1
fi

# Push the merged changes to the remote repository
echo "Pushing the merged changes to $RELEASE_BRANCH..."
git push origin $RELEASE_BRANCH

# Provide a success message
echo "Merge of $DEV_BRANCH into $RELEASE_BRANCH was successful and changes were pushed to the remote."
