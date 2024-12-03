#!/bin/bash

# List of repositories (local directories where the repositories are stored)
REPOS=("repo1" "repo2" "repo3")  # Add your repository folder names here

# Define branch names
DEV_BRANCH="dev"        # or "dev/39.0", depending on your naming convention
RELEASE_BRANCH="release"  # or "release/39.0"

# Loop through each repository
for REPO in "${REPOS[@]}"
do
  echo "Processing repository: $REPO"
  
  # Navigate into the repository directory
  cd "$REPO" || { echo "Directory $REPO not found!"; exit 1; }

  # Fetch the latest changes from the remote repository
  echo "Fetching the latest changes from the remote repository..."
  git fetch origin
  
  # Checkout the release branch
  echo "Checking out the $RELEASE_BRANCH branch..."
  git checkout $RELEASE_BRANCH || { echo "Failed to checkout $RELEASE_BRANCH in $REPO"; exit 1; }

  # Pull the latest changes for the release branch
  echo "Pulling the latest changes for $RELEASE_BRANCH..."
  git pull origin $RELEASE_BRANCH || { echo "Failed to pull changes for $RELEASE_BRANCH in $REPO"; exit 1; }

  # Checkout the dev branch and pull the latest changes
  echo "Checking out the $DEV_BRANCH branch..."
  git checkout $DEV_BRANCH || { echo "Failed to checkout $DEV_BRANCH in $REPO"; exit 1; }
  git pull origin $DEV_BRANCH || { echo "Failed to pull changes for $DEV_BRANCH in $REPO"; exit 1; }

  # Checkout the release branch again before merging
  echo "Switching back to $RELEASE_BRANCH branch..."
  git checkout $RELEASE_BRANCH || { echo "Failed to checkout $RELEASE_BRANCH again in $REPO"; exit 1; }

  # Perform the merge
  echo "Merging $DEV_BRANCH into $RELEASE_BRANCH..."
  git merge $DEV_BRANCH || { echo "Merge conflict in $REPO. Please resolve manually."; exit 1; }

  # Push the merged changes to the remote repository
  echo "Pushing changes to $RELEASE_BRANCH..."
  git push origin $RELEASE_BRANCH || { echo "Failed to push changes to $RELEASE_BRANCH in $REPO"; exit 1; }

  # Move back to the parent directory to process the next repo
  cd ..
  
  echo "Successfully merged $DEV_BRANCH into $RELEASE_BRANCH in $REPO."
  echo "---------------------------------------------------"
done

echo "All repositories processed successfully."
