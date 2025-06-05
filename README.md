# 1. Clone the repo (if not already)
git clone https://bitbucket.org/your-team/your-repo.git
cd your-repo

# 2. Fetch all branches
git fetch --all

# 3. List all remote branches except master and delete them
git branch -r | grep -v 'origin/master' | while read branch; do
  bname=$(echo $branch | sed 's/origin\///')
  git push origin --delete "$bname"
done
