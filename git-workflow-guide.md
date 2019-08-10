---
title: How to work with git - An overview of git workflows
published: false
description: 
tags: git
---

> If you're here looking for a recommendation on what workflow to use, I strongly support the [recommendation](https://docs.microsoft.com/en-us/azure/devops/repos/git/git-branching-guidance?view=azure-devops) Microsoft hands out to their customers.

Git workflows differ from dev team to dev team and from git server to git server (Github, Gitlab, etc.). Git as a technology is so solid and flexible that people have found multiple viable workflows. Here is an extensive overview. It is assumed that you know git.

> I plan on updating this post based on your feedback. So please, if you think something's wrong or missing, leave a comment or contact me!

## Starting point: Master-only development

> You are the only person working on the project.

If you are the only person working on a project you can work on the _master_ branch directly (commit to it and push to the remote frequently). This workflow is unfit for serious/professional software projects. For one-person-projects however, I recommend this approach. It keeps the git-overhead low allowing you to develop with high velocity.

If you work on multiple things at once, for example if you notice a bug while you are working on a feature and decide to fix the bug before completing the feature, you should fall back to using branches to separate the different tasks (see _topic branches_).

While we're on the topic of the master branch: The _master_ branch in this article refers to the branch on which the main development is happening or being integrated. It's the branch releases are branched off of. Some teams use a seperate _develop_ branch for main development. The _Git Flow_ workflow also recommends this. In these workflows the branch named _master_ serves another purpose. But that is really rare so when I say 'master' in this article I mean the main development branch.

## Using a git server

> Problem: You want to share your repository with your team or other people.



## Feature / topic branches

> Problem: Team members are working on several unrelated tasks. Master-only development leads to a loss of oversight. Everyone is stepping on each other's feet. `master` contains unfinished work.

Teams of >1 developers benefit from using git in more sophisticated ways. Whenever you start working on some changes (be it a new feature or a fix) you should create a new branch based on the current version of master. After finishing work on such a branch it is merged back into master and deleted. This is so common, I think every team is doing this.

> Branch for __errythang!__

The reason for working on separate branches is, first of all, to cleanly separate simultaneous changes, and secondly, to separate unfinished work from the stable master branch. The most popular name for these branches is __feature branch__, although the term __topic branch__ is actually more accurate since not only new features should be coded in separate branches.

TODO
[](https://www.atlassian.com/blog/git/git-team-workflows-merge-or-rebase)

## Tier 2: Pull Requests and code reviews

Pull Requests is the big innovation that GitHub brought to the git table. They are like a forum thread in which code changes are discussed. PRs are created when a branch is ready to be merged into `master` or when you push some code that you want to merge later but already want feedback on. It displays the changes between the to-be-merged branch and the target branch you choose (`master` most of the time). It is possible for team members to discuss and evaluate the new code. Reviewers can request changes. It is even possible to make a comment on a particular  code line in the web UI directly.

There are two general ways of working with PRs:

1. All developers on a team have access to one and the same repository.
    - This is especially common for professional teams.
    - Team members work in topic branches, commit and push frequently and when the time comes they open a PR for the changes. Somebody else reviews the code (many teams even have a policy that two reviewers need to approve) and when everything is resolved the PR is accepted and thereby the branch is merged and the PR closed.
    - For this setup it is common to have some restrictions set up for some branches. Example 1: `master` is write-protected so that only senior devs can push to them. Example 2: `master` can only be updated through accepted PRs where accepting PRs is only allowed for seniors.
    - Sometimes there are branches for which the code gets automatically deployed. This is known as continuous integration or continuous deployment and is a huge topic. Deployment can be to the production environment directly or to a testing or 'staging' environment where the code is tested for regressions first.
2. Only core developers can push to the main repository.
    - This is the common form of collaboration for open source projects. Sometimes there is even only one person - the maintainer of the project - who can modify the main repo.
    - The way people contribute to such projects is by a process called _forking_. Forking is a feature provided by the git hoster. It clones the entire git repo to your personal user account. You have full control over that cloned repository. [a]
    - You will have to add both repos as remotes on your workstation. This way, you take the most up-to-date code from the original repo and push your topic branches to your cloned repo.
    - If you are on the web page of the original repo and you have a fork of that repo you can submit a PR to the original repo which, if accepted, will result in a pull of a branch from your fork to the original repo.
    - This process is where the name _Pull_ Request comes from. The result of a PR as just described is an actual pull whereas a PR in the previous scenario is a mere merge. That is why PRs are called "Merge Requests" in Gitlab.

The essential difference between these two appproaches is that in the latter the maintainer(s) are in full control of the official repo. If anyone could push to any repo, they would quickly devolve into mess. 

[a] [GitHub's guide on forking](https://guides.github.com/activities/forking/)

#### Clean history with rebase before opening PR

See also: [Anatomy of a perfect Pull Request](https://opensource.com/article/18/6/anatomy-perfect-pull-request)

[Linus Torvalds on _clean history_](https://lwn.net/Articles/328438/)

#### Avoiding merge conflicts

Sometimes there are merge conflicts and thus your PR can't be accepted. This happens when the master branch advanced in the meantime while you were working on your topic branch and both branches changed overlapping lines of code. The PR web interface will tell you that there are conflicts and that the branch can't be merged.

There are two ways to  

#### Allow / prohibit ff



## Tier 3: Merge policies

It is very common to configure the git server in such a way that release branches and `master` are protected. I.e. only specific persons can alter it, for example, or these branches can only be modified through accepted PRs and who can accept PRs.

When PRs are the preferred way to drive development, the question arises who is responsible for merging changes. There are two options:

1. It's either the seniors of the project. They merge the change manually making sure and taking responsibility that the code is integrated without breakage. For that they either resolve conflicts locally on their machine and push the result or they use the web interface for merging if the git server provides one.

2. It's the person who submits the PR.

The rest of this section goes into detail about option 2.

For merge policies or PR-acceptance-criteria there are again so many variations.

#### Squash merge

For one, some teams have the habit of squash-merging (`merge --squash`). The goal of this is to have a more compact git history. The thought process there is "if a topic branch is one atomic functional change then why should the commit history be bloated with multiple commits?"

#### Fast-forward only / No Fast-forward

Some teams prefer to have a linear history, where there are no branches and merges visible. 

## Tier 4: Long-lived topic branches

Topic branches should be short-lived. This means that ideally only one or a few days should pass between branching and merging back. If branches live longer, problems arise from the fact that potentially the code changes on master and a divergence emerges between the current master codebase and the version on which you created your branch. When merging a branch back sometimes the code you adjusted to integrate your new feature has changed on master since you branched off. That means your merge can't be applied. You need to readjust the new code to facilitate the feature.

- Ways to prevent long-lived branches:
    - Reduce the scope of the branch/issue (try to split the problem you are trying to solve into smaller problems)
    - Use _feature toggles_ [[1]](https://martinfowler.com/articles/feature-toggles.html) [[2]](https://martinfowler.com/articles/feature-toggles.html) ... TL;DR: Hide the new code behind `if` statements. Optionally, make the toggle a config option. With this approach the new code resides next to the old and both evolve close together.
    - Use [branch by abstraction](https://trunkbaseddevelopment.com/branch-by-abstraction/), a variant of feature toggles where you first introduce a new abstraction that wraps the part of the code you want to change. Then you implement the new code as an implementation of that abstraction while the rest of the team uses the old implementation 

## Tier 5: Release management

How you handle releases depends on whether you have to support multiple software releases. If you always only release one version and then develop the next version, then simply adding __tags__ to the master branch is sufficient.

If you have to support multiple releases, create a __release branch__ for every release. On this branch you integrate bugfixes. You also prepare and release updates from it. 

Sometimes you detect a bug that affects multiple releases and perhaps also your master. There are different approaches how to tackle this situation.

- Git Flow recommends creating a branch from the release and merging the fixing commits into every affected release and the develop branch.

- You can always implement the fix for every release manually

- When you have fixed the issue in one branch sometimes you can cherry-pick some of the commits onto other branches

## Specific Workflows

- Git flow + discussion
    - https://www.reddit.com/r/git/comments/bkvo0h/lpt_dont_go_overboard_with_your_branching_strategy/
    - https://www.reddit.com/r/programming/comments/a8n44j/a_successful_git_branching_model/

- <https://trunkbaseddevelopment.com/> - This is the basis for many popular workflow. Its website is very informativ.
    - [Release Flow](https://docs.microsoft.com/en-us/azure/devops/learn/devops-at-microsoft/release-flow) is Microsoft's git workflow. It's a variation of trunk-based development.
    - [GitHub Flow](https://guides.github.com/introduction/flow/). Also, to find out how to contribute to projects, read about [forking](https://guides.github.com/activities/forking/).

- E-Mail based (Linux)
