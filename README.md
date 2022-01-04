# Ansible-LAMP-Wordpress


### OutPut

```

PLAY [LAMP-Wordpress] *************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************
The authenticity of host '172.31.33.157 (172.31.33.157)' can't be established.
ECDSA key fingerprint is SHA256:qDukVPzPdzCYjs5EoHYBNCnC5IcRZ9Ss7ipN2IgkvFQ.
ECDSA key fingerprint is MD5:19:50:8c:5a:66:1f:fa:77:9e:7b:c1:10:91:d3:1c:4d.
Are you sure you want to continue connecting (yes/no)? yes
[WARNING]: Platform linux on host 172.31.33.157 is using the discovered Python interpreter at /usr/bin/python, but future installation of
another Python interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for
more information.
ok: [172.31.33.157]

TASK [include_tasks] **************************************************************************************************************************
included: /root/apache-php.yml for 172.31.33.157

TASK [Apache - Installing httpd server] *******************************************************************************************************
changed: [172.31.33.157]

TASK [Apache - Installing php Support] ********************************************************************************************************
changed: [172.31.33.157]

TASK [Apache - Creating VirtualHost] **********************************************************************************************************
changed: [172.31.33.157]

TASK [Apache - Creating DocumentRoot] *********************************************************************************************************
changed: [172.31.33.157]

TASK [Apache - Restart/Enabling Service] ******************************************************************************************************
changed: [172.31.33.157]

TASK [include_tasks] **************************************************************************************************************************
included: /root/maria-db.yml for 172.31.33.157

TASK [Insatllaing mariadb-server] *************************************************************************************************************
changed: [172.31.33.157]

TASK [Enabling/Resatrting mariadb] ************************************************************************************************************
changed: [172.31.33.157]

TASK [Creating root password] *****************************************************************************************************************
[WARNING]: Module did not set no_log for update_password
changed: [172.31.33.157]

TASK [Creating my.cnf file] *******************************************************************************************************************
changed: [172.31.33.157]

TASK [Removing Anonymous User] ****************************************************************************************************************
changed: [172.31.33.157]

TASK [Removing Test Database] *****************************************************************************************************************
changed: [172.31.33.157]

TASK [Creating Wordpress Database Called wordpress] *******************************************************************************************
changed: [172.31.33.157]

TASK [Creating Wordpress User wordpress and setting privileges] *******************************************************************************
changed: [172.31.33.157]

TASK [include_tasks] **************************************************************************************************************************
included: /root/wordpress.yml for 172.31.33.157

TASK [Downloading Wordpress] ******************************************************************************************************************
changed: [172.31.33.157]

TASK [Extracting Wordpress tar file] **********************************************************************************************************
changed: [172.31.33.157]

TASK [Copying Wordpress files to  document root of the domain] ********************************************************************************
changed: [172.31.33.157]

TASK [Copying wp-config file] *****************************************************************************************************************
changed: [172.31.33.157]

TASK [Post-Installation - CleanUp] ************************************************************************************************************
changed: [172.31.33.157] => (item=/tmp/wordpress.tar.gz)
changed: [172.31.33.157] => (item=/tmp/wordpress)

TASK [Post-Installation - Restarting] *********************************************************************************************************
changed: [172.31.33.157] => (item=httpd)
changed: [172.31.33.157] => (item=mariadb)

PLAY RECAP ************************************************************************************************************************************
172.31.33.157              : ok=23   changed=19   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 

```





![image](https://github.com/Ismailpb/Ansible-LAMP-Wordpress/blob/a4c7023ced9fe78bee1d61964cf1d20a71a41288/Screenshot%20from%202022-01-05%2004-53-22.png)
