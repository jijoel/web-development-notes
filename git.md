git
================

* [Basic Usage](#basic)
* [Commits](#commits)
* [Branches](#branch)
* [Stashes](#stash)
* [Tagging](#tags)
* [Working with remote branches](#remote)
* [Forks and upstream changes](#forks)
* [Troubleshooting](#troubleshooting)
* [Configuration](#config)
* [Strategy for working with branches](#strategy)
* [Working with Continuous Integration (CI) Tools](#ci-tools)


Basic Usage <a name="basic" />
--------------------------------

    man gittutorial

To create a new local repository:

    git init      // Initialize a new git repository
    git remote add origin https://github.com/jijoel/project.git

To copy a remote repository to the local machine:

    git clone https://github.com/jijoel/project.git local_project_dir

To copy a specific branch of a remote repository to the local machine in git 1.7.10 or later:

    git clone -b develop --single-branch https://github.com/laravel/laravel.git laravel-develop

To copy a specific branch of a remote repository to the local machine in git 1.7.9 or prior:

    mkdir repo
    git init
    git fetch url-to-repo branchname:refs/remotes/origin/branchname
    

To use git to send files to a remote repository:

    git add *     // Add all files in the path on the local machine to the track list
    git commit    // Commits changes to the local repository
    git push      // Pushes changes to the remote repository
    
To pull working files from the repository (eg, to get updates from other people or other places that have been pushed to the remote repository)

    git pull      // Pull (and merge) changes from the remote repository

or

    git fetch
    git merge

To see what has changed in the working tree

    git status

Pull data from a remote repository, wiping out any local changes:

    git reset --hard HEAD                   (resets to the current HEAD)
    git pull

If you have local commits that you'd also like to wipe out:

    git reset --hard origin/master          (resets to the current origin/master commit)
    git pull
    
To rewind a branch that you have pushed to a remote repository (on the master branch); in this case, rewind a master branch and put the changes into a new branch:

    git push -f origin a3f0442_id_of_prior_commit:master   // rewinds the remote
    git reset a3f0442                                      // resets the local to that spot
    git stash
    git checkout -b branch_name                            // switch to new branch
    git stash pop                                          // brings back files in new branch

Move recent commits from a branch to a new branch:

    git branch newbranch
    git reset --hard HEAD~3    // Go back 3 commits. You *will* lose uncommitted work.
    git checkout newbranch

Move recent commits from a branch to an existing branch:

    git checkout existingbranch // branch you want to move commits to
    git merge master            // branch you want to move commits from
    git checkout master
    git reset --hard HEAD~3    // Go back 3 commits. You *will* lose uncommitted work.
    git checkout existingbranch


Stop tracking a file that is currently tracked:

    git rm --cached file

Remove untracked files:

    git clean -f
    
git add just adds changes (including in new files)

    git add .             (adds all files in directory)
    git commit -a         (stages all tracked files with modifications)
    git commit -m '<msg>' (commit everything with a comment; don't open editor)

Use a .gitignore file in a directory to ignore files and folders. List each type of file to be ignored. If you need to track specific files among ignored files, use a ! to disregard the ignore, and track those files:

    temp/*.txt              (ignore every .txt file in temp)
    !temp/important.txt     (track important.txt)

Parts of git:

    working directory       uncompressed files
    staging area (index)    lets you choose what to commit; 
                            good encapsulated messages
    git repository          

git diff is used to see changes that have been made over time.

    git diff              difference between working directory and staging area
    git diff --staged     shows difference between committed and staged
    git diff HEAD         difference between working dir and HEAD (repository)
    git diff <hash> <file>  changes to <file> since <commit hash>
    git diff --name-only  <hash>  files changed since <hash>

git log shows a history of commits over time

    git log               basic history of commits over time
    git log --stat        includes statistics  (files changed, insertions/deletions)
    git log --oneline     Shows each commit on one line  (hash, title)
    git log --graph       Shows graph of the current branch
    git log --all         Shows changes to all branches
    git log --decorate    Shows which branch the commit points to
    git log --oneline --graph --all --decorate   shows one-line graph of all branches
    git log --pretty='x'  Shows log with formatted output (which can be used elsewhere)
                          (see http://git-scm.com/docs/git-log for params) Examples:
                            %h   abbreviated hash
                            %t   abbreviated tree hash
                            %an  author name
                            %ad  author date
                            %ar  author date (relative)
                            %cn  committer name
                            %s   subject (commit message)
    git log --grep='x'    Limit output to those with log message matching regex

    alias gl='git log --oneline --graph --all --decorate'

    

Commits<a name="commits" />
-------------------------------
A commit is basically a snapshot of changes at a given time. It includes a message about what has changed. To make a commit:

    git add/rm <file(s)>            // Tell git what files to commit
    git commit -m 'message'         // Take a snapshot of those changes

You can commit all of the changed files (git add .), or select specific related things. The latter is much more useful, because you can give descriptive names about what has changed, and see it in the log.

To modify the previous commit (if, for instance, you realize another file should have been added), make changes (eg, git add/rm), then enter:

    git commit --amend

You can arbitrarily move to any commit in the log, see how things were then, and even create a new branch from that spot. To move to a separate point in the log, given these commits:

* 5d632ba (HEAD, origin/master, master) files to test
* 5194b7e initial commit

To switch back to the initial commit, as it was before any changes were made:

    git reset 5194b7e               // moves back to the initial commit
    git checkout -- .               // unstages changes to initial commit 
                                    // (eg, modified files)
    git clean -f -d                 // removes any new files and directories 
                                    // (anything that has not yet been added)

To alter the last commit, stage the changes, then use:

    git commit --amend

To see what has changed in a given commit:
    
    git show 5d632ba                    // shows all changes
    git show --name-only 5d632ba        // shows names of files changed 

To get only a list of files changed, with no header information, (eg, something that could be used to list files for a script, etc.), either of these will work: 

    git show --pretty="format:" --name-only 5d632ba
    git diff-tree --no-commit-id --name-only -r 5d632ba

You can apply a specific commit on another branch to your current branch:

    git cherry-pick 5d632ba

You can squash a commit (turn several commits into a single commit) by moving back to the base commit, then rebasing with 'squash'.

    * d2c6aef (HEAD, origin/update-to-pheanstalk-3.0.0, update-to-pheanstalk-3.0.0)
    * edccb7c make code a little cleaner
    * deed004 update to pheanstalk 3.0.0 (PSR-4)
    * 1e7c42e (origin/master, origin/HEAD, master) ...

    git rebase -i master
    (a list comes up; select 'pick' for commits to leave intact; 'squash' for commits to squash into those)
    git push --force  (forces the squashed commits to github)


Branches <a name="branch" />
-----------------------------------

Master branch is there by default. Work on new, experimental features in new branches. You can switch branches at any time. When you commit, it will commit to the active branch.

    git branch              shows list of branches
    git branch <name>       create a new branch
    git branch -m <o> <n>   renames old branch <o> to new name <n>
    git branch -d <name>    delete the named branch
    git checkout <name>     swich to the named branch
    git checkout -b <name>  create and switch to a new branch
    git merge <name>        merge the named branch with the active branch
    git rebase <name>       rewind HEAD to branch point, apply branch changes, 
                            then replay additional changes to HEAD
                            (makes the log linear, so it looks like there was no branching)

    git push -u origin <name>   Push latest commit from branch <name> to origin repository (github)
                                (create the origin repository if it doesn't already exist)
    

Sometimes conflicts come up when merging files. Both branches might have merged the file.

    git merge --no-ff branch2           // perform the merge; you will be told if there are conflicts

    git merge --abort                   // aborts the merge
    git reset --merged                  // older syntax for aborting a merge

    git checkout --ours PATH/FILE       // use the version of the file in our branch
    git checkout --theirs PATH/FILE     // use the version of the file in the other branch




Stashes <a name="stash" />
-------------------------

You can stash changes to pause what you're working on, switch to something else, then resume when you're ready.

    git add .           adds changes to the index
    git stash           Write those changes to a stash, return to original working state
    git stash apply     Bring your stashed work back
    git shash pop       Bring your stashed work back (and delete the stash)
    git stash list      List all stashes
    git shash drop <id> Delete a stash (use after applying it)
    git stash clear     Delete all stashes



Tagging <a name="tags" />
---------------------------
You can tag specific commits as being important. 
NOTE: Make the commit, then add the tag.

    git tag                                 List all tags
    git tag -l 'v1.4.2.*'                   List all tags under v1.4.2.x
    git tag -a v1.4 -m 'my version 1.4'     Create tag with a message
    git tag -s ...                          Creates a gpg encrypted tag
    git tag -v ...                          Verifies the gpg encrypted tag
    git push origin [tagname]               Push a tag to a server (eg, github)
    git push origin --tags                  Push all tags to remote server



Working with remote branches <a name="remote" />
-------------------------------------------------

    git remote                              shows all remote branches
    git remote -v                           shows remote branches (with url)
    git remote add [alias] [url]            add a new remote repository
    git remote rm [alias]                   remove (delete) the local pointer to the remote repository
    git remote rename [old] [new-alias]     rename old-alias to new-alias
    git remote set-url                      changes the url of the given alias

    git fetch [remote-name]                 fetch an update from remote repository
    git pull [remote-name]                  fetch and merge update from remote repository into active branch
    git push [remote-name] [branch-name]    push changes from local branch-name to remote remote-name
      (eg, git push origin master)
    git push -u origin mybranch             push changes (create tracking) for remote branch
    git remote show [remote-name]           shows data about the remote (URL, tracked branches)
    git push origin --delete [branch]       Deletes a branch from the origin (remote site)
    git push origin :the_remote_branch      Another way to delete a branch from origin
    git branch -d the_local_branch          Delete a branch from the local repository

    git push [remotename] [localbranch]:[remotebranch]  pushes from localbranch to remotebranch on remotename

    git branch -a                           shows all branches available on the remote repository
    git checkout -b dev origin/dev          checkout the dev remote branch (create a local tracker)
    git fetch [remote] [branch]             pulls a branch from remote to local
    git branch -rd origin/branch            deletes the local reference to a (deleted) branch

    git remote update
    git pull --all

    git rebase -p origin/dev                rebases the current branch to origin/dev
                                            (it could have been origin/master, or something else)

To squash multiple commits in a feature branch into a single commit, reset the feature branch changes in the master and commit everything again:

    git checkout master
    git fetch       # this may be necessary (depending on your git config) to receive updates on origin/master
    git pull

    # Merge the feature branch into the master branch.
    git merge feature_branch

    # Reset the master branch to origin's state.
    git reset origin/master

    # Git now considers all changes as unstaged changes.
    # We can add these changes as one commit.
    # Adding . will also add untracked files.
    git add --all
    git commit

    git branch -d feature_branch

NOTE: Only do this with feature branches you intend to delete. After merging, you will not be able to work on that branch any more.

                                        
Forks and upstream changes <a name="forks" />
---------------------------------------------
You can fork a repository on github. This will create your own personal version of this repository, in your own github account. To make a pull request (a request to have your code pulled into the master repository):

1. Fork the repository to your own github account
2. Clone your version to your local machine               git clone ...
3. Create a new branch for your feature or bug fix        git checkout -b bug/... 
4. Make the changes
5. Commit the changes (git add; git commit)
6. Push your branch to your github repo  (git push origin my/branch)
7. Look at our branch on github; make sure everything looks right
8. Click Pull Request button on github; 
      Make sure to submit changes to the right place (dev vs master, etc.)
      write comments so maintainer can see what you changed
      send pull request

To update your repository from the master repository, you can use upstream changes:

    git remote add upstream https://github.com/vendor/master-repository-name.git

    git fetch upstream
    # Fetches any new changes from the original repository

    git merge upstream/master
    # Merges any changes fetched into your working files



                                        
Troubleshooting <a name="troubleshooting" />
---------------------------------------------------

Error: You asked me to pull without telling me which branch you want to merge with...

git remote show origin    shows local vs origin branches, in this format:

    * remote origin
      Fetch URL: https://github.com/jijoel/project.git
      Push  URL: https://github.com/jijoel/project.git
      HEAD branch: master
      Remote branches:
        autobuild tracked
        dev       tracked
        master    tracked
      Local branches configured for 'git pull':
        dev    merges with remote dev
        master merges with remote master
      Local refs configured for 'git push':
        autobuild pushes to autobuild (local out of date)
        dev       pushes to dev       (up to date)
        master    pushes to master    (up to date)

In this particular case, autobuild is set to push, but not to pull. To fix it:

    git branch --set-upstream autobuild origin/autobuild



Configuration <a name="config" />
-------------------------------------------------------
Use the nano editor for commits

    git config --global core.editor "nano"
    
Syntax highlighting in nano
http://milianw.de/code-snippets/git-commit-message-highlighting-in-nano

Color output:

    git config --global color.ui true

Configuration information is stored in these files:

    ~/.gitconfig          (global for user)
    project/.git/config   (local to project)



Strategy for working with branches <a name="strategy" />
---------------------------------------------------------
Based on:
http://nvie.com/posts/a-successful-git-branching-model/

Branching Strategy:
We want several types of branches:

    master:     latest live/production version
    hotfix:         critical bug fixes, etc.; send directly to master and dev when completed 
    release:        once major features implemented, feature freeze. Bug fixes, and when good send to master
    dev:        integration branch; latest delivered development changes
    feature:        specific features for the next (or future) release. As feature completed, send to release 

Feature Branch:

    creating:
    $ git checkout -b myfeature dev         // switch to new myfeature branch, forked from dev
    $ git push origin myfeature:myfeature   // pushes from local machine to origin 
    $ git branch -a                         // shows all branches available on the remote repository
    $ git checkout -b dev origin/dev        // checkout the dev remote branch (create a local tracker)

    incorporating:
    $ git checkout dev                      // check out dev on the local machine
    $ git merge --no-ff myfeature           // can also include -m 'message' for non-default message
    $ (or) git rebase myfeature             // alternative: import without intermediate brach steps
    $ git branch -d myfeature               // delete the myfeature branch from local machine
    $ git push origin --delete myfeature    // delete the myfeature branch from remote
    $ git push origin dev                   // push the merged branch to the remote dev branch
    $ git branch -rd origin/branch          // on other computer: deletes local reference to branch

Release Branch:

    creating release-*:
    $ git checkout -b release-1.2 dev
    $ echo 1.2 > version.txt             // or some other way to change the version number
    $ git commit -a -m "Bumped version number to 1.2"

    incorporating into master, then removing:
    $ git checkout master
    $ git merge --no-ff release-1.2
    $ git tag -a 1.2
    $ git checkout dev
    $ git merge --no-ff release-1.2
    $ git branch -d release-1.2
    $ git push origin --delete release-1.2

Hotfix Branch:

    creating hotfix-*:
    $ git checkout -b hotfix-1.2.1 master
    $ echo 1.2.1 > version.txt
    $ git commit -a -m "Bumped version number to 1.2.1"

    Fixing the bug:
    $ git commit -m "Fixed severe production problem"

    Incorporate changes to master and dev:
    $ git checkout master
    $ git merge --no-ff hotfix-1.2.1
    $ git tag -a 1.2.1
    $ git checkout dev
    $ git merge --no-ff hotfix-1.2.1
    $ git branch -d hotfix-1.2.1
    $ git push origin --delete hotfix-1.2.1


Working with Continuous Integration (CI) tools <a name="ci-tools">
-------------------------------------------------------------------
There's a clean way to work with Continuous Integration tools, like [travis](travis-ci.org), [coveralls](coveralls.io), and [scrutinizer](scrutinizer-ci.com). They will scan pull requests, so you can see if everything is working BEFORE merging it with master.

