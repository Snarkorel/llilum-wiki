### Useful alias

Logging: (various level of prettified git log, I personally like git lg the best since it shows the branch graph)  
`alias.ll=log --pretty=tformat:'%C(yellow)%h%Cred%d %Creset%s %C(yellow)(%ad)%C(cyan) [%cn]%Creset' --decorate --numstat --date=relative`  
`alias.ls=log --pretty=tformat:'%C(yellow)%h%Cred%d %Creset%s %C(yellow)(%ad)%C(cyan) [%cn]%Creset' --decorate --date=relative`  
`alias.lds=log --pretty=tformat:'%C(yellow)%h %ad%Cred%d %Creset%s%C(cyan) [%cn]%Creset' --decorate --date=short`  
`alias.ld=log --pretty=tformat:'%C(yellow)%h %ad%Cred%d %Creset%s%C(cyan) [%cn]%Creset' --decorate --date=relative`  
`alias.lg=log --color --graph --pretty=tformat:'%C(yellow)%h%Creset%Cred%d%Creset %s %C(yellow)(%cr) %C(cyan)[%an]%Creset' --abbrev-commit`  

Diff-ing (git d does the diff on unstaged files, git dc does it for the staged files)  
`alias.d=!start /MIN git difftool --dir-diff`  
`alias.dc=!start /MIN git difftool --dir-diff --cached`  

Other shorthands  
`alias.aa=add -A`  
`alias.sa=stash apply`  
`alias.s=status`  
`alias.co=checkout`  
`alias.a=add`  
`alias.ci=commit`  
`alias.cp=cherry-pick`  
`alias.st=status -s`  
`alias.cl=clone`  
`alias.sl=stash list`  
`alias.br=branch`  
`alias.ss=stash save`  

### Tools
* Posh-git -- https://github.com/dahlbyk/posh-git  
Powershell extension for git. I do all my command prompts from powershell nowadays, so this is a nice addition.
I like it in particular because it does git command completion with tab, and show me the current branch and items staged / modified on my prompt.

* Git extension -- http://sourceforge.net/projects/gitextensions/  
A nice graphical tool for visualizing changes and perform many operators via the GUI too.


### What to do if things are messed up
If your repo is in a bad state and you'd like to go back in time, here's a nifty git command:  
`git reflog show --date=default`  
This will list all the commits that HEAD has pointed to in reverse chronological order. This means that it will list out all the commit hashes of every commit, every branch switch, rebase, merge ...etc. that you've done.  
To go back to a particular point, just do:  
`git checkout <commit hash>`  
then you can re-attach it to a branch if needs to be.

Some examples of when this might be useful:  
* Pull down and rebase to the latest from upstream and now things don't work? You can go back to your commit prior to the rebase.  
* Botch a merge and want to start over? Go back to the commit prior to your merge.
* Accidentally squash a change? Go back to the commit prior to your interactive rebase.
* Delete a branch by accident and now you can't get back to the commit it was pointing to? The commit should be in the list.