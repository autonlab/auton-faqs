# git 101
## Goal
This doc's purpose is to get users up to speed with comfortably branching from a project, making a feature addition, squashing their multiple commits to clean up project history, then rebasing atop main.

## Intended Takeaways
- Basic knowledge of `git`'s bread and butter commands, granting a user the ability to comfortably:
  - Check `diff` between local repository and remote repository
  - `checkout` working branch from main to work on feature addition 
  - `squash` your messy commit history into one definite, blameable peace of work*
  - `rebase` your completed feature addition back atop main
- Understanding of underlying repository state and how each of the above commands alter or show views of that state

\* **note** this isn't a standalone git command but rather an option one may use while `merge`ing or `rebase`ing

## Shortcomings
- GUI 101, if we can add a how-to for that we'd remove more obstacles to prospective git users


# Modifying a readme to a sample repository
## 0. Setup
### Fork sample repository
First, go to this [sample repo](https://github.com/githubtraining/hellogitworld) and [fork](https://docs.github.com/en/get-started/quickstart/fork-a-repo) it
-  Navigate to url above, click the fork button at top right-hand corner

### Clone onto local machine
For me, this was via:

```git clone https://github.com/InjuredRoman/hellogitworld.git```

Then navigate into repository
- `cd hellogitworld`

## 1. `checkout` new branch
``` git checkout -b addingToReadme ```

**Note** the `-b` above is only to **create a new branch**, so on future switches between *existing* branches, the `-b` isn't necessary, we can just `git checkout addingToReadme`

## 2. `add` your work
Every time we make a change to a file (or add one) and wish to commit our intermediate changes before continuing, the process is the same: 
- `git add {the files you've changed}`
- **optional:** `git status`  to verify the changes you wish to include in this commit
- `git commit -m "{some message explaining what you've done}"`
- `git push` your changes to remote branch

## 3. `rebase` our work atop main
**What's rebase?** Check [docs](https://git-scm.com/docs/git-rebase), scroll to pictures a wee bit down

**Why rebase instead of merge?** I like it because it simplifies git branch history for future review and blaming, *assuming* informative squashed commit messages, but my opinion is just that so figure out [what's best for you](https://www.atlassian.com/git/tutorials/merging-vs-rebasing) :)

The skinny here is that we'll be squashing our multiple changes into one so that the commit history on main is clean, with one feature addition per commit rather than one feature addition followed by 3 or 4 small changes.
### Interactive rebase
From our `addingToReadme` branch we'll rebase onto main with the `--interactive` option, which will allow us to squash our multiple commits into one and edit our single commit message
- ``` git rebase -i main ```
- This will open a prompt with all your commits - in front of all but one replace `pick` with `squash` or `s` (they're equivalent) 
- Once you save and exit, comment out all lines but one of commit message and replace the non-commented line with the message you'd like for the combination of commits


