#!/bin/bash

# List of repository URLs or local repo paths
REPOS=(
  "git@github.com:your-org/repo1.git"
  "git@github.com:your-org/repo2.git"
  "git@github.com:your-org/repo3.git"
)

# Tag name and message
TAG_NAME="v1.0.0"
TAG_MESSAGE="Release $TAG_NAME"

# Temporary directory to clone repos
WORKDIR="/tmp/repos-tagging"

# Cleanup old workdir
rm -rf "$WORKDIR"
mkdir -p "$WORKDIR"

# Loop through each repository
for REPO in "${REPOS[@]}"; do
  echo "Processing repo: $REPO"

  REPO_NAME=$(basename "$REPO" .git)
  cd "$WORKDIR" || exit
  git clone "$REPO"
  cd "$REPO_NAME" || continue

  # Checkout main and pull latest
  git checkout main
  git pull origin main

  # Create tag and push
  git tag -a "$TAG_NAME" -m "$TAG_MESSAGE"
  git push origin "$TAG_NAME"

  echo "Tagged $REPO with $TAG_NAME"
done

echo "✅ All repositories tagged."
