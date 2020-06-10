---
layout: post
title: Git bisect with unit tests
---

## Scenario

I have some unit tests. I don't always run them, and I don't add them to any continuous integration workflow.
But I can execute them from time to time.

I executed them recently and I just noticed that one of them fails. But I don't know when that started. I could try to guess, look at the code. But I could also just ask git with a bit of automation.

For that, I can finally try out `git bisect`, or `git bisect run` more specifically.

## Prerequisite information

Here's how my repo was looking

```cmd
BAD       * 8d3a3d25 (HEAD -> cssvars, origin/cssvars) more ...
          * 35c6201c More JS code comment-out
          * 2ea1c16f commented out code.
          * 3d00e283 added Not to test
          * a88c0d27 css cleanup
          * c8e15e1a css cleanup
          * 4f8af80b apply BEM notation (CSS class renames)
          * 1ab6347d cleanup
          * 6847313b remove unused css defs.
OK        * fd34a276 make style more consistent for buttons
          * ed4d8d90 correct initialization for user cost ...
          *   1833e39a Merge branch 'BugFix' into cssvars
```

Git bisect needs some info to run:

- when was your repo OK?
  - for me, it was a bit back (E.g. fd34a276)
- when was your repo BAD?
  - well, it's bad now, so use the latest (E.g. 8d3a3d25)
- what command can you run to identify a repo version as good or bad?
  - well, I had some unit tests, so I can execute those.
  - I can use MSBuild to build the solution, I can then run VSTest.Console.exe to run the unit tests.
  - And I can check the %ERRORLEVEL% to check the result for them.
  - I'll simply use a plain old batch file, as I'm comfortable with those.

## Execution

I executed git bisect for the input mentioned just before and got the following steps:

1. It first checked out:

- [`c8e15e1a`] css cleanup.
  ```cmd
  Bisecting: 4 revisions left to test after this (roughly 2 steps)
  [c8e15e1a35fa9023c5f5828fbc32efed2ea7ca51] css cleanup
  ```
- It built and executed the tests. Got result:
  ```cmd
  Total tests: 12. Passed: 12. Failed: 0. Skipped: 0.
  Test Run Successful.✔
  ```

2. Next it checked out:

- [`3d00e283`]
  ```cmd
  Bisecting: 2 revisions left to test after this (roughly 1 step)
  [3d00e283dff3663c24e94df568ccda8d7f631bbe] added Not to test
  ```
- It built and executed the tests. Got result:
  ```cmd
  Total tests: 12. Passed: 11. Failed: 1. Skipped: 0.
  Test Run Failed.❌
  ```

3. Next, it moves on to:

- [`a88c0d27`]
  ```cmd
  Bisecting: 0 revisions left to test after this (roughly 0 steps)
  [a88c0d276892f5906e50e234c3595fac22bab841] css cleanup
  ```
- It built and executed the tests. Got result:
  ```cmd
  Total tests: 12. Passed: 12. Failed: 0. Skipped: 0.
  Test Run Successful.✔
  ```

4. The first BAD commit, when the tests started failing was identified and displayed:

   ```cmd
   3d00e283dff3663c24e94df568ccda8d7f631bbe is the first bad commit
   ```

5. It also showed me who broke the tests, but I knew beforehand that would be me.

## Execution Summary

So here's the execution order viewed on the git log:

```cmd
BAD       * 8d3a3d25 (HEAD -> cssvars, origin/cssvars) more ...
          * 35c6201c More JS code comment-out
          * 2ea1c16f commented out code.
#2 (BAD)  * 3d00e283 added Not to test
#3 (OK)   * a88c0d27 css cleanup
#1 (OK)   * c8e15e1a css cleanup
          * 4f8af80b apply BEM notation (CSS class renames)
          * 1ab6347d cleanup
          * 6847313b remove unused css defs.
OK        * fd34a276 make style more consistent for buttons
          * ed4d8d90 correct initialization for user cost ...
          *   1833e39a Merge branch 'BugFix' into cssvars
```

It does a divide and conquer. Sweet!

## Adapting

There's a simple configuration area which can be modified.

Here it is for `rungitbisect.bat`.

```cmd
Rem === Begin Parameter section ===
Set goodGitHash=fd34a27
Set badGitHash=8d3a3d2
Set batchNameInThisDir=buildandruntest.bat
Set vscmd="c:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\Common7\Tools\VsDevCmd.bat"
Rem === End Parameter section ===
```

And here it is for `buildandruntest.bat`.

```cmd
Rem === Begin Parameter section ===
Set SolutionDir=d:\work\my-solution\
Set SolutionName="MySolution.sln"
Set TestProjectName=MySolutionWebClient-mstest
Set Configuration=Debug
Rem === End Parameter section ===
```
