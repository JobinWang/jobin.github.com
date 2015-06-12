#### Remove a commit
  撤销最新的一个commit,保留修改内容：git reset --soft HEAD^

  方法二：
    git rebase -i HEAD~2  在编辑器内删除commit
    force push: git push origin +master | git push --force


list the commits on your branch that have been made since the last commit on origin/master.

$ git log --oneline origin/master..my-new-feature
6c34529 My second commit with a tweak
889f452 My first commit


squash the commits into one using git rebase
git rebase -i origin/master


force the push by adding a + before the branch name
$ git push origin +my-new-feature

Delete the branch remotely
$ git push origin :my-new-feature

Delete the branch locally.
$ git branch -D my-new-feature




##########
git fetch master ?  给我我所没有的。

git pull (git fetch && git merge)


cherrypick: only one commit on another branch.

experiment


git fetch upstream
(master) git merge upstream/master  | git rebase upstream/master
(develop) git rebase master
          git rebase develop

gitref

Pro git

remotes:
  git pull == git fetch + git merge

  git pull --rebase = git fetch + git rebase #{remote}/master

  multiple remotes

git log --oneline --graph --decorate

git log branchA ^branchB

git log master ^origin/master  (after fetch)

##### my project
git remote add bb git@bitbucket.org:gotomk/fair-one.git
git push -u bb --all
git push -u bb --tags
