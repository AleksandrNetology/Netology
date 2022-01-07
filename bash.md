8.1 какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?

vagrant@vagrant:~$ man bash | grep -n 'HISTFILESIZE'
1121:       HISTFILESIZE

8.2 что делает директива ignoreboth в bash?

Значение ignoreboth является сокращением для ignorespace и ignoredups:
	ignorespaces - игнорировать команды, начинающиеся с пробелов
	ignoredups - игнорировать дубликаты команд
Описание этого непонятно чего приводится в разделе про параметр HISTCONTROL
	
Вся эта хрень вставляется в файл ~/.bashrc - это файл с настройками bash
Описание тут: http://www.linuxjournal.su/777/
И тут ещё смотрел: https://qastack.ru/ubuntu/15926/how-to-avoid-duplicate-entries-in-bash-history

9. В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?

Фигурные скобки на англ. называются brace. Впервые это слово в man bash встрнечается на строчке 511:
"...If the function reserved word is used, but the parentheses are not supplied, the braces are required."

Вот здесь хорошо написано про brace: http://rus-linux.net/lib.php?name=/MyLDP/consol/brace-ru.html
Разместил статью в отдельном файле: brace.note












