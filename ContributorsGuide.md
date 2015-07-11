# Introduction #

Here's all the stuff you should know if you want to do more than just use firefox-ntlmauth.

# Product Roadmap #
Besides fixing bugs, hopefully we're heading down a path that gets us to a better product with more features. The ProductRoadmap talks about some of the more major features and milestones we'd like to accomplish.

# Patch Acceptance #

If you file an issue and include a source fix for it, we'll make sure it gets added as appropriate with the proper code style and tracking. We'd rather get contributions than have y'all scared off by the rules. :)

# Code Style #

The only real style guideline I've got:

**Use tabs, not spaces, for indentation.**

There ya go.

# Issue Tracking #

Any contributions that go into the add-on really need to be tracked in the issue tracker. That way we know what the list of features/fixes is that we make from version to version and we can track work back to the issues and see why things were done the way they were done.

# Source Control #

Again, trying to keep this simple yet disciplined:

  * No commits directly to the trunk unless it's a broken-build-one-line-fix-emergency.
  * Any significant work happens in task branches.
  * Task branches should be named after the issue they're addressing. (If you need to create an issue so the work can be tracked back, do it.)

## Trunk ##

The trunk always has the latest and greatest. It may have stuff that hasn't been released yet.

## Branches ##

There are two kinds of branches - issue branches and spike branches. There is a folder inside the "branches" folder to contain each of these branch types.

### Issues ###

**Issue branches** are for doing work that relates to an issue filed in the issue tracker. This might be fixing a bug, adding a feature, or whatever.

Issue branches should be named after the issue ID they correspond to.

### Spikes ###

**Spike branches** are for experimental work that isn't filed in the issue tracker. For example, I have a spike branch where I'm trying to figure out how to get XPCOM working to intercept the auth dialog.

Spike branches should be named in two-or-three word descriptions, all lower case, separated by dashes. For example, I called my XPCOM spike "auth-dialog-integration" since that's the endgame there.

### Branch Structure ###

Whether it's an issue or a spike, the general branch structure is the same:

```
 + svn
    + branches
       + issue
       |  + 1
       |    + dev                // Codeline/trunk for the task
       |    + tags               // Contains tags relevant to merging this task code
       |       + source          // Tags of the main codeline (trunk)
       |       |  + branchpoint  // Tag of trunk where this task branched off
       |       |  + mergepoint00 // Tag where trunk code was brought into this task
       |       |  + mergepointN
       |       + task            // Tags of this task branch codeline (dev)
       |          + mergepoint00 // Tag of dev to push task changes to trunk
       |          + mergepointN
       + spike
          + auth-dialog-integration
             + dev               // See above
             + tags              // See above
```

The general process (refer to the above template for some context as you read this):

  1. **Create your task branch.**
    1. Create a folder under "issue" or "spike" to contain your branch. Name it accordingly.
    1. Create a "tags" folder under that to house your tags.
    1. Create a "source" folder under "tags" so you can create trunk tags.
    1. Create a "task" folder under "tags" so you can create task codeline tags.
    1. Do an svn copy from trunk to the tags/source folder. Call the copied folder "branchpoint" - this marks where you're jumping off of trunk and starting your work.
    1. Do an svn copy from the "branchpoint" to a folder called "dev" right under your main issue/spike folder. This is the "trunk" for your task, and this is what you'll check out and do your work in.
  1. **Do your work in your task branch.** Check in often - it doesn't matter how much because it's your branch, not the trunk. As long as you work in your branch you won't be affecting anyone else.
  1. **Bring in changes from the main codeline.** As you work, other folks may also be working and committing to the trunk. You'll want to get these changes into your task branch.
    1. Do an svn copy from trunk to the tags/source folder (where "branchpoint" is). Call the copied folder "mergepoint00" - you may bring changes in from the trunk more than once, so number these serially (mergepoint01, mergepoint02, etc.).
    1. Do an svn merge from the branchpoint tag to your new mergepoint tag and commit them into your working copy of your task. What this is doing is saying, "I want all the changes between the branchpoint and the new mergepoint, and I want to apply them to my task." You may have to resolve some conflicts if you've been working in the same file as some other folks.
    1. As you do more work, you'll perform this action several times - bringing changes from the trunk into your task. When you do subsequent merges, do it from the last mergepoint to the most recent. For example, your next merge would be from mergepoint00 to mergepoint01; then from mergepoint01 to mergepoint02; etc.
  1. **When you're done with a spike,** generally that means you're done and the branch gets abandoned. Spikes are usually experimental only, they're usually rushed, and you probably don't test them as well. If you want to apply the changes for real to the trunk, it's best if you file an issue in the issue tracker and do the work in an issue branch following good production practices.
  1. **When you're done with an issue,** you'll want to merge your task changes back to trunk.
    1. Make sure you have gotten the latest changes from trunk merged into your task branch. See above for more on that.
    1. Make sure it builds from the command line and you've tested it out for real in Firefox.
    1. Do an svn copy from your task codeline (dev) to the tags/task folder. Call it "mergepoint00" (usually there will only be one of these mergepoints ever, but in the event you need to continue work, we'll just number it anyway).
    1. Do an svn switch on your working copy - switch it back so you're working in the main trunk.
    1. Do an svn merge from the last mergepoint in your task's tags/source folder to the mergepoint in your tags/task folder. Basically you're saying "take the differences between the latest trunk that I grabbed and the stuff in my codeline, then apply it to the trunk."
    1. **BEFORE YOU COMMIT do one last build and test in Firefox** to make sure the merge went correctly.
    1. **Once it works, commit the changes to the trunk.** You just successfully resolved an issue. On some largely-unpredictable basis, we'll tag the trunk and release, and that release will include your fix.

This sounds like a lot of work, but there's a lot of benefit to doing it in this disciplined fashion:
  * **Your work is isolated.** No one checking something in will break you, and you can get the changes to the trunk at your leisure.
  * **The trunk build will never be broken.** You can check partially-completed things into your task branch but you won't be breaking the trunk.
  * **Issue work is quantifiable.** If we want to see what work went into fixing a particular issue, we can look in Subversion and it's easy to tell. It also means if a particular fix breaks something, we can roll that changeset out.
  * **We can use the merges to the trunk as a way to figure out what's new** (and put that in the readme).

Here is a simple batch script for creating an issue branch (create-issue-branch.bat):
```
@echo off
if "%1" == "" goto :help

@echo on
svn mkdir https://firefox-ntlmauth.googlecode.com/svn/branches/issue/%1 -m "Created folder for issue %1."
svn mkdir https://firefox-ntlmauth.googlecode.com/svn/branches/issue/%1/tags -m "Created tag folder for issue %1."
svn mkdir https://firefox-ntlmauth.googlecode.com/svn/branches/issue/%1/tags/source -m "Created folder for issue %1 source tags."
svn mkdir https://firefox-ntlmauth.googlecode.com/svn/branches/issue/%1/tags/task -m "Created folder for issue %1 task tags."
svn copy https://firefox-ntlmauth.googlecode.com/svn/trunk https://firefox-ntlmauth.googlecode.com/svn/branches/issue/%1/tags/source/branchpoint -m "Created branchpoint for issue %1 codeline."
svn copy https://firefox-ntlmauth.googlecode.com/svn/branches/issue/%1/tags/source/branchpoint https://firefox-ntlmauth.googlecode.com/svn/branches/issue/%1/dev -m "Created codeline for issue %1."
@echo off
echo Done. Switch working task copy to https://firefox-ntlmauth.googlecode.com/svn/branches/issue/%1/dev

goto :eof

:help
echo create-issue-branch - Create a firefox-ntlmauth issue branch.
echo Syntax: create-issue-branch [issue#]
echo Example: create-issue-branch 11
```