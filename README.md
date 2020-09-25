**Cenário**:

- Dois servidores MySql com repositório no Azure StorageAccount Files mapeado entre os dois servidores.

![image.png](/.attachments/image-0b685667-2b71-4b8f-8429-2379817baa3b.png)

**Ansible:** 
- Pode ser instalado em um dos dois servidores ou pode ser criar um terceiro servidor para orquestrar as tarefas do Ansible.

**Requisitos para execução:** 
- Instalar os pacotes: 
-- sshpass
-- python or python3
-- python-pip or python-pip
-- Modulo python PyMySQL

**Estrutura da pasta playbook:**

.
└── mysql (raiz)
---------├── group_vars
---------│---------├── hostbackup
---------│---------└── hostrestore
---------├── inventory
---------├── playblook-mysql-backup-restore.yml
---------├── roles
---------│---------├── mysql-backup
---------│---------│---------└── tasks
---------│---------│------------------└── main.yml
---------│---------└── mysql-restore
---------│---------│---------└── tasks
---------│---------│------------------└── main.yml

**Segurança:** 

Os dados de acesso aos servidores e ao backup ficam os arquivos:
- hostbackup
- hostrestore

Então os arquvios devem ser criptografados, comando:

`ansible-vault encrypt group_vars/hostbackup`
`ansible-vault encrypt group_vars/hostrestore`

**Parametros:**

O inventário tem dois grupo:
- hostbackup
- e hostrestore

A playbook foi configurado com dois jobs para selecionar o grupo de hosts, conforme tarefa solicitada.

Nos arquivos hostbackup e hostbackup devem ser preenchido os campos:


```
# Usuário de acesso SSH
ansible_ssh_user: 

# Senha de acesso SSH por Certificado, permissao de arquivo 0600
# ansible_ssh_private_key_file: 

# Senha de acesso SSH, caso não tiver certificado
ansible_ssh_password: 

#Dados do ROOT
ansible_become_user: 
ansible_become_pass: 

# Dados de conexao com Mysql
mysql_user: 
mysql_pass: 

# Repositorio de backup
repo_backup: ""
# Exemplo: "/tmp/dumall-{{ansible_date_time.day}}.sql"
```

Obs.: Usar o comando ansible-vault edit <arquivo>

**Execução:** 

Para executando execute o comando:
`ansible-playbook --ask-vault-password -i inventory playblook-mysql-backup-restore.yml`

No momento da execução, será solicitado a chave de acesso dos arquivos criptografado.

Obs.: Caso não estiver dentro da pasta onde esta a playbook, coloque o caminho completo.


