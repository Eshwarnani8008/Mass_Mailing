# 1. Initialize git if not already done
git init

# 2. Remove all real .env files from tracking
git rm --cached -r services/
git rm --cached -r frontend/
git rm --cached .env 2>/dev/null

# 3. Copy the 3 files I created into your project
# (Replace the paths below with where you downloaded them)
cp README.md enterprise-platform/README.md
cp .gitignore enterprise-platform/.gitignore
cp .env.example enterprise-platform/.env.example

# 4. Stage and commit
git add .
git commit -m "Add README, .gitignore, .env.example — secure setup"

# 5. Push to GitHub
git remote add origin https://github.com/Eshwarnani8008/enterprise-platform.git
git push -u origin main
