#!/bin/bash

# GitHub API endpoint and your personal access token
GITHUB_API="https://api.github.com"
GITHUB_TOKEN="your_personal_access_token"  # Replace with your actual GitHub token
USERNAME="your_github_username"            # Replace with your GitHub username

# List of repositories where the PR will be created
REPOSITORIES=("repo1" "repo2" "repo3")  # Replace with the actual repositories

# Source and target branches
SOURCE_BRANCH="dev/rel_39.0"
TARGET_BRANCH="release/rel_39.0.0"

# Loop through each repository
for REPO in "${REPOSITORIES[@]}"; do
  # Create a pull request using GitHub API
  RESPONSE=$(curl -s -X POST \
    -H "Authorization: token $GITHUB_TOKEN" \
    -H "Accept: application/vnd.github.v3+json" \
    -d '{
      "title": "Merge '"$SOURCE_BRANCH"' into '"$TARGET_BRANCH"'",
      "head": "'"$SOURCE_BRANCH"'",
      "base": "'"$TARGET_BRANCH"'",
      "body": "This PR merges changes from '"$SOURCE_BRANCH"' into '"$TARGET_BRANCH"' for release preparation."
    }' "$GITHUB_API/repos/$USERNAME/$REPO/pulls")

  # Check if the pull request was created successfully
  PR_URL=$(echo "$RESPONSE" | jq -r '.html_url')

  if [[ "$PR_URL" != "null" ]]; then
    echo "Pull request created successfully for '$REPO': $PR_URL"
  else
    ERROR_MSG=$(echo "$RESPONSE" | jq -r '.message')
    echo "Failed to create PR for '$REPO': $ERROR_MSG"
  fi
done
