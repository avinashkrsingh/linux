#!/bin/bash

# Specify the directory containing the folders
directory="$1"

# Check if the directory is provided and exists
if [ -z "$directory" ]; then
  echo "Please provide a directory."
  exit 1
elif [ ! -d "$directory" ]; then
  echo "The specified directory does not exist."
  exit 1
fi

# Output file to store the generated content
output_file="foldernames.yml"

# Start the output file
echo "#start of foldername" > "$output_file"

# Loop through each folder in the specified directory
for folder in "$directory"/*/; do
  # Check if it's a directory
  if [ -d "$folder" ]; then
    # Extract folder name (strip trailing slash)
    folder_name=$(basename "$folder")
      # Remove the "afa" prefix if it exists
    folder_name_cleaned=$(echo "$folder_name" | sed 's/^168426\.totalconduct\.//')
    
    # Append folder name in the desired format to the output file
    echo " - displayName: \"$folder_name\"" >> "$output_file"
    echo "     regex: \".*$folder_name.*\"" >> "$output_file"
  fi
done

echo "Output written to $output_file"
