# ------------------------------------------------------
# Как вы получаете метод 'recurcive' при merge?
# У меня всегда только 'ort'. Или ort = recurcive?

$ git merge origin/git-merge
Merge made by the 'ort' strategy.
 branching/merge.sh | 5 +++--
 1 file changed, 3 insertions(+), 2 deletions(-)
# ------------------------------------------------------

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git pull
remote: Enumerating objects: 27, done.
remote: Counting objects: 100% (27/27), done.
remote: Compressing objects: 100% (20/20), done.
remote: Total 27 (delta 11), reused 22 (delta 6), pack-reused 0
Unpacking objects: 100% (27/27), 4.20 KiB | 43.00 KiB/s, done.
From https://github.com/AleksandrNetology/Netology
   62a5c2d..67dda12  main       -> origin/main
 * [new branch]      git-merge  -> origin/git-merge
 * [new branch]      git-rebase -> origin/git-rebase
Updating 62a5c2d..67dda12
Fast-forward
 branching/merge.sh  |   8 ++++
 branching/rebase.sh |  10 +++++
 experiment.note     | 111 ++++++++++++++++++++++++++++++++++++++++++++++++++++
 3 files changed, 129 insertions(+)
 create mode 100644 branching/merge.sh
 create mode 100644 branching/rebase.sh
 create mode 100644 experiment.note

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git checkout git-rebase
Switched to a new branch 'git-rebase'
Branch 'git-rebase' set up to track remote branch 'git-rebase' from 'origin'.

HOME@iGO MINGW64 /c/Git/GitHub (git-rebase)
$ ll
total 29
drwxr-xr-x 1 HOME 197121     0 дек 16 20:37 branching/
-rw-r--r-- 1 HOME 197121  4335 дек 16 20:15 experiment.note
-rw-r--r-- 1 HOME 197121    59 дек 15 23:50 forgotten.md
-rw-r--r-- 1 HOME 197121    23 дек 16 00:07 has_been_moved.txt
-rw-r--r-- 1 HOME 197121   106 дек 16 00:10 lesson1.txt
-rw-r--r-- 1 HOME 197121   236 дек 16 00:10 outlines.md
-rw-r--r-- 1 HOME 197121 11548 дек 16 00:13 outlines.txt
-rw-r--r-- 1 HOME 197121    25 дек 15 23:50 PyCharm_lesson
-rw-r--r-- 1 HOME 197121  1198 дек 15 23:50 readme.md
drwxr-xr-x 1 HOME 197121     0 дек 15 23:50 terraform/

HOME@iGO MINGW64 /c/Git/GitHub (git-rebase)
$ cd branching/

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ ll
total 2
-rwxr-xr-x 1 HOME 197121 155 дек 16 20:15 merge.sh*
-rwxr-xr-x 1 HOME 197121 155 дек 16 20:37 rebase.sh*

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ notepad++ rebase.sh

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git add rebase.sh

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git commit -m 'git-rebase 1'
[git-rebase 21e2892] git-rebase 1
 1 file changed, 5 insertions(+), 3 deletions(-)

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ notepad++ rebase.sh

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git add rebase.sh

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git commit -m 'git-rebase 2'
[git-rebase 1464119] git-rebase 2
 1 file changed, 1 insertion(+), 1 deletion(-)

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git push
Enumerating objects: 11, done.
Counting objects: 100% (11/11), done.
Delta compression using up to 8 threads
Compressing objects: 100% (8/8), done.
Writing objects: 100% (8/8), 785 bytes | 392.00 KiB/s, done.
Total 8 (delta 3), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (3/3), completed with 1 local object.
To https://github.com/AleksandrNetology/Netology
   7d3462d..1464119  git-rebase -> git-rebase

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.

HOME@iGO MINGW64 /c/Git/GitHub/branching (main)
$ git merge origin/git-merge
Merge made by the 'ort' strategy.
 branching/merge.sh | 5 +++--
 1 file changed, 3 insertions(+), 2 deletions(-)

HOME@iGO MINGW64 /c/Git/GitHub/branching (main)
$ git push
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 386 bytes | 386.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/AleksandrNetology/Netology
   67dda12..397d846  main -> main

HOME@iGO MINGW64 /c/Git/GitHub/branching (main)
$ ll
total 3
-rwxr-xr-x 1 HOME 197121 160 дек 16 21:08 merge.sh*
-rwxr-xr-x 1 HOME 197121 171 дек 16 20:53 rebase.sh*
-rwxr-xr-x 1 HOME 197121 158 дек 16 20:38 rebase.sh.bak*

HOME@iGO MINGW64 /c/Git/GitHub/branching (main)
$ cd ..

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ ll
total 29
drwxr-xr-x 1 HOME 197121     0 дек 16 21:08 branching/
-rw-r--r-- 1 HOME 197121  4335 дек 16 20:15 experiment.note
-rw-r--r-- 1 HOME 197121    59 дек 15 23:50 forgotten.md
-rw-r--r-- 1 HOME 197121    23 дек 16 00:07 has_been_moved.txt
-rw-r--r-- 1 HOME 197121   106 дек 16 00:10 lesson1.txt
-rw-r--r-- 1 HOME 197121   236 дек 16 00:10 outlines.md
-rw-r--r-- 1 HOME 197121 11548 дек 16 00:13 outlines.txt
-rw-r--r-- 1 HOME 197121    25 дек 15 23:50 PyCharm_lesson
-rw-r--r-- 1 HOME 197121  1198 дек 15 23:50 readme.md
drwxr-xr-x 1 HOME 197121     0 дек 15 23:50 terraform/

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git mv experiment.note merge.and.rebase.note

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ ll
total 29
drwxr-xr-x 1 HOME 197121     0 дек 16 21:08 branching/
-rw-r--r-- 1 HOME 197121    59 дек 15 23:50 forgotten.md
-rw-r--r-- 1 HOME 197121    23 дек 16 00:07 has_been_moved.txt
-rw-r--r-- 1 HOME 197121   106 дек 16 00:10 lesson1.txt
-rw-r--r-- 1 HOME 197121  4335 дек 16 20:15 merge.and.rebase.note
-rw-r--r-- 1 HOME 197121   236 дек 16 00:10 outlines.md
-rw-r--r-- 1 HOME 197121 11548 дек 16 00:13 outlines.txt
-rw-r--r-- 1 HOME 197121    25 дек 15 23:50 PyCharm_lesson
-rw-r--r-- 1 HOME 197121  1198 дек 15 23:50 readme.md
drwxr-xr-x 1 HOME 197121     0 дек 15 23:50 terraform/

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ notepad++ merge.and.rebase.note

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ notepad++ readme.md

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git mv readme.md ignores_tmp.note

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ notepad++ readme.md

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        renamed:    readme.md -> ignores_tmp.note
        renamed:    experiment.note -> merge.and.rebase.note

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   merge.and.rebase.note

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        readme.md


HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git add *
The following paths are ignored by one of your .gitignore files:
merge.and.rebase.note.bak
readme.md.bak
hint: Use -f if you really want to add them.
hint: Turn this message off by running
hint: "git config advice.addIgnoredFile false"

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   ignores_tmp.note
        renamed:    experiment.note -> merge.and.rebase.note
        modified:   readme.md


HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git commit -a -m 'change files'
[main d76966e] change files
 3 files changed, 11 insertions(+), 34 deletions(-)
 copy readme.md => ignores_tmp.note (100%)
 rename experiment.note => merge.and.rebase.note (98%)
 rewrite readme.md (99%)

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git push
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 2.07 KiB | 1.04 MiB/s, done.
Total 4 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/AleksandrNetology/Netology
   397d846..d76966e  main -> main


HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git checkout git-rebase
Switched to branch 'git-rebase'
Your branch is up to date with 'origin/git-rebase'.

HOME@iGO MINGW64 /c/Git/GitHub (git-rebase)
$ git rebase -i main
error: could not parse 'pick'
error: invalid line 2: fixup pick 1464119 git-rebase 2
You can fix this with 'git rebase --edit-todo' and then run 'git rebase --continue'.
Or you can abort the rebase with 'git rebase --abort'.


HOME@iGO MINGW64 /c/Git/GitHub (git-rebase|REBASE)
$ git rebase -i main
fatal: It seems that there is already a rebase-merge directory, and
I wonder if you are in the middle of another rebase.  If that is the
case, please try
        git rebase (--continue | --abort | --skip)
If that is not the case, please
        rm -fr ".git/rebase-merge"
and run me again.  I am stopping in case you still have something
valuable there.


HOME@iGO MINGW64 /c/Git/GitHub (git-rebase|REBASE)
$ git rebase --continue
Auto-merging branching/rebase.sh
CONFLICT (content): Merge conflict in branching/rebase.sh
error: could not apply 21e2892... git-rebase 1
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply 21e2892... git-rebase 1

HOME@iGO MINGW64 /c/Git/GitHub (git-rebase|REBASE 1/2)
$ cd branching/

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase|REBASE 1/2)
$ ll
total 3
-rwxr-xr-x 1 HOME 197121 160 дек 16 21:23 merge.sh*
-rwxr-xr-x 1 HOME 197121 256 дек 16 21:26 rebase.sh*
-rwxr-xr-x 1 HOME 197121 158 дек 16 20:38 rebase.sh.bak*

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase|REBASE 1/2)
$ notepad++ rebase.sh

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase|REBASE 1/2)
$ git add rebase.sh

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase|REBASE 1/2)
$ git rebase --continue
[detached HEAD 001428c] git-rebase 1
 1 file changed, 1 deletion(-)
Auto-merging branching/rebase.sh
CONFLICT (content): Merge conflict in branching/rebase.sh
error: could not apply 1464119... git-rebase 2
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply 1464119... git-rebase 2

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase|REBASE 2/2)
$ notepad++ rebase.sh

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase|REBASE 2/2)
$ git add rebase.sh

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase|REBASE 2/2)
$ git rebase --continue
[detached HEAD b0fd926] Merge branch 'git-merge'
 Date: Thu Dec 16 20:39:38 2021 +0300
 1 file changed, 1 insertion(+), 2 deletions(-)
Successfully rebased and updated refs/heads/git-rebase.

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git push
To https://github.com/AleksandrNetology/Netology
 ! [rejected]        git-rebase -> git-rebase (non-fast-forward)
error: failed to push some refs to 'https://github.com/AleksandrNetology/Netology'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git push -f
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 461 bytes | 461.00 KiB/s, done.
Total 4 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/AleksandrNetology/Netology
 + 1464119...b0fd926 git-rebase -> git-rebase (forced update)

HOME@iGO MINGW64 /c/Git/GitHub/branching (git-rebase)
$ git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.

HOME@iGO MINGW64 /c/Git/GitHub/branching (main)
$ git merge git
gitlab/fix    gitlab/main   git-rebase

HOME@iGO MINGW64 /c/Git/GitHub/branching (main)
$ git merge git-rebase
Updating d76966e..b0fd926
Fast-forward
 branching/rebase.sh | 3 +--
 1 file changed, 1 insertion(+), 2 deletions(-)

HOME@iGO MINGW64 /c/Git/GitHub/branching (main)
$ git push
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/AleksandrNetology/Netology
   d76966e..b0fd926  main -> main

HOME@iGO MINGW64 /c/Git/GitHub/branching (main)
$ cd ..

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git push
Everything up-to-date

HOME@iGO MINGW64 /c/Git/GitHub (main)
$ git branch -d git-rebase
Deleted branch git-rebase (was b0fd926).
