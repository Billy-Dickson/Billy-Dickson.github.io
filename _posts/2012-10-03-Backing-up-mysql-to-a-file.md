---
title: Backing up a MYSQL database to a file
date: 2012-10-03
draft: false
categories: [Homelab, Linux]
tags: [homelab, linux]     # TAG names should always be lowercase
image: 
   path: ../assets/img/posts/headers/Tux_Linux.webp
---

This is reminder to myself (but if it's useful, then feel free). Forgot the name of the database (Duh!) 😊

```bash
mysql -u root -p

Enter password:

Welcome to the MySQL monitor.Â  Commands end with ; or \g.
Your MySQL connection id is 887
Server version: 5.1.63-0ubuntu0.10.04.1 (Ubuntu)

Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| billy_wordpress    |
| mysql              |
+--------------------+
3 rows in set (0.01 sec)

mysql> quit
Bye
```

 Backup wordpress MySQL database

```bash
mysqldump -u root -p billy_wordpress > wordpress.sql
```

Encrypt using gpg for offsite storage, remember your not using your (or someone's) public key to encrypt the file, your using a specific password so.

> Tip: Keep a note of the password.
{: .prompt-tip }

```bash
gpg -c wordpress.sql
```

The encryted file will have a gpg extension, so in my case it's called "wordpress.sql.gpg".

How to Decrypt the file.

This will give you 2 files, the original encrypted wordpress.sql.gpg file, and the unencryped wordpress.sql file.

```bash
gpg -o wordpress.sql -d wordpress.sql.gpg
```

## References

- Backup and Restore [MySQL Databases](https://www.thegeekstuff.com/2008/09/backup-and-restore-mysql-database-using-mysqldump/)
