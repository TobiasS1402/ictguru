---
layout: post
author: tobias
title: "sqlite3.IntegrityError | Upgrading FreeNAS 11.3-U7 to TrueNAS Core 12.4-U4"
date: 2021-07-30T18:37:36+02:00
categories: [TrueNas]
description: "Troubleshooting sqlite integrity errors while upgrading FreeNAS to TrueNAS"
image: assets/images/tn-core-logo.png
---
During my upgrade of FreeNAS I encountered a sqlite3.IntegrityError stopping a active database upgrade. I will show you how to fix it and upgrade to TrueNAS.

I had tried upgrading FreeNAS a number of times but it all failed, all errors had something to do with the FreeNAS middlewere. Today I wanted to see if I could overcome by upgrading to TrueNas, a in-place upgrade gave me the same result. I then tried a clean install of FreeNAS, this did boot. But when I tried to import my configuration it failed once again, this time I had a file as a reference; **/data/update.failed** here I could see the following lines. As you can see **sqlite3.IntegrityError: UNIQUE constraint failed: directoryservice\_idmap\_rid.idmap\_rid\_domain_id**

```python
Operations to perform:
Apply all migrations: account, auth, contenttypes, directoryservice, jails, network, plugins, services, sessions, sharing, storage, system, tasks, vm
Running migrations:
Applying account.0010_auto_20190221_0824... OK
Applying account.0011_remove_netdata_user... OK
Applying directoryservice.0006_certificate_model... OK
Applying directoryservice.0007_migrate_to_nslcd... OK
Applying directoryservice.0008__alter_kerberos_principal... OK
Applying directoryservice.0009__add_createcomputer_to_ad... OK
Applying directoryservice.0010_encrypt_keytabs... OK
Applying directoryservice.0011_add_new_idmap_model...Traceback (most recent call last):
File "/usr/local/lib/python3.7/site-packages/django/db/backends/utils.py", line 64, in execute
return self.cursor.execute(sql, params)
File "/usr/local/www/freenasUI/freeadmin/sqlite3_ha/base.py", line 381, in execute
execute = super().execute(query, params)
<strong>sqlite3.IntegrityError: UNIQUE constraint failed: directoryservice_idmap_rid.idmap_rid_domain_id</strong>

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
File "/usr/local/www/freenasUI/manage.py", line 42, in <module>
execute_from_command_line(sys.argv)
```

[This thread](https://www.truenas.com/community/threads/upgrade-from-11-2-u8-to-11-3-u2-1-stuck-at-middlewared-setting-up-plugins-system-2-34.84833/) came to my rescue as on of the only sources online, they stated that deleting two rows in the freenas-v1.db from your configuration download could fix this issue. Download [SQLite browser](https://sqlitebrowser.org/) and unzip your FreeNAS/TrueNAS archive file. Then open your freenas-v1.db file with SQLite explorer. Here you can see a lot of tables for FreeNAS configuration. We need **directoryservice\_idmap\_rid**.

![Sqlite explorer for freenas.db](/assets/images/sqlite.png)

List the rows and remove row 2 and three by right clicking on the row number and clicking remove record. Also navigate to `/usr/local/etc/smb4.conf` and remove all idmap config parameters, restart the samba service. Source: [Samba wiki](https://wiki.samba.org/index.php/Updating_Samba#Failure_To_Access_Shares_on_Domain_Controllers_If_idmap_config_Parameters_Set_in_the_smb.conf_File)

![Sqlite explorer for directoryservice_idmap_rid](/assets/images/tempssssnip.png)

Save the modified database, repack it in a tar archive file and upload as your configuration to your NAS. You should install a clean version of the newest version, after booting you can import the new archive file to import your configuration. Additionally I had to backup my encryption keys and reset my ZFS pool.