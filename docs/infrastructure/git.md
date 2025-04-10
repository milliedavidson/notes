# Git

## Pushing local repository to GitHub

- Create a new repository on Github.

- Initialise Git repository: ```git init```

- Add all your files to the repository: ```git add .```

- Commit the files: ```git commit -m "Initial commit"```

- Add the GitHub repository as a remote: ```git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO-NAME.git```

- Push your code to GitHub ```git push -u origin main``` (or ```master```)

## Merging development branch to main branch

- Make sure you have the latest changes from both branches: ```git fetch --all```

- Switch to the main branch: ```git checkout main```

- Pull the latest changes on main to ensure you're up to date: ```git pull origin master```

- Merge your development branch into main: ```git merge development```

- Push the merged changes to the remote repository: ```git push origin main```
