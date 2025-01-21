        - name: script
          value: |
            # Remove node_modules folder and package-lock.json before running npm install
            rm -rf node_modules
            rm -f package-lock.json
