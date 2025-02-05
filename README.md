#!/bin/bash

# Set the folder to exclude
exclude_folder="2025folder"

# Loop through the contents of the current directory
for item in *; do
  # Check if the item is not the folder you want to keep
  if [ "$item" != "$exclude_folder" ]; then
    # Remove the item (file or directory)
    rm -rf "$item"
  fi
done
