Куда надо перейти в дереве git, чтобы вывод команды > git log --oneline --graph --all - показывала все ветвления проекта?
Сейчас у меня получается посмотреть только основную ветку проекта.

Странно, но получилось так:
1. Запустил >git в ветке main, как обычно
2. Далее, как и обычно, перешёл в ветку fix: > git switch fix
											   Switched to branch 'fix'
											   Your branch is ahead of 'origin/fix' by 1 commit.
													(use "git push" to publish your local commits)
	Вижу, что надо запушить какой-то коммит.

3. Делаем: > git push
	В удалённый репозиторий улетают обновления.

4. Продолжая находится в ветке fix выполняю: > git log --oneline --graph --all
	и получаю вид всех веток!!! Почему рантше этого не получалось????

Вывод комманды:
* dfc51cd (HEAD -> fix, origin/fix) new file
* 80f543b (gitlab/fix, bitbuc/fix) change file in branch 'fix'
* 12ffb5c branch 'fix'
* ae30085 New branch 'fix'
| * 59a6438 (origin/main, origin/HEAD, main) Rename lesson2
| * be01b62 lesson2 update
| * 0b0c053 test pycharm
| * ece31c8 test pycharm
| * 7de4a57 Очередное простое обновление
| * eab3011 file updated
| * 1a3a083 file lesson2.txt updated
| * b9f2256 Задание 2 - Тэги
| * 4f79470 Lesson 3 complete
| * f5fd5cc Lesson 4/5
| * a7d6d01 Lesson 4/5
| * e65c895 (tag: v0.2) fix errors
| * 6cef7d1 Lesson 3/5
| *   656c9cc Merge branch 'main' of https://github.com/AleksandrNetology/Netology
| |\
| | * 609a479 Clone commite
| * | 9e5962b lesson continue
| |/
| * 11d63b4 lesson continue
| *   6697dc0 (tag: v0.1) Merge branch 'main' of https://github.com/AleksandrNetology/Netology
| |\
| | * 1f40022 forgotten file 1
| * | 3765957 Knowledge Continuous Integration
| * | c84e0be forgotten file 1.2
| |/
| * 0cce800 forgotten file
| * 7b32637 Git diff, commit, rm, rv, log
| *   84cebad Merge branch 'main' of https://github.com/AleksandrNetology/Netology
| |\
| | * 225f6f7 Second repository
| * | 9e2a1f6 Основы Git/ 01, 02
| |/
| * 7a7bd58 readme updated
| * 6e688b7 Added gitignore
| * 510c3ef Added gitignore
| * b57fc5d Added gitignore
| * f85ccec Moved and deleted
| * 27131b3 delete file
|/
* 1192c91 Prepare to delete and move
* aa001a7 Added gitirnore
* 16f1d30 First local commit
*   960e57e (tag: v0.0) Merge pull request #3 from AleksandrNetology/AleksandrNetology-patch-1
|\
| * 1d8f447 (origin/AleksandrNetology-patch-1) Update README.md
|/
* d33b909 Update README.md
* 1ff27d4 Initial commit
* faa2383 (bitbuc/main) Initial commit
* c9f0535 (gitlab/main) Configure SAST in `.gitlab-ci.yml`, creating this file if it does not already exist
