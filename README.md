#!/bin/bash

# Define input and output YAML file
input_file="your_file.yaml"
output_file="cleaned_file.yaml"

# Use sed to dynamically capture the part after "168426.tc."
# and replace it with ".*<captured_value>*"
sed -E 's/(dddsds\.sdd\.[^ ]*)/.*\1.*/g' "$input_file" > "$output_file"

echo "Pattern replaced dynamically and saved to '$output_file'"
