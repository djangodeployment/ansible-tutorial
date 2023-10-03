=====================
Day 7: Special topics
=====================

Yesterday you used Ansible's ``file`` module to create directories, and
the ``template`` module to create the Django application's
``settings.py`` file. Today you will create two handlers that will
execute ``manage.py collectstatic`` and ``manage.py migrate``.

This assignment is going to take you at least 15 minutes, so we will not
do anything else today.

Your assignment is to create two handlers: a ``collect static`` handler
and a ``migrate`` handler. The handlers shall do the equivalent of these
commands:

.. code-block:: bash

   export PYTHONPATH=/etc/opt/$DJANGO_PROJECT
   export DJANGO_SETTINGS_MODULE=settings
   cd /opt/$DJANGO_PROJECT
   ./venv/bin/python manage.py collectstatic
   su $DJANGO_USER -c 'venv/bin/python manage.py migrate'

Hints:

 * Use the Ansible ``command`` module for both, and make sure you are
   using the virtualenv
 * Check the Ansible documentation on "Understanding privilege
   escalation: become" and "Setting the remote environment"
 * Make sure the ``git`` task and the ``settings.py`` task notify these
   handlers in addition to the compile handler
 * To make them run, do something that will cause the ``git`` task or
   the ``settings.py`` task to modify something; for example, logon to
   the server and add an empty line to ``settings.py``

Tomorrow we will see the solution and we will setup nginx.
