# Netology

# 2.1. Системы контроля версий.
# Описание файла Terraform.gitignore

**/.terraform/*
# всё содержимое директории .terraform игнорируется + не важно на каком уровне вложенности находится директория .terraform
# Спасибо Филиппу Воронову за дополнительные пояснения

*.tfstate 
# файлы с казанным расширение игнорируются

*.tfstate.*
# файл, содержащий в своём названии .tfstate. игнорируется

crash.log
override.tf
override.tf.json
.terraformrc
terraform.rc
# игнорировать указанные файлы

*_override.tf
*_override.tf.json
# файлы, оканчивающиеся так - игнорируются

# Создано 2 копии репозитория на 2х рабочих станциях: дома и в офисе. Синхронизировано.


# Создан конфликт версий.
=======
# добавлена ветка 'exofix' для лекций по merge

