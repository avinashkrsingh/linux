#!/bin/bash

# Define input and output YAML file
input_file="your_file.yaml"
output_file="cleaned_file.yaml"

# Regex pattern to match lines containing "168426.tc.ucm-case"
pattern="d\.tcs\.ucm-case"

# Use sed to delete lines that match the regex pattern
# The sed command removes any lines that contain the specified pattern
sed "/$pattern/d" "$input_file" > "$output_file"

echo "Unwanted entries removed and saved to '$output_file'"
