## Git/GitHub Workflows for R Users (DRAFT)

Joyce Robbins
12/7/18

Note: This is an attempt to outline beginner workflows in as succinct a manner as possible for reviewers who are familiar with Git/GitHub and can provide feedback / suggest improvements to the workflows themselves, rather than the how-to. As such, I have not provided step-by-step, tutorial style instructions. Eventually the ideas here will all be incorporated into a tutorial presentation, a draft of which is available here: [GitHubWorkflow.pdf](GitHubWorkflow.pdf).

Thank you for your help!

Beginner workflows in increasing order of difficulty:

### 1. Share work on GitHub (No git required)

Situation: You have files that you want to share.

Mantra: WORK, UPLOAD, REPEAT

Method:  
1. Create a repo on GitHub.  
2. Add files on GitHub via the "Add files via upload" button.

### 2. Work on local master branch

Situation: You are the only contributor to your project. You need to be able to work locally and sync with GitHub.

Mantra: PULL, WORK, COMMIT, PUSH, REPEAT

Method:  
1. Create a repo on GitHub.  
2. Clone it while creating a new RStudio project.  
3. Begin with pulling, then work, commit, push.   Everything is done with RStudio buttons.  

### 3. Work on local new branch (your project)

Situation: You are working on a project with other collaborators that resides on your GitHub repo. You have agreed that pull requests will not be merged by the author.

Mantra: PULL, BRANCH, WORK, COMMIT, PUSH, SUBMIT PULL REQUEST, DELETE BRANCH, REPEAT

Method:  
1. Same as above but work starts with a new branch (RStudio button)  
2. After pushing new work, a pull request is submitted on GitHub.  
3. Once the PR is merged, the remote branch is deleted on GitHub (button) and locally with `git branch -d <branch-name>`. Stop tracking deleted branch with `git fetch -p`.  

### 4. Work on new local branch (someone else's project, you are a collaborator)  

*to be added*

### 5. Work on new local branch (someone else's project, you are not a collaborator)  

*to be added*


