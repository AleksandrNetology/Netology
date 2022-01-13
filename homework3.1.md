1. Установите средство виртуализации Oracle VirtualBox.
	--- Выполнение -----------------------------------------------------------------------
	- Скачал: https://download.virtualbox.org/virtualbox/6.1.30/VirtualBox-6.1.30-148432-Win.exe
	- Установил.
	--------------------------------------------------------------------------------------

2. Установите средство автоматизации Hashicorp Vagrant.
	--- Выполнение -----------------------------------------------------------------------
	- Скачал: https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.msi
	- Установил.
	--------------------------------------------------------------------------------------

3. В вашем основном окружении подготовьте удобный для дальнейшей работы терминал.
	--- Выполнение -----------------------------------------------------------------------
	- Windows Terminal
	--------------------------------------------------------------------------------------
	
4. С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant.
   В ней выполните vagrant init. Замените содержимое Vagrantfile по умолчанию....
   
	--- Выполнение -----------------------------------------------------------------------
	- Создайте директорию, в которой будут храниться конфигурационные файлы Vagrant.
		d:\VirtualBox
		> vagrant init
			A `Vagrantfile` has been placed in this directory. You are now
			ready to `vagrant up` your first virtual environment! Please read
			the comments in the Vagrantfile as well as documentation on
			`vagrantup.com` for more information on using Vagrant.
	- Редактирую Vagrantfile:
	
		Vagrant.configure("2") do |config|
			config.vm.box  = "bento/ubuntu-20.04"
		end
	
	- > vagrant up
			Bringing machine 'default' up with 'virtualbox' provider...
			==> default: Importing base box 'bento/ubuntu-20.04'...

			[KProgress: 40%
			[K==> default: Matching MAC address for NAT networking...
				==> default: Checking if box 'bento/ubuntu-20.04' version '202112.19.0' is up to date...
				==> default: Setting the name of the VM: tmp_default_1642097807186_92949
				==> default: Clearing any previously set network interfaces...
				==> default: Preparing network interfaces based on configuration...
				default: Adapter 1: nat
				==> default: Forwarding ports...
				default: 22 (guest) => 2222 (host) (adapter 1)
				==> default: Booting VM...
				==> default: Waiting for machine to boot. This may take a few minutes...
					default: SSH address: 127.0.0.1:2222
					default: SSH username: vagrant
					default: SSH auth method: private key
	------ 8< -----------------------------
	
	- > vagrant suspend
			==> default: Saving VM state and suspending execution...

	- > vagrant up
			Bringing machine 'default' up with 'virtualbox' provider...
			==> default: Checking if box 'bento/ubuntu-20.04' version '202112.19.0' is up to date...
			==> default: Resuming suspended VM...
			==> default: Booting VM...
			==> default: Waiting for machine to boot. This may take a few minutes...
			default: SSH address: 127.0.0.1:2222
			default: SSH username: vagrant
			default: SSH auth method: private key
			default:
			default: Vagrant insecure key detected. Vagrant will automatically replace
			default: this with a newly generated keypair for better security.
			default:
			default: Inserting generated public key within guest...
			default: Removing insecure key from the guest if it's present...
			default: Key inserted! Disconnecting and reconnecting using new SSH key...
			==> default: Machine booted and ready!
			
	- > vagrant halt
			==> default: Attempting graceful shutdown of VM...
	--------------------------------------------------------------------------------------	
	
5. Ознакомтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина,
	которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?
	--- Выполнение -----------------------------------------------------------------------
	RAM:	1024mb
	CPU:	2
	HDD:	64 gb
	Video:	4 mb
	--------------------------------------------------------------------------------------
	
6. Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: документация.
	Как добавить оперативной памяти или ресурсов процессора виртуальной машине?	
	--- Выполнение -----------------------------------------------------------------------
	Vagrant.configure("2") do |config|
			config.vm.define "U2004" do |u2004|
				u2004.vm.box = "bento/ubuntu-20.04"
				u2004.vm.provider "virtualbox" do |vb|
					vb.customize ["modifyvm", :id, "--usb", "on"]
					vb.customize ["modifyvm", :id, "--memory", "4096"]
					vb.customize ["modifyvm", :id, "--vram", "6"]
					vb.cpus = 2
					vb.name = "Netology.U2004"
				end
			end
		end
	--------------------------------------------------------------------------------------
	
7. Команда vagrant ssh из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри
	виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых
	команд в терминале Ubuntu.
	--- Выполнение -----------------------------------------------------------------------
	vagrant ssh
	Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

	* Documentation:  https://help.ubuntu.com
	* Management:     https://landscape.canonical.com
	* Support:        https://ubuntu.com/advantage

	System information as of Thu 13 Jan 2022 06:33:43 PM UTC

	System load:  1.39               Processes:             123
	Usage of /:   11.4% of 30.88GB   Users logged in:       0
	Memory usage: 18%                IPv4 address for eth0: 10.0.2.15
	Swap usage:   0%


	This system is built by the Bento project by Chef Software
	More information can be found at https://github.com/chef/bento
	vagrant@vagrant:~$
	--------------------------------------------------------------------------------------
	
8.1 какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?
	--- Выполнение -----------------------------------------------------------------------
	man bash
		/HISTFILESIZE			# Нажал '/', написал, что ищу HISTFILESIZE
		-N						# Включил нумерацию строк
		859		HISTFILESIZE	# Номер строки
		
		/HISTSIZE				# Нажал '/', написал, что ищу HISTSIZE
		875		HISTSIZE		# Номер строки
	--------------------------------------------------------------------------------------
	
8.2 Что делает директива ignoreboth в bash?
	--- Выполнение -----------------------------------------------------------------------
	Значение ignoreboth является сокращением для ignorespace и ignoredups:
		ignorespaces - игнорировать команды, начинающиеся с пробелов
		ignoredups - игнорировать дубликаты команд
	Описание этого приводится в разделе про параметр HISTCONTROL
	--------------------------------------------------------------------------------------
	
	Вся эта хрень вставляется в файл ~/.bashrc - это файл с настройками bash
	Описание тут: http://www.linuxjournal.su/777/
	И тут ещё смотрел: https://qastack.ru/ubuntu/15926/how-to-avoid-duplicate-entries-in-bash-history

9. В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?
	--- Выполнение -----------------------------------------------------------------------
	
	{} - зарезервированные символы, использующиеся для автоматизации.
	
	Строка 179:	! case  coproc  do done elif else esac fi for function if in select then until while { } time [[ ]]
	
	А вообще, фигурные скобки на англ. называются brace. Впервые это слово в man bash встречается на строчке 407:
	"...If the function reserved word is used, but the parentheses are not supplied, the braces are required."

	Вот здесь хорошо написано про brace: http://rus-linux.net/lib.php?name=/MyLDP/consol/brace-ru.html
	Разместил эту статью в отдельном файле на github: brace.note
	--------------------------------------------------------------------------------------
	
10. Как создать однократным вызовом touch 100000 файлов?
	Получится ли аналогичным образом создать 300000? Если нет, то почему?
	
	--- Выполнение -----------------------------------------------------------------------
	Можно:
	touch {000001..100000}.md
	
	!Но зачем? =8-()!
		
	300 тыс. можно создать только за три вызова - однократно никак.
	Почему?
	Возможно потому, что в этом нет смысла, а может потому, что это ограничение какой-то
	переменной, участвующей в выполнении этого действия.
	
	Хорошая статья по команде touch нашлась тут:
	https://omgubuntu.ru/15-polieznykh-primierov-ispolzovaniia-komandy-touch-v-sistiemakh-linux/
	
	Разместил её в файле на github: touch.docx
	--------------------------------------------------------------------------------------
		
11. В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]]
	--- Выполнение -----------------------------------------------------------------------
	Поискал. Прикольно.

	Гуглим. Есть статья https://www.opennet.ru/docs/RUS/bash_scripting_guide/c2171.html
	Тут есть про разные скобки. Копию статьи скинул в файл: skobki.docx
	
	Нашёл статью по основам bash (https://habr.com/ru/post/47163/), эдесь тоже про скобки.

	Конструкция [[ -d /tmp ]] проверяет наличие каталога /tmp и возвращает 0 или 1

	if [[ -d /tmp ]]
		then
			echo "1 - каталог есть"
		else
			echo "0 - каталога нет"
	fi
	--------------------------------------------------------------------------------------

12. Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных,
	командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине
	наличия первым пунктом в списке:

	bash is /tmp/new_path_directory/bash
	bash is /usr/local/bin/bash
	bash is /bin/bash
	
	--- Выполнение -----------------------------------------------------------------------
	Последовательность у меня была такая:
	1. mkdir tmp
	2. cd tmp
	3. mkdir new_path_directory
	4. cd new_path_directory
	5. mkdir bash
	6. cd ~
	7. cp /usr/bin/bash tmp/new_path_directory/bash
	8. export PATH=tmp/new_path_directory/bash:$PATH
	9. type -a bash
	bash is tmp/new_path_directory/bash/bash
	bash is /usr/bin/bash
	bash is /bin/bash
	--------------------------------------------------------------------------------------

13. Чем отличается планирование команд с помощью batch и at?

	--- Выполнение -----------------------------------------------------------------------
	at      executes commands at a specified time

	batch   executes commands when system load levels permit; in other words,
               when the load average drops below 1.5, or the value specified  in
               the invocation of atd.
	--------------------------------------------------------------------------------------

	Хорошее описание тут: https://codepre.com/ru/how-to-use-at-and-batch-on-linux-to-schedule-commands.html
	Вынес в файл  на github: shcedule.docx