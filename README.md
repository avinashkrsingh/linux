#!/bin/bash

# Configuration
JIRA_URL="https://your-domain.atlassian.net/rest/api/3/issue"  # Jira API endpoint
AUTH_TOKEN="your-personal-access-token"  # Replace with your Jira Personal Access Token
PROJECT_KEY="PROJ"  # Replace with your Jira project key
ISSUE_TYPE="Task"  # Replace with the appropriate issue type (e.g., Task, Bug, Story)
CSV_FILE="output.csv"  # Your converted CSV file (manual conversion from Excel)

# Read the header to find the column indices dynamically
HEADER=$(head -n 1 "$CSV_FILE")
DESCRIPTION1_INDEX=$(echo "$HEADER" | tr ',' '\n' | grep -n '^Description1$' | cut -d: -f1)
DESCRIPTION4_INDEX=$(echo "$HEADER" | tr ',' '\n' | grep -n '^Description4$' | cut -d: -f1)

if [ -z "$DESCRIPTION1_INDEX" ] || [ -z "$DESCRIPTION4_INDEX" ]; then
  echo "Error: Could not find 'Description1' or 'Description4' column in the CSV."
  exit 1
fi

# Read CSV file and loop through each line, starting from line 2 (skip header)
tail -n +2 "$CSV_FILE" | while IFS=, read -r line; do
  # Extract the Description1 and Description4 columns dynamically using the found indices
  DESCRIPTION1=$(echo "$line" | cut -d',' -f"$DESCRIPTION1_INDEX")
  DESCRIPTION4=$(echo "$line" | cut -d',' -f"$DESCRIPTION4_INDEX")

  # Combine Description1 and Description4 into Summary
  SUMMARY="$DESCRIPTION4 | $DESCRIPTION1"
  
  # Create the issue payload in JSON format
  ISSUE_DATA=$(cat <<EOF
{
  "fields": {
    "project": {
      "key": "$PROJECT_KEY"
    },
    "summary": "$SUMMARY",
    "description": "$DESCRIPTION4",  # Use Description4 field as the description
    "issuetype": {
      "name": "$ISSUE_TYPE"
    }
  }
}
EOF
)

duedate="1-Mar-2025"

# Create a month-to-number mapping
month_name=$(echo $duedate | cut -d'-' -f2)
months=("Jan" "Feb" "Mar" "Apr" "May" "Jun" "Jul" "Aug" "Sep" "Oct" "Nov" "Dec")
month_num=$(printf "%02d" $(($(echo ${months[@]} | tr ' ' '\n' | grep -i -n "^$month_name" | cut -d: -f1) )))

# Extract day and year
day=$(echo $duedate | cut -d'-' -f1)
year=$(echo $duedate | cut -d'-' -f3)

# Format to YYYY-MM-DD
formatted_date="$year-$month_num-$(printf "%02d" $day)"

echo $formatted_date


  # Send POST request to create the issue
  RESPONSE=$(curl -s -o response.json -w "%{http_code}" -X POST \
    -H "Authorization: Bearer $AUTH_TOKEN" \
    -H "Content-Type: application/json" \
    --data "$ISSUE_DATA" \
    "$JIRA_URL")

  # Check if the issue creation was successful
  if [ "$RESPONSE" == "201" ]; then
    echo "Successfully created issue: $SUMMARY"
  else
    echo "Failed to create


    if [[ "$Type" == "VA ISSUE" || "$Type" == "VA Testing- Unchdule" ]]; then
    echo "Condition Matched: VA ISSUE or VA Testing- Unchdule"
elif [[ "$Type" == "Some Other Type" ]]; then
    echo "Condition Matched: Some Other Type"
else
    echo "Condition Not Matched"
fi
