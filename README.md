# Submodule Quick Reference!
Let's say you have a repository with a bunch of folders/projects within it, and you would like to use some of those folders in a different repository. Sure, you could copy over the files manually, but `git` (1.8+) has a built-in method that's even better: `submodules`!

Why bother with submodules?
- Retains all previous git history of the files
- If the submodule needs to be updated for any reason, then all repos that use the submodule can easily receive the updates using some quick git commands
- Looks cool :D

## Turning a collection of folders into a submodule
### Step -1: Make a copy of your current repo folder!!!
So that the world doesn't end if you make any mistakes :)

### Step 0: Make sure you `cd` to the root folder of your repo
(this would be the folder that `git clone` put all of the files into, when you first cloned/initialized the repo)

### Step 1: Checkout the branch that you would like to branch your "submodule branch" from
```shell
git checkout <original branch>
```

### Step 2: Make a branch for your intended submodule and checkout to it
```shell
git checkout -b <submodule-branch-name>
```
Obviously change `<submodule-branch-name>` to whatever you want the branch's name to be.

### Step 3: Filter out files to keep and files to remove
The nice part about using git to filter is that the repo will retain all history on the files, even after you filter them.
```shell
git filter-branch -f --prune-empty --index-filter 'git rm --cached -r -q -- . ; git reset -q $GIT_COMMIT -- <folders and files to keep>'
```

Replace `<folders and files to keep>` with every file/folder name to keep, separated with spaces.
If you would like to apply this filter to all of the branches on the repo, then you can append `-- --all` to the end of the command.

For example, in [this branch](https://github.com/YaseenAlk/face_api_testing/tree/pythonfaceidhelper-submodule) that I made, I branched the submodule-branch from the [unity-helper-face-msgs](https://github.com/YaseenAlk/face_api_testing/tree/unity-helper-face-msgs) branch, and then I ran the following command:
```shell
git filter-branch -f --prune-empty --index-filter 'git rm --cached -r -q -- . ; git reset -q $GIT_COMMIT -- python/PythonFaceIDHelper LICENSE README.md .gitignore .vscode'
```
This example command kept the files `LICENSE`, `README.md`, and `.gitignore`, kept the folder `.vscode` as-is, and extracted + kept all of the contents within the folder `python/PythonFaceIDHelper`.

### Step 4: Delete any files that were skipped because of your `.gitignore`
Look through the branch's files and see if any unwanted files were kept. Make sure that the unwanted files were not accidentally included in the previous step. If they were not supposed to be included, then it's most likely because of your `.gitignore`. If this is the case, then you should be able to delete these files without changing the `git status`.

### Step 5: Adjust the `.gitignore` for your new branch
If any folders got moved around, then you might need to edit the contents of your `.gitignore`.

### Step 6: Push your newly-created branch!
Note that `git filter-branch` rewrites history, so you need to force push it.
```shell
git push -f
```
If it's a new branch, you may need to set the upstream first.

This branch can now be referenced as a submodule in other git repos!

### Making changes to this branch
You can still commit and push changes to this branch whenever you need to! After you've commited changes to the submodule'd branch, do the `Fetching and applying updates to the submodule` step listed below. 

---

## Using the "submodule branch" that you just made
When you follow the steps below, your repository-of-interest should end up with 2 new additions: a `.gitmodules` file and a reference to the submodule's folder. You will need to add/commit/push these files. Whenever you make changes in the repo that you are submoduling, you will need to fetch and apply the updates in your repository-of-interest (refer to the step below: `Fetching and applying updates to the submodule`); then, you need to add/commit/push the above files once again.

### Cloning the submodule
In the root folder of your repository-of-interest, run the following command:
```shell
git submodule add -b <submodule-branch-name> <remote repo URL> <path>
```
where `<submodule-branch-name>` is the branch made earlier, `<remote repo URL>` is the URL to the repository (same URL you clone with; ends in `.git`), and `<path>` is the directory to put the submodule into.

### Initializing the submodule
In the root folder of your repository-of-interest, run the following command:
```shell
git submodule update --init --recursive
```

### Fetching and applying updates to the submodule
```shell
git submodule update --remote --recursive
```
Note 1: if you have multiple submodules in your repository-of-interest, this command will fetch/update all of them.

Note 2: If you are finding that the submodule is not fetching the updates properly, try running `git pull` first, in the root folder of the repository-of-interest.

---
For more information on git submodules, check out this [third-party guide](https://www.activestate.com/blog/2014/05/getting-git-submodule-track-branch), this [guide](https://git-scm.com/book/en/v2/Git-Tools-Submodules) made by the documenters of git, and of course, the [official git documentation](https://git-scm.com/docs/git-submodule).

Thanks for reading!
---
