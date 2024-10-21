To merge 'main' into your branch, go to the branch folder on your machine and run `git merge main`. 

When pushing to the same branch from two different devices, you have to follow a specific set of instructions. They are listed below. 

Before you start taking notes:
```Bash
git pull origin <branch-name>
```

When you're done taking notes for the moment:
```bash
git commit -am "<Your commit message>" # -a commits all, -m is the commit message
git push origin <branch-name> # Push changes to the remote branch
```

If you try to commit and get an error along the lines of "LF will be replaced with CLRF," then you should run `git config --global core.autocrlf true`. [This link](https://stackoverflow.com/questions/1967370/git-replacing-lf-with-crlf) will explain why. If that's not a satisfactory explanation, then [this]() should help. 

If you're not sure what I mean by that, watch a tutorial about Git CLI. It's the command line version of Git. To successfully run Git CLI commands, you have to be in the folder on your device that contains the Git repo. 