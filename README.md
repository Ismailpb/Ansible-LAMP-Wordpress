### Ansible to Install Wordpress on LAMP Stack on Amazon Linux 2

Ansible is an open-source automation tool, or platform, used in configuration management, application deployment, intraservice orchestration, and provisioning. Ansible is used for the multi-tier deployments and it models all of IT infrastructure into one deployment instead of handling each one separately. There are no agents and no custom security architecture is required to be used in the Ansible architecture.

#### Features of Ansible
- We need to run the process only on master machine and it will automatically get transferred to client servers.
- Agentless: You don’t need to install any other software or firewall ports on the client systems you want to automate. You also don’t have to set up a separate management structure.
- Secure and consistent – Since the Ansible uses SSH and Python it is very secure and the operations are flawless.

#### Ansible installation
> Note : I'm using EC2 instance to perform the task

```sh
amazon-linux-extras install ansible2 -y
```
- The configuaration file of ansible will be
```sh
/etc/ansible/ansible.cfg
```
#### Inventory File:
The Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. The default location for the inventory file is /etc/ansible/hosts. You can also create project-specific inventory files in alternate locations.

I have created a custom inventory file for my project..
```sh
vim remote
```
```sh
[amazon]
<IP>  ansible_user="ec2-user" ansible_port=22 ansible_ssh_private_key_file="ansible.pem"
```
> [amazon] is the group name
I've created only one client server. If we want to add more, we can add under the same group name if all are on same OS
If the server is different OS, we need to create another group.

> Note: Ansible is using SSH protocol to connect to the client servers.

There are two methods to pass commands to ansible client
1. [Adhoc method](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html) : Ansible ad hoc commands are written for a very specific task. It  is a one-line command that lets you perform basic tasks efficiently without writing playbooks.
The syntax of ad-hoc commands to run is as follows and see some of the ad-hoc commands as well.
```sh
$ ansible [pattern] -m [module] -a "[module options]"
```
2. [Playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html) : Playbooks are files consisting of your written code, and they are written in YAML format, which defines the tasks and executes them through the Ansible. Playbooks may include one or more plays. Plays defines a set of activities or tasks to be run on hosts of inventory file.
- We need to strictly follow the syntax suggested by ansible, otherwise it will throw the error. Let's see in the following, some of the suggestions made by ansible.
- Playbooks always start in a YAML file with three dashes.
- Items that start with a single dash shall be treated as list items.
- Whitespaces matters in ansible, so the items are always defined by indentation.
 
To Run the playbook, we need to execute the command shown as follows.
$ ansible-playbook

E.g.: $ ansible-playbook sample.yaml


##### _Checking Ssh Connectivity Between Master & Client With Ping Module_
```sh
# ansible -i remote amazon  -m  ping 
```
> I've created a custom host/inventory file with name remote(which contains group, ec2 private IP, and the SSH key). 
This will check mentioned command on the server defined in remote file under the group 'amazon'

> -m : module name
Ansible uses modules to run instruction. Here ping is the module name

```sh
<IP> | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
```
> How ansible code works:
  Ansible first convert the code into python, then the code is transferred to client using scp and execute the python script on the remote server. 
  Python module must be installed on the client and master server

If we have mutiple groups, we can specify the group names or we can select all the groups using 'all'
```sh
ansible -i remote amazon,amazon1  -m  ping 
```

This will perform the command on all the groups mentioned in the inventory file. 
```sh
ansible -i remote all  -m  ping 
```
This will perform the command on all the groups mentioned in the inventory file. 

# Installation

We can go through step by step installation.

Before to start with the installation we need to set some variables for the lamp stack installation. For that I have created a file named "variables.var" and added the variable values. You can check which all variables have been mentioned in the file.

```
cat variables.var

---

domain_name: "xyz.com"
url: "https://wordpress.org/latest.tar.gz"
httpd_port: "80"
httpd_user: "apache"
httpd_group: "apache"

mysql_root_password: "mysql@123"
wp_db_name: "wordpress"
wp_db_user: "wordpress"
wp_db_password: "wordpress"
```

Also we need to create a default virtual host file, wordpress configuration file and mysql config file.

```
cat virtualhost.conf.tmpl
---

 <virtualhost *:{{ httpd_port }}>
  
  servername {{ domain_name }}
  documentroot /var/www/html/{{ domain_name }} 
  directoryindex index.php index.html
    
  <directory "/var/www/html/{{ domain_name }}">
     allowoverride all
  </directory>

</virtualhost>
```

```
cat my.cnf.tmpl
---
[client]
user=root
password= {{ mysql_root_password }}
```

```
cat wp-config.tmpl

---
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the installation.
 * You don't have to use the web site, you can copy this file to "wp-config.php"
 * and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * Database settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://wordpress.org/support/article/editing-wp-config-php/
 *
 * @package WordPress
 */

// ** Database settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', '{{ wp_db_name }}' );

/** Database username */
define( 'DB_USER', '{{ wp_db_user }}' );

/** Database password */
define( 'DB_PASSWORD', '{{ wp_db_password }}' );

/** Database hostname */
define( 'DB_HOST', 'localhost' );

/** Database charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );

/** The database collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

/**#@+
 * Authentication unique keys and salts.
 *
 * Change these to different unique phrases! You can generate these using
 * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
 *
 * You can change these at any point in time to invalidate all existing cookies.
 * This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );

/**#@-*/

/**
 * WordPress database table prefix.
 *
 * You can have multiple installations in one database if you give each
 * a unique prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wp_';

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 *
 * For information on other constants that can be used for debugging,
 * visit the documentation.
 *
 * @link https://wordpress.org/support/article/debugging-in-wordpress/
 */
define( 'WP_DEBUG', false );

/* Add any custom values between this line and the "stop editing" line. */



/* That's all, stop editing! Happy publishing. */

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
	define( 'ABSPATH', __DIR__ . '/' );
}

/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```

Once the files were ready we can start with the installation of lampstack.

For that here I used the task for lamp-stack on separate playbook and defined a call to that on the main playbook

### 1. Installation of Apache-PHP

For that I have created a file named "apache-phpinstallation.yml".

```
---

    - name: "Apache - Installing httpd server"
      yum:
        name: httpd
        state: present
      tags:
        - httpd

    - name: "Apache - Installing php Support"
      shell: "amazon-linux-extras install php7.4 -y"
      tags:
        - httpd  
    
    - name: "Apache - Creating VirtualHost"
      template:
        src: virtualhost.conf.tmpl
        dest: "/etc/httpd/conf.d/{{domain_name}}.conf"
      tags:
        - httpd
      notify:
        - httpd_restart
        
    - name: "Apache - Creating DocumentRoot"
      file:
        path: "/var/www/html/{{ domain_name }}"
        state: directory
        owner: "{{ httpd_user }}"
        group: "{{ httpd_group  }}"
      tags:
        - httpd

    - name: "Apache - Restart/Enabling Service"
      service:
        name: httpd
        state: restarted
        enabled: true
      tags:
        - httpd
```

The yum module will install httpd.
Using tags 'httpd', we have better control over installation. We can install the httpd alone by using the tag.
Also you can see the "notify" as I have set an handler to restart the httpd service when the whole process complete.


### 2.Installation of mariadb

For that I have created a file named "mariadb-installation.yml".

```
---

- name: "Installing Mariadb and python mysql module"
  yum:
    name:
      - mariadb-server
      - MySQL-python
    state: present
  register: mysql_status

- name: "mariadb-restart"
  when: mysql_status.changed == true
  service:
    name: mariadb
    state: restarted
    enabled: true
  tags:
    - mariadb


- name: "Setup Root password"
  when: mysql_status.changed == true
  mysql_user:
    login_user: root
    login_password: ""
    name: root
    password: "{{ mysql_root_password }}"
    host_all: yes
  tags:
    - mariadb


- name: "Creating my.cnf file"
  when: mysql_status.changed == true
  template:
    src: "my.cnf.tmpl"
    dest: "/root/.my.cnf"
    owner: "root"
    group: "root"
  tags:
    - mariadb


- name: "Removing Anonymous User"
  when: mysql_status.changed == true
  mysql_user:
    config_file: /root/.my.cnf   
    name: ""
    state: absent
    host_all: true
  tags:
    - mariadb


- name: "Removing Test Database"
  when: mysql_status.changed == true
  mysql_db:
    config_file: /root/.my.cnf
    name: "test"
    state: absent
  tags:
    - mariadb


- name: "Creating Wordpress Database Called {{ wp_db_name }}"
  mysql_db:
    config_file: /root/.my.cnf
    name: "{{ wp_db_name }}"
    state: present
  tags:
    - mariadb


- name: "Creating Wordpress User {{ wp_db_user }} and setting privileges"
  mysql_user:
    config_file: /root/.my.cnf
    user: "{{ wp_db_user }}"
    state: present
    password: "{{ wp_db_password }}"
    priv: "{{ wp_db_name }}.*:ALL"
  tags:
    - mariadb
```

The yum module will install mariadb and mysql-python (to communicate mysql with python) and store the status on the register variable "mysql_status"

> when: mysql_status.changed == true
Once mariadb is installed and whenever the register variable "mysql_status" value is changed to true, it will perform restart of the service mariadb.

Regarding with the mysql configuration

1. During intial installation, we will login to mysql root user with null password. 
2. We need to set root password for mysql
3. Removed all anonymous user and test db by loging in as root with new password
4. Created wordpress database using mysql_db module
5. Created wordpress user using mysql_user module, defined the password and provided full access to the database

> ignore_errors: true    _## once the mysql is installed, and root password is set, when we re-run the play, it will thow error as mysql root login can't be possible using null password. As a result the playbook will exit the process. 
To avoid that we add  ignore_errors: true, even though there is error, the playbook will continue to check with next task.

### 3.Configuring Wordpress

For that I have created a file named wordpress-configuration.yml
```
---
- name: "Downloading Wordpress"
  get_url:
    url: "{{ url }}"
    dest: "/tmp/wordpress.tar.gz"


- name: "Extracting Wordpress tar file"
  unarchive:
    src: "/tmp/wordpress.tar.gz"
    dest: "/tmp/"
    remote_src: true


- name: "Copying Wordpress files to  document root of the domain"
  copy:
    src: "/tmp/wordpress/"
    dest: "/var/www/html/{{ domain_name }}/"
    owner: "{{ httpd_user }}"
    group: "{{ httpd_group }}"
    remote_src: true

- name: "Copying wp-config file"
  template:
    src: "wp.config.tmpl"
    dest: "/var/www/html/{{ domain_name }}/wp-config.php"
    owner: "{{ httpd_user }}"
    group: "{{ httpd_group }}"
```

How it works

1. Download wordpress defined in the variable name "url" using get_url module and saved it to a pre-defined name so that we can extract it.
2. Extract the content using unarchive module
remote_src: true  ## if this is not defined, by default, the src path mentioned on the unarchive will check on Master server and since it's not present, it will throw error. If remote_src is enabled, it will check on the remote server itself.
3. copy the contents from the extracted directory to our home directory. 
4. copy the wp-config file from master server "wp-config.tmpl" as wp-config.php on the remote server's document root so that the database informations can be defined. 

Once the above installation completed, we can proceed to create the main playbook file ie "main.yml".
```
---
- name: "LAMP-Wordpress"
  hosts: amazon
  become: true
  vars_files:
    - variables.tmpl

  tasks:
    

    - include_tasks: apache-phpinstallation.yml
    - include_tasks: mariadb-installation.yml
    - include_tasks: wordpress-configuration.yml


    - name: "Post-Installation - CleanUp"
      file:
        path: "{{ item }}"
        state: absent
      with_items:
          - "/tmp/wordpress.tar.gz"
          - "/tmp/wordpress"


  handlers:
    - name: "httpd_restart"
      service:
        name: httpd
        state: restarted
        enabled: true
```
