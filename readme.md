# Netology

# 2.1. Системы контроля версий.
# Описание файла Terraform.gitignore

**/.terraform/*
# всё содержимое директории .terraform игнорируется

*.tfstate 
# файлы с казанным расширение игнорируются

*.tfstate.*
# файл, содержащий в свём названии .tfstate. игнорируется

crash.log
override.tf
override.tf.json
.terraformrc
terraform.rc
# игнорировать указанные файлы

*_override.tf
*_override.tf.json
# файлы, оканчивающиеся так - игнорируются
