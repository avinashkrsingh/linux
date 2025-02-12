#!/bin/bash

# Configuration
JIRA_URL="https://your-domain.atlassian.net/rest/api/3/issue"  # Jira API endpoint
AUTH_TOKEN="your-personal-access-token"  # Replace with your Jira Personal Access Token
PROJECT_KEY="PROJ"  # Replace with your Jira project key
ISSUE_TYPE="Task"  # Replace with the appropriate issue type (e.g., Task, Bug, Story)
EXCEL_FILE="your-file.xlsx"  # Path to your Excel file
CSV_FILE="output.csv"  # Temporary CSV file

# Convert Excel to CSV (using a tool like libreoffice or xlsx2csv)
# Example: libreoffice --headless --convert-to csv your-file.xlsx
# or use `xlsx2csv` tool to convert the Excel file:
xlsx2csv "$EXCEL_FILE" "$CSV_FILE"

# Read CSV and create Jira issues
# Skip the header row with "tail -n +2" and loop through each line
tail -n +2 "$CSV_FILE" | while IFS=, read -r description4_1 description4_2; do
  # Combine the two description4 fields into Summary
  SUMMARY="$description4_1 | $description4_2"
  
  # Create the issue payload in JSON format
  ISSUE_DATA=$(cat <<EOF
{
  "fields": {
    "project": {
      "key": "$PROJECT_KEY"
    },
    "summary": "$SUMMARY",
    "description": "$description4_1",  # Use the first description4 field as the description
    "issuetype": {
      "name": "$ISSUE_TYPE"
    }
  }
}
EOF
)

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
    echo "Failed to create issue: $SUMMARY"
    cat response.json  # Print the error message from Jira
  fi
done
