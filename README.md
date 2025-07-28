
#!/bin/bash

# Decrypt temporarily and run
gpg --quiet --batch --yes --decrypt --output /tmp/temp_dml.sh run_dml.sh.gpg

chmod +x /tmp/temp_dml.sh
/tmp/temp_dml.sh "$@"

# Clean up
rm -f /tmp/temp_dml.sh
