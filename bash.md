8.1 какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?

vagrant@vagrant:~$ man bash | grep -n 'HISTFILESIZE'
1121:       HISTFILESIZE
3946:       of lines specified by the value of HISTFILESIZE.  If HISTFILESIZE is  un‐
3962:       the history file is truncated to contain no more than HISTFILESIZE lines.
3963:       If  HISTFILESIZE  is unset, or set to null, a non-numeric value, or a nu‐

8.2 что делает директива ignoreboth в bash?

Значение ignoreboth является сокращением для ignorespace и ignoredups:
	ignorespaces - игнорировать команды, начинающиеся с пробелов
	ignoredups - игнорировать дубликаты команд
	
Вся эта хрень вставляется в файл ~/.bashrc - это файл с настройками bash
Описание тут: http://www.linuxjournal.su/777/
И тут ещё смотрел: https://qastack.ru/ubuntu/15926/how-to-avoid-duplicate-entries-in-bash-history

9. В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?
Вот здесь хорошо написано: http://rus-linux.net/lib.php?name=/MyLDP/consol/brace-ru.html
Разместил статью в отдельном файле: brace.note

Как это всё в man найти, я даже примерно не понимаю.










