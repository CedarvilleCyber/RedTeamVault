To merge 'main' into your branch, go to the branch folder on your machine and run `git merge main`. 

When pushing to the same branch from two different devices, you have to follow a specific set of instructions. They are listed below. 

Before you start taking notes:
```Bash
git pull origin <branch-name>
```

When you're done taking notes for the moment:
```bash
git add . # Stage your changes
git commit -m "Your commit message"
git push origin <branch-name> # Push changes to the remote branch
```

