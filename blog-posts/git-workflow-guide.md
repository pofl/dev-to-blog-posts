---
title: How to work with git - An overview of git workflows
published: false
description: 
tags: git
---

> If you're here looking for a recommendation on what workflow to use, I strongly support the [recommendation](https://docs.microsoft.com/en-us/azure/devops/repos/git/git-branching-guidance?view=azure-devops) Microsoft hands out to their customers.

Git workflows differ from dev team to dev team and from git server to git server (Github, Gitlab, etc.). Git as a technology is so solid and flexible that people have found multiple viable workflows. Here is an extensive overview. It is assumed that you know git.

> I plan on updating this post based on your feedback. So please, if you think something's wrong or missing, or if a link becomes out-dated leave a comment or contact me!

Another guide about the same topic: <https://www.codingblocks.net/podcast/comparing-git-workflows/>

## Forking vs Centralized Workflow

##### The centralized workflow - i.e. all developers on a team have read and write access to a shared repo.

This is especially common for professional teams. Team members fetch from the central repo, work in topic branches and push their branches to the central repo. Code is reviewed by other devs in Pull Requests. Accepted PRs merge the branch into `master`.

##### The forking workflow - i.e. only core developers can change the repo.

This is the most common form of collaboration for open source projects. The goal of this workflow is that the publicly visible repository sustains a high quality. Only trusted people (i.e. the maintainers of a project) can modify it. The way non-maintainers contribute to such projects is by a process called _forking_. It clones the entire git repo to your personal user account.

Say, if I want to make a contribution to some open source project like the programming language Scala. I would go to the official repo github.com/scala/scala and click on the _Fork_ button. That would create a copy of that repo under github.com/pofl/scala. I would have full control over that clone. I can push to it how ever I want.

When I want to submit a contribution to the official repo, I will first push my changes to my fork. Then I can create a Pull Request in the upstream project. Such a PR is essentially a request to merge a branch in my repo into the upstream repo. It's called Pull Request instead of Merge Request (which it is called in GitLab btw) simply because 'pull' is the gitnically correct term for fetching a branch from a remote and merging it into HEAD.

The day-to-day workflow is:
- Fork the repo.
- Clone your fork to your computer (`origin`).
- Add the official repo as a remote (`upstream`).
- Fetch `upstream`.
- Create a new branch from `upstream/master`.
- When you're done, push it to `origin/topic-branch`.
- Then you can create a PR in the upstream project where you can request that your topic-branch be merged as if it was a branch in the upstream repo.
- Resolve all requested changes that the maintainers pose.
- The maintainers accept the PR and the branch is pulled form your fork into `upstream`.

[GitHub's guide on forking](https://guides.github.com/activities/forking/)

## Git history hygiene

Developers strive for clean code. Clean and consistent code is easier and faster to work with. To accomplish that teams author a code style guide and enforce it. The addition of Git as a tool to your development setup brings with it a whole new dimension of things that need to be kept clean. This section explains these things.

If you are following the developer community on the internet you'll come across a few articles and blog posts talking about the git history. Many people have string opinions about what a clean history is. Curiously, when people say Git history what they actually mean most of the time is the shape of the commit graph of `master` and other permanent branches. We'll cover that in this section. What we'll also cover is under what conditions a commit is 'clean'.

### Linearity of Git history

When you merge a branch into another, usually a merge commit is created but not always. Git defaults to not create a merge commit if it's not necessary (this is called [fast-forward merging](http://marklodato.github.io/visual-git-guide/index-en.html#merge)). A merge is what happens anytime a code contribution gets accepted into `master`, and that's the point where some people go religious. Luckily, most people just accept the default behavior and live with it.

##### Linear history

There is one philosophical tribe of people who find that the commit graph should be linear. They want to be able to have a look at the Git history and immediately be able to see what has been happening and who's been doing what. With a linear history all you see is the unnoisy `master` and the feature branches that are in progress. A linear history is achieved by rebasing the feature branch onto master before merging/pulling and enforcing fast-forward merges eliminating merge commits.

The downside of this approach is that rebasing is quite an overhead as it involves more steps than merging. It also becomes limiting when many people work on the same repo, as there will be situations where devs will have to re-rebase because master changed before the PR got reviewed. The advantage is that you really do get a cleaner look at you history.

[Here](https://stackoverflow.com/questions/15316601/in-what-cases-could-git-pull-be-harmful) is a link that discusses and explains this philosphy further. [Here](https://gitlab.gnome.org/GNOME/mutter/-/network/master) you can see the Git history of a project following the this philosophy.

##### Maximum historic information

The other extreme is one where people disable fast-forward merges and enforce the creation of merge commits. The philosophy behind this is that the history is a source of information and the fact that code was created on feature branches would be denied when you create a fast-forward merge. "A linear history is a lie" is their saying.

[Here](https://www.atlassian.com/blog/git/git-team-workflows-merge-or-rebase) is an article comparing the two approaches with favoring the latter. [Here](https://gitlab.com/gitlab-com/www-gitlab-com/-/network/master)'s a project following the second approach. [Here](https://www.youtube.com/watch?v=3XjeYfH2BBI&t=426) is (a section of a) talk proclaiming the second philosophy.

##### Squash merge

This is another approach how to keep the history linear. [This article](https://docs.microsoft.com/en-us/azure/devops/repos/git/merging-with-squash) explains everything.

TL;DR a squash merge is a merge where the entire merge source branch is condensed to a single commit which is created on top of the merge target branch without creating a merge commit. Some teams like to do this because it keeps the Git history slim.

### Permissions

Many Git servers allow restricting permissions to some extent. Here are a few use cases:

- Suppose you have a branch called `live` or `prod` which, on every new commit, gets automatically deployed into the live production environment that is used by your users. You probably don't want everyone to be able to make changes on these branches.
- Let's say you want to maintain a very high code quality in master, it should not be possible for people to bypass the code quality assurance measurements.

So in both examples what you want to achieve is, basically, to restrict write permissions on certain branches. Here are a few ways some Git hosting solutions allow you to do this:

- Pretty much every one of them allows you to protect branches against changes like pushing or merging. You are able to grant this permission to a select set of users.
- Pretty much every one of them allows you to make it a requirement that some CI/CD pipeline succeeds before PRs can be acctepted.
- Some of them enable you to make it so that some branches can only be modified by accepted PRs. This effectively makes code review mandatory and is therefore very cool. It is also sometimes possible to require the approval of PRs by specific persons.

Here are some links that go into more detail: [GitLab on the benefits of branch protection](https://about.gitlab.com/blog/2014/11/26/keeping-your-code-protected/), [GitLab documentation of allowing branches to be modified only through PRs](https://docs.gitlab.com/12.3/ee/user/project/protected_branches.html#using-the-allowed-to-merge-and-allowed-to-push-settings) (Merge Requests in the GitLab language) and [GitHub on their branch protection features](https://help.github.com/en/enterprise/2.18/user/articles/about-protected-branches)

### Clean commits

First, a link: [Anatomy of a perfect Pull Request](https://opensource.com/article/18/6/anatomy-perfect-pull-request)

Many teams have rules that determine under which conditions the commit history is 'clean'. Firstly many teams have rules for what the commit message should look like. One very popular set of rules is [this](https://chris.beams.io/posts/git-commit/). Another [write-up](https://wiki.openstack.org/wiki/GitCommitMessages#Summary_of_Git_commit_message_structure) of rules.

Besides those I've heard of projects which mandate that the commit title starts with the name of the module or the file in which the changes were made (in such a case there is usually a not so restrictive rule on title length). Some teams also mandate that there must always be a body.

One very popular rule is: for every commit compilation has to succeed and the code needs to be free of errors (minimum: tests have to pass). The reason for this is that if somehow at one point a regression emerges, you can easily find out what commit caused it with `git bisect` ([Link1](https://americanexpress.io/git-bisect/), [Link2](https://stackoverflow.com/questions/4713088/how-to-use-git-bisect)).

The reason why many teams have rules to enforce a clean history is to simplify code reviews. When all commit messages look the same, they can be processed faster. When all commits have a body detailing the changes the reviewer can grasp the context faster. It's easier for a reviewer to review three commits in succession as opposed to one commit containing three different logical changes.

We all do however make that "WIP" commit from time to time. We do commits that don't build, that have an ugly commit message etc. With git you can always go back and clean your history up with `git rebase --interactive`. See [this guide](https://git-rebase.io/) to learn how to manipulate the Git history.

PS: [Linus Torvalds on _clean history_](https://lwn.net/Articles/328438/)

## Branching strategies

#### Release handling

How you handle releases depends on whether you have to support multiple software releases. If you always only release one version and then develop the next version, then simply adding __tags__ to the master branch is sufficient.

If you have to support multiple releases, create a __release branch__ for every release. On this branch you integrate bugfixes. You also prepare and release updates from it. Even if you only support one release release branches can be made great use of. They are an excellent way to perform a soft code freeze where on the release branch the code doesn't change during bug-hunting while on master new code can be added at full speed.

Read [this great guide](https://trunkbaseddevelopment.com/branch-for-release/) on release branching.

Sometimes you detect a bug that affects multiple releases or your master. There are different approaches how to tackle this situation.

- Git Flow recommends creating a branch from the release and merging the fixing commits into every affected release and the develop branch.

- You can always implement the fix for every release manually

- When you have fixed the issue in one branch you can cherry-pick the commits onto the other branches.

#### Popular Branching Strategies

There are a few branching strategy guides out there. One of the first is [Git Flow](http://nvie.com/posts/a-successful-git-branching-model/). It was one of the first detailed branching guidelines for git and gained a lot of popularity because of that. It's an interesting read, but in my humble opinion it's an overkill for a branching strategy. Here are two reddit threads [[1]](https://www.reddit.com/r/git/comments/bkvo0h/lpt_dont_go_overboard_with_your_branching_strategy/) [[2]](https://www.reddit.com/r/programming/comments/a8n44j/a_successful_git_branching_model/) where this strategy is discussed and where the downsides of it are layed out.

Another very popular workflow is [Trunk-based development](https://trunkbaseddevelopment.com/) (TBD). This website has many great articles about the details of the workflow. Some parts of it can be applied to other workflows. Microsoft has published a [recommendation](https://docs.microsoft.com/en-us/azure/devops/repos/git/git-branching-guidance?view=azure-devops) for Git workflows that is very very similar to TBD while being a much shorter read. That article and TBD in general is my personal recommendation to be a basis for your workflow.

Once in a while some company invents a new Git workflow, gives it a fancy name and releases it as an article. Here are those that I'm aware of.

- [Release Flow](https://docs.microsoft.com/en-us/azure/devops/learn/devops-at-microsoft/release-flow) by one team at Microsoft
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [GitLab Flow](https://docs.gitlab.com/ee/workflow/gitlab_flow.html)

However, it's not very satisfying to read these articles because you'll realize they're mostly the same as TBD. Which speaks for TBD IMO. So many Git specialist companies recommending something like it.

#### Long-lived topic branches

Topic branches should be short-lived. This means that ideally only one or a few days should pass between branching and merging back. If branches live longer, problems arise from the fact that potentially the code in master changes, resulting in a divergence between the current master codebase and the version on which you created your branch. Merging/rebasing becomes very complicated.

- Ways to prevent long-lived branches:
    - Reduce the scope of the branch/issue (try to split the problem you are trying to solve into smaller problems)
    - Use _feature toggles_ [[1]](https://martinfowler.com/articles/feature-toggles.html) [[2]](https://martinfowler.com/articles/feature-toggles.html) ... TL;DR: Add some form of toggle (an `if` statement most of the time) to dis- and enable the new code. With this approach the new code resides next to the old and both evolve close together.
    - Use [branch by abstraction](https://trunkbaseddevelopment.com/branch-by-abstraction/), a variant of feature toggles where you first introduce a new abstraction that wraps the part of the code you want to change. Then you implement the new code as an implementation of that abstraction while the rest of the team uses the old implementation 

## E-Mail based workflow (Linux kernel)

The Linux kernel is the birht place of Git. The lead developer of Linux, Linus Torvalds, made Git because he needed a source code manager that suits the development workflow of the kernel. That workflow is centered around mailing lists. Code changes ("patches") are shared via mailings lists and there is no central Git server like GitHub. [Here](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows) is an overview of distributed Git workflows. [Here](https://git-send-email.io/) is a guide explaining how to use Git with E-Mails.
