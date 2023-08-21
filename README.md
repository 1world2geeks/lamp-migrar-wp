# lamp-migration-wordpress

It will install a LAMP environment with adequate to WordPress requirements. Configure the virtual-hosts in patch and recover the database from a backup as well as the root of the website.

## These other apache variables

You can defined that variables under the **vars/** directory of the same role. But i recommend to define this variables under the Ansible directories **host_vars**.

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

The following variables are used by the apache.conf.j2 template

```
 http_host: "site-name"
 http_conf: "site-name.conf"
 server_admin: ""
```

## Backups root site and variables definitions 

It is very important to define the name of the root compressed file, in the variable "bk_raiz". This variable have to same name of the backup file with that extensión, ex: "webtest.tar.gz". Please it is very important too with the name of extractiond folder have the same name with the variable **http_host**, becouse the task apache2server.yml use this name to manipulation permisions. 

```
bk_raiz: "webtest.tar.gz"
```

I recomment to create a folder with the name files under your host_var, ex: **host_var/server-hostname/vars/files**. In this way you will have the bk of each host located correctly on the destination server, if you use this method it is important that you add a **.gitignore** file defining which file extensions **git** will ignore, the reason for this is that these files are very very large to store in this repository. With this in mind we are going to ignore the files under the "files" directories with the extension .sql and .tar.gz.

There is already a file in the role, it would just be to copy it to the folder **host_var/server-hostname/vars/files**.

In this version the database backups are stored with **.sql** 
extension. 

## The variables of the db

I recomment to use a file with the name **host_var/server-hostname/vars/10_lamp_wp.yml**.

It is important to know that the database name is defined without the extension **.sql**.

```
 wp_mysql_db: "databasename"
 wp_mysql_user: "user name"
```

## Set root password, encrypted with Vault

Define vault file, **host_var/server-hostname/vault/main.yml**. Remember create and edit this file with **ansible-vault** command. Is you not know this command, please see the next [link](https://docs.ansible.com/ansible/latest/cli/ansible-vault.html#ansible-vault)

```
mariadb_root_password: "{{ vault_mariadb_root_password }}"
```

## Define database password tu use wordpress, encrypted with vault

```
wp_mysql_password: "{{ vault_wp_mysql_password }}"
``` 


## Example Playbook
----------------

Including an example of how to use your role. In the section where it calls the role, we see that it just names it and we don't give it the full path. This is because the path where the roles are located is defined in the ansible.cfg file.

It is possible to use other tags to call specific tasks of the role, within each tasks/ there are defined tags. It is possible to execute each one individually by changing the tags of the playbook


```
---

- name: config a LAMP environment and install WP
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
