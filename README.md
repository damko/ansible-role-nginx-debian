Ansible Nginx Debian
=====

This Ansible role installs and configures the Nginx web server for a Debian system (squeeze or wheezy).

The user can specify any http configuration parameters they wish to apply their site.

Any number of sites can be added with configurations of your choice.

The installation will start only if the package is not installed in the system.

The configuration will run only right after the installation of the package so that future manual modification of the configuration won't be overwritten by running the task again.

Credits
------------
Good part of this role has been taken from Benno Joy's and Pierre Buyle's versions.

Requirements
------------

This role requires Ansible 1.4 or higher and platform requirements are listed
in the metadata file.

Role Variables
--------------

You can see the variables that can be passed to this role by checking the file `defaults/main.yml`.


Examples
========

1) Install nginx package but do not modify the configuration (so the debian's package configuration will be applied). Sites (vhosts) won't be installed too.

    - hosts: all

      gather_facts: yes

      roles:
      - {role: damko.nginx-debian,
         configure_nginx: false}


2) Install nginx and configure it with HTTP directives of choices, but with no sites (vhosts):

    - hosts: all

      gather_facts: yes

      roles:
      - {role: damko.nginx-debian,
         nginx_http_params: { sendfile: "on",
                              access_log: "/var/log/nginx/myaccess.log"},
                              nginx_sites: none }

Note: Please make sure the HTTP directives passed are valid, as this role
won't check for the validity of the directives. See the nginx documentation
for details.

3) Install nginx and add 1 site to the configuration.

    - hosts: all

      gather_facts: yes

      roles:
      - role: damko.nginx-debian,
        nginx_http_params:
          sendfile: "on"
          access_log: "/var/log/nginx/access.log"
          nginx_sites:
          - server:
             file_name: bar
             listen: 8080
             location1: {name: "/", try_files: "$uri $uri/ /index.html"}
             location2: {name: /images/, try_files: "$uri $uri/ /index.html"}

Note: Each site added is represented by list of hashes, and the configurations
generated are populated in /etc/nginx/sites-available/

The file name for the specific site configurtaion is specified in the hash
with the key "file_name", any valid server directives can be added to hash.
For location directive add the key "location" suffixed by a unique number, the
value for the location is hash, please make sure they are valid location
directives.

4) Install Nginx and add 2 sites (different method)

    ---
    - hosts: all
      roles:
        - role: damko.nginx-debian,
          nginx_http_params:
            sendfile: "on"
            access_log: "/var/log/nginx/access.log"
          nginx_sites:
           - server:
              file_name: foo
              listen: 8080
              server_name: localhost
              root: "/tmp/site1"
              location1: {name: /, try_files: "$uri $uri/ /index.html"}
              location2: {name: /images/, try_files: "$uri $uri/ /index.html"}
           - server:
              file_name: bar
              listen: 9090
              server_name: ansible
              root: "/tmp/site2"
              location1: {name: /, try_files: "$uri $uri/ /index.html"}
              location2: {name: /images/, try_files: "$uri $uri/ /index.html"}

Don't forget
------------

1) If you configured any site, don't forget to link them in the nginx/sites-enabled/ folder, in this way:

    Ex.:

    cd /etc/nginx/sites-enabled
    ln -s ../sites-available/site_file .

2) Don't forget to take care, in some way, of the updates for the Nginx package. This role doesn't deal with the package update.

Dependencies
------------

None

License
-------

BSD

Author Information
------------------

Damiano Venturin @damko


