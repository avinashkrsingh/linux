#!/bin/bash

# Usage:
# ./run_dml.sh "UPDATE employees SET active = 'Y' WHERE dept = 'HR';"
# ./run_dml.sh /path/to/file.sql

# === Oracle DB Connection Details ===
ORACLE_USER="your_user"
ORACLE_PASSWORD="your_password"
ORACLE_SID="your_sid"           # Or SERVICE_NAME
ORACLE_HOST="your_host"
ORACLE_PORT="1521"

# === Forbidden keywords ===
FORBIDDEN_KEYWORDS=("CREATE" "ALTER" "DROP" "TRUNCATE" "PACKAGE" "FUNCTION" "PROCEDURE" "BEGIN" "DECLARE")

# === Check if input is a file ===
if [ -f "$1" ]; then
    SQL_CONTENT=$(cat "$1")
    IS_FILE=true
    FILE_PATH="$1"
else
    SQL_CONTENT="$1"
    IS_FILE=false
fi

# === Convert to uppercase for case-insensitive check ===
SQL_UPPER=$(echo "$SQL_CONTENT" | tr '[:lower:]' '[:upper:]')

# === Block if forbidden keywords are present ===
for keyword in "${FORBIDDEN_KEYWORDS[@]}"; do
    if echo "$SQL_UPPER" | grep -wq "$keyword"; then
        echo "❌ Forbidden keyword detected: '$keyword' – Only DML statements are allowed."
        exit 1
    fi
done

# === Execute SQL using Oracle SQL*Plus ===
echo "✅ Executing DML command..."

if $IS_FILE; then
    sqlplus -s "$ORACLE_USER/$ORACLE_PASSWORD@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=$ORACLE_HOST)(PORT=$ORACLE_PORT))(CONNECT_DATA=(SID=$ORACLE_SID)))" @"$FILE_PATH"
else
    echo "$SQL_CONTENT" | sqlplus -s "$ORACLE_USER/$ORACLE_PASSWORD@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=$ORACLE_HOST)(PORT=$ORACLE_PORT))(CONNECT_DATA=(SID=$ORACLE_SID)))"
fi
