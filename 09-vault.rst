========================
Day 9: The Ansible vault
========================

Yesterday you finished your work on Ansible and you can now deploy a
Django application with it.

One thing we didn't do yet is add any commits to the git repository. We
did create a repository before we even created ``ansible.cfg``, and you
were probably expecting me to tell you to do something with it. Well,
there's nothing that you don't already know. At some point you need to
add and commit the files you created to the repository. Any files that
shouldn't be in the repository must already be listed in ``.gitignore``,
but if you execute ``git add .`` and then ``git status`` you will be
able to see for yourself.

The need to manage your secrets
===============================

Your playbook should be looking like this:

.. code-block:: yaml

   ---
   - name: Install greeter server
     hosts: example.com
     tasks:
     - name: Install pillow (required by greeter)
       apt: name=python3-pillow

     roles:
       - role: django
         django_project: greeter
         django_project_repository: https://github.com/djangodeployment/greeter.git
         django_user: greeter
         django_project_python: python3
         django_project_site_packages: yes
         django_domain: example.com
         django_secret_key: eu4ke2eiQu1cizoh8ac3
         django_port: 8000
         gunicorn_workers: 4

There is a problem there: your django secret key is in the playbook.
When you deploy a real project, there will also be the PostgreSQL
password, the email server password, your SSL private key, and possibly
more secrets.

Maybe you don't want these secrets in your playbook. Occasionally you
will need to provide access to the repository to another person. That
other person might be OK to see how you've set up your infrastructure,
but not to actually have access to such infrastructure.

The solution is to use the Ansible vault.

Setting up the vault password
=============================

The vault is a collection of secrets, encrypted with the use of a master
password, called the "vault password".

You could add the ``--ask-vault-pass`` option each time you execute an
Ansible command and it would ask you for the password each time; but
this would be very inconvenient. Instead, we will store the password in
a file.

1. Git ignore file
------------------

Change the ``.gitignore`` file to add ``.vault-password`` to the
excluded files::

   *.retry
   .vault-password

2. Vault password file
----------------------

Create a master password and store it in file ``.vault-password``. The
file should contain only the password, like this::

   Waim6Eichoh9vahDoogu

3. ansible.cfg
--------------

Tell Ansible to read the password from that file by default, by editing
``ansible.cfg`` as follows:

.. code-block:: ini

   [defaults]
   hostfile=hosts
   vault_password_file=vault-password

   [ssh_connection]
   pipelining=True

Adding the secrets to the vault
===============================

1. Execute this command:

   .. code-block:: bash

      ansible-vault edit group_vars/secrets.yml

   This will open an editor. Since the file does not exist, it will be
   created and will be empty. Specify these contents:

   .. code-block:: yaml

      ---
      greeter_secret_key: eu4ke2eiQu1cizoh8ac3

   When you exit the editor, Ansible will encrypt the file contents (using
   the password in ``.vault-password``).

2. Now change ``site.yml`` as follows:

   .. code-block:: yaml

      ---
      - name: Install greeter server
        hosts: example.com
        vars_files:
          - group_vars/secrets.yml
        tasks:
          [...]
        roles:
          - role: django
              django_project: greeter
              [...]
              django_secret_key: "{{ greeter_secret_key }}"

When you run the playbook, Ansible will automatically detect that
``group_vars/secrets.yml`` is encrypted, and it will decrypt it on the
fly using the password in ``.vault-password``.

This concludes our introduction to Ansible. I hope you learned much and
that you learned it painlessly.
