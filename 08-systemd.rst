=======================
Day 8: service, systemd
=======================

Yesterday you were given the assignment of creating a ``collectstatic``
handler and a ``migrate`` handler. Here is the result to which you
should have arrived:

.. code-block:: yaml

   - name: Collect static files for {{ django_project }}
     environment:
       PYTHONPATH: /etc/opt/{{ django_project }}
       DJANGO_SETTINGS_MODULE: settings
     command: >
       /opt/{{ django_project }}/venv/bin/python
       manage.py collectstatic --noinput
     args:
       chdir: /opt/{{ django_project }}

   - name: Migrate {{ django_project }}
     environment:
       PYTHONPATH: /etc/opt/{{ django_project }}
       DJANGO_SETTINGS_MODULE: settings
     become: "{{ django_user }}"
     command: >
       /opt/{{ django_project }}/venv/bin/python
       manage.py migrate
     args:
       chdir: /opt/{{ django_project }}
 
The service module
==================

Your next assignment is to configure nginx. For this you will create two
tasks (nginx configuration file, nginx symbolic link) and one handler
(reload nginx).

1. Create the nginx configuration file
--------------------------------------

Use the ``template`` Ansible module to create file
``/etc/nginx/sites-available/DOMAIN``. The template file is this:

.. code-block:: nginx

   server {
      listen 80;
      listen [::]:80;
      server_name {{ django_domain }};
      root /var/www/{{ django_domain }};
      location / {
          proxy_pass http://localhost:{{ django_port }};
          proxy_set_header Host $http_host;
      }
      location /static/ {
          alias /var/cache/{{ django_project }}/static/;
      }
      location /media/ {
          alias /var/opt/{{ django_project }}/media/;
      }
   }

Add the ``django_port`` parameter to the playbook.

2. Create the symbolic link
---------------------------

Use the ``file`` module to do the equivalent of these commands:

.. code-block:: bash

   cd /etc/nginx/sites-enabled
   ln -s ../sites-available/$DOMAIN

3. Reload nginx
---------------

Create a handler that uses the ``service`` module to perform the
equivalent of this command:

.. code-block:: bash

   service nginx reload

Make sure both tasks notify the handler.

Run the playbook and verify you did everything right.

The systemd module
==================

Your final assignment is to install and configure Gunicorn. This will be
three tasks and one handler.

1. Create a system service
--------------------------

Use the ``template`` module to create file
``/etc/systemd/system/DJANGO_PROJECT.service``. These are the contents
of the template:

.. code-block:: ini

   [Unit]
   Description={{ django_project }}

   [Service]
   User={{ django_user }}
   Group={{ django_user }}
   WorkingDirectory=/opt/{{ django_project }}
   Environment="PYTHONPATH=/etc/opt/{{ django_project }}"
   Environment="DJANGO_SETTINGS_MODULE=settings"
   ExecStart=/opt/{{ django_project }}/venv/bin/gunicorn \
     --workers={{ gunicorn_workers }} \
     --bind=127.0.0.1:{{ django_port }} \
     --log-file=/var/log/{{ django_project }}/gunicorn.log \
     {{ django_project }}.wsgi:application

   [Install]
   WantedBy=multi-user.target

2. Install Gunicorn
-------------------

Use the ``pip`` module to install Gunicorn in the virtualenv.

3. Enable and start the service
-------------------------------

Use the ``systemd`` module to do the equivalent of these commands:

.. code-block:: bash

   systemctl enable $DJANGO_PROJECT
   service $DJANGO_PROJECT start

4. Restart the service
----------------------

Create a handler that uses the ``service`` module to do the equivalent
of this command:

.. code-block:: bash

   service $DJANGO_PROJECT restart

Make sure the first two of the above tasks notify it, so that the
service is restarted whenever gunicorn is upgraded or the service file
changes. Also consider which other modifications should result in a
restarting of the service, and make the related tasks also notify the
handler.

When you finish, check if it's running. Visit the site on your browser
and see if it works. If you are getting a server error, the most likely
case is that you did not run ``collectstatic`` or ``migrate``. Maybe you
forgot to add the handlers to the ``notify`` option of the git install
task or the settings.py task.

Congratulations! You finished deployment using Ansible. The only thing
that needs to be fixed is that you might want to hide the secret key
from people who have access to your Ansible repository. So tomorrow we
will learn about the Ansible vault.
