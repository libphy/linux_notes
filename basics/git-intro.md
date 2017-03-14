# Using Git

Git is a useful tool for version control, especially in collaborations with other developers. It is also an excellent way to showcase your projects and codes to your potential employer.

## Introduction to Git
If you're new to git, have a look at these resources:    
https://git-scm.com/book/en/v2    
https://git-scm.com/videos

You can find many git hosting services such as github.com or gitlab.com.
Github has a big user base in the opensource community. Gitlab offers free-tier private repository. There are many other awesome products. Have a look at popular services on git:    
https://www.git-tower.com/blog/git-hosting-services-compared/

## Setup a Git
You are free to use any git hosting service you prefer. If it's your first time using git, I recommend trying github, because it's the most popular, so you'll easily find someone's repository you can learn something from. Below is a guide how to setup github.
https://help.github.com/categories/bootcamp/
A shortcut to downloading Git: https://git-scm.com/downloads
If you already have Anaconda installed, you can also do in your cmd or bash
```bash
conda install git
```

## Forking someone's project repo
If you found an interesting project in someone's github, or you have a repo from your class, you can **fork** to your github account by clinking (while you're signed-in) the **fork** icon on the top of the project repo that you're trying to copy.

## Useful git commands
 
When you create a repository or forked from someone else in the github website, you will see a list of repositories in your github main page. A repository that's sitting online is called **remote repository** since it's hosted by their server. You will **clone** it to your computer, then the copied folder is called **local repository**.
Once you have your remote repository, go to your remote repository online, then find a green button that says "Clone or download". Open your bash/cmd window and cd into the folder location you want to set the local repository. Let's say, your git repo url is "https://github.com/myusername/test-project" (you can name it differently), then type
```bash
git clone https://github.com/myusername/test-project
```
It should create a folder "test-project" and download all the files in the remote (you may not have any file if you haven't created or uploaded in your remote repo). You can modify README.md or .gitignore file. If you haven't create .gitignore file, you can create one in the bash by `touch .gitignore`. In .gitignore, you can setup rules for ignoring when adding files to git. For example, if you have data files which is big and you don't want to upload them to remote repository, or you have certain file extensions that you don't want to upload you can list them in the .gitignore file.
```
(in .gitignore)

/data
*.csv
```
After you add/update your codes, you can check changed files by `git status`. You can manually add one by one using `git add <filename>` or add everything by `git add .`. What `git add` does is **staging** files before commit, and is different from uploading.    
Once you added files you want to upload, commit them with comments about the changes; for example,
```bash
git commit -m "updated mycode.py"
```
Then you can push (finally uploading step)
```bash
git push
```
You will be asked to type your username and password. Then it will be uploaded. Go to your remote repo and see how they changed.

You can also update files using web GUI. If you pull those changes in your local repo, you can do so by `git pull` in the folder.

#### Other useful commands
You can unstage files (or changes that made in the file): `git reset <file>`
When you remove a file and want to reflect it also in the remote repo: `git rm <file>`

#### Still confused?
Read more online resources: https://git-scm.com/docs   
I found this diagram helpful: http://files.zeroturnaround.com/pdf/zt_git_cheat_sheet.pdf
