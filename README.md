# lamp-migration-wordpress

It will install a LAMP environment with adequate to WordPress requirements. Configure the virtual-hosts in patch and recover the database from a backup as well as the root of the website.

## These other apache variables are defined under the **vars/** directory of the same role.

```
http_host: "site-name"
http_fqdn: "www.site-name.example.com"
http_conf: "site-name.conf"
http_port: "80"
```

We define the user with which the web-apps/web-site will be executed.

```
app_user: www-data
```

It is very important to define the name of the root compressed file, in the variable "bk_raiz". This variable have to same name of the backup file without extensión ".tar.gz".

```
bk_raiz: "example-bk.tar.gz"
```

## The variables of the Apache is define under the Ansible directories **host_vars**

The following variables are used by the apache.conf.j2 template

```
 http_host: "site-name"
 http_conf: "site-name.conf"
 server_admin: ""
```

## The variables of the db is define under the Ansible directories **host_vars**

I recomment to use a file with the name **host_var/server-hostname/vars/10_LAMP_WP.yml**.

```
 wp_mysql_db: "databasename"
 wp_mysql_user: "user name"
```

## The variables of the wp-cli is define under the Ansible directories "host_vars"**

We are goin to install wp-cli to mange WP in a CLI environment, we need to define an administrator to define the script.
I recomment to use a file with the name **host_var/server-hostname/vars/10_LAMP_WP.yml**.

```
#Administrador WP con wp-cli
adminwpcli: "nombreDelUsuarioAdministrador"
```

## Set root password, encrypted with Vault
define vault file, **host_var/server-hostname/vault/main.yml**

```
mariadb_root_password: "{{ vault_mariadb_root_password }}"
```

## Define database password tu use wordpress, encrypted with vault

```
wp_mysql_password: "{{ vault_wp_mysql_password }}"
``` 

## Ignore Files

In this project, we need to ignore the backup files, the reason for this is that these files are very, very large to store in this repository. With this in mind we are going to ignore the files under the "files" directories with the extension .sql and .tar.gz

Nota: Como estan definidos bajo el directorio default, son variables de bajo precedencia, pueden ser sobre escritas definiendo las mismas en host_vars y encriptadas con el vault, recomendado!! 


## Example Playbook
----------------

Including an example of how to use your role. In the section where it calls the role, we see that it just names it and we don't give it the full path. This is because the path where the roles are located is defined in the ansible.cfg file.

It is possible to use other tags to call specific tasks of the role, within each tasks/ there are defined tags. It is possible to execute each one individually by changing the tags of the playbook


```
---
# playbook, para configurar un entorno LAMP en un host  una vez creado
#

- name: Configuración de un entorno LAMP y Instalación de Wordpress
  hosts: fqdn-host
  remote_user: remote-user
  become: yes
   gather_facts: yes

  roles:
    - lamp-migration-wordpress  

  tags:
    - lamp
    - apache
    - php
    - mysql
    - wp-cli

```


Nota:
- Descomentar la extension curl (extension=curl) del archivo /etc/php/7.4/apache2/php.ini , o agregarla al final.
