This page describes the basic workflow of a check in, from syncing, making the change locally, iterating through code review, to final submission using git.  

This workflow assumes that you have a private fork of the zelig-pr repo -- `https://github.com/<your_username>/zelig-pr.git`, and the NETMF central zelig-pr repo is set as the remote repository named "upstream" (`git remote add upstream https://github.com/NETMF/zelig-pr.git`).

 1. Before you begin, make sure your private fork/origin's dev branch is clean and up to date with the upstream.
		`git checkout dev`  
		`git fetch upstream`  
		`git merge upstream/dev`  
		`git push`  
	There should be no conflicts to resolve. And at this point, your _dev_, _origin/dev_, and _upstream/dev_ should all point to the same place.
	
 2. On your private fork, while on the dev branch, create a new branch off of dev for the work you are going to do.  
		`git checkout -b NewWorkBranch`  
		
 3. In this new branch, make your changes. Do as many commits as you'd like. You can also make other branches to experiment different approach ...etc. This is git, so use it to its full potential.  
		`git add changedFile.cs`  
		`git commit -c "My change description"`  
		`...`  
		
 4. At any time, if you need to pull in new changes from upstream, do this:  
		`<stash away or commit your outstanding changes if there are any>`  
		`git checkout dev`  
		`git pull upstream dev`  
		`git push`  
		`git checkout NewWorkBranch`  
		`git rebase dev`  
		`<handle merge conflicts if there's any>`  
		`<apply back the stash if there were changes that was stashed from earlier>`  
	Note that we use rebase here, so that any changes you make in _NewWorkBranch_ is re-apply on top of the latest change from _dev_. This is essential to keeping the history clean and easily readable when it is finally merge back to upstream.
	
 5. Do as many step 3 and step 4 until you are ready to for code review. Perform the commands in step 4 to make sure your _NewWorkBranch_ is up to date with _upstream/dev_. Right now, the history of your branch should look like the following, where it's just a series of commits from  _dev_, _origin/dev_, and _upstream/dev_.
	[[pics/GitLinearCommits.png]]
 6. Start the [code review process with CodeFlow](Code-Review-with-CodeFlow).  
    The diff should be generated from `origin/dev...NewWorkBranch`.

 7. To update your change with code review feedback or to sync down new changes, follow step 3 and 4, as you would before.

 8. Once you received sign off, perform instructions in step 4 to make sure your working branch is up to date and rebased properly.

 9. Squash and/or fix up any commits down to 1.  
		`git rebase -i dev`  
	    The goal here is to allow others to understand the change you make easier. The vast majority of the time, this means squashing all changes down to one commit, and just give it a nice detailed description -- (Think a change list that you "sd submit" in the source depot world). This will be the description that will live on in the main repo, so take out anything that's unnecessary. There may be occasions where it makes sense to have more than 1 sequential commits, but again, this should be the exception, and not the rule. Use your discretion.   
        Here's a link to a more detailed explanation of how to do interactive rebase -- [https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)
	
 10. Advance _dev_ so it now contains your commit, and push it upstream.  
    `git checkout dev`  
    `git merge NewWorkBranch`  
    `git push upstream dev`  
    `git push`  
    This is the equivalent of doing an "sd submit" in the source depot world. If your `git push upstream dev` fails due to your _dev_ branch being out of date with _upstream/dev_ (perhaps because another dev is submitting around the same time), fetch from upstream and rebase your change again prior to retrying:  
    `git fetch upstream`  
    `git rebase upstream/dev`  
    `<resolve any merge conflicts if needed>` 
    `git push upstream dev`  
    `git push`     
		
 11. Do the happy dance!
 
 12. Now it's time to clean up! Delete the branch locally:  
    `git branch -d NewWorkBranch`  
    
 13. You are ready to go back to step 1 and start your next change!!

### Side bar on stashing
At any point, if you need to do something (say, pull down the latest from upstream) but you have staged or unstaged changes, stash them away:  
	`git stash save "Description of the uncommitted changes"`  
	`<do your other thing>`  
When you're done, get back to the branch you want (it doesn't have to be the same as when you did the stash), and apply the stash.  
	`git stash pop`  
(pop will apply the last thing you stash and remove it from the list of stashes. Or you can do `git stash apply` instead if you want to have greater control or have multiple stashes that you want to apply out of order.)
