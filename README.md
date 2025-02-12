curl -X POST \
     -H "Authorization: Bearer your-personal-access-token" \
     -H "Content-Type: application/json" \
     --data '{
         "fields": {
             "project": {
                 "key": "PROJ"
             },
             "summary": "New issue created via API using PAT",
             "description": "This issue was created through the Jira API using a Personal Access Token.",
             "issuetype": {
                 "name": "Bug"
             }
         }
     }' \
     https://your-domain.atlassian.net/rest/api/3/issue
