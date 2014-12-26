#Git

##Start Git

###git install
* Windows
* Linux

###Git Manage Tool
SmartGitHg

###Git Start Example
* Create your first github repos
* Init your git in local :

 	`git init`

* Add files you want to commit : 

	`git add file/folder/*`  

* Commit what you add to git :

	`git commit -m  'comment'`

* Add your remote branch : 

	`git remote add origin https://github.com/username/reposname.git`

* Push your your commit to remote origin branch, supposing you are on master branch:

	`git push -u origin master`

## Git cmd
* create local branch :

	`git branch <branchName>`

* show current branch :

	`git branch`

* show git status :

	`git status`

* switch branch :

	`git checkout <branchName> `

* delete local branch :

	`git branch -d <branchName>`

* delete remote branch or tag :

	`git push origin :<branchName>` (Git v1.5.0) 	

or

	`git push origin --delete <branchName>` (Git v1.7.0)

* rename branch name:

	`git branch -m <oldname> <newname>`

or

	`git branch -m <newname>`(change current branch name)

* show git commit version log 

	`git log -n`(n : last n log)

* revert file or folder to special version :

	`git revert <commit_version_string>`

