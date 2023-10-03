===================================================
Day 1: Ansible installation and other prerequisites
===================================================

Ansible is a strange beast, and the best way to learn it is little by
little every day. This course has short lessons, one for each day, for a
total of about two weeks. You will need to work 15–30 minutes on each
lesson. This is the first one.

We will be running Ansible on your local machine (which we will use to
control a server), so first of all you need to install Ansible.

Installing Ansible on GNU/Linux or Mac OS X
===========================================

Ansible runs only on Unixlike machines, so your local machine must be
running Mac OS X or GNU/Linux or similar. If your laptop runs Windows,
please read the section about Windows further below.

Here is how to install Ansible on a virtualenv on a **Debian or Ubuntu**
machine::

   apt install ansible

If you prefer to install it with pip, here is a way::

   apt install python3-paramiko python3-jinja2 python3-yaml
   mkvirtualenv --python=/usr/bin/python3 --system-site-packages ansible-course
   pip install ansible

I don’t know how to do it on **Mac OS X**, but it shouldn’t be difficult
to do the equivalent of the above commands. It’s fine if you install
``jinja2`` and ``yaml`` with ``pip``, but ``paramiko`` can be tricky
(because of its crypto dependencies), so it’s better if you install a
system package.

To verify that it has been installed, enter ``ansible --help``; it should
give you a usage message.

Hey, I use Windows!
===================

Ansible runs **only on Unixlike machines**. If you have Windows, you
must install a GNU/Linux virtual machine (e.g. using Virtualbox), or use
the WSL (Windows Subsystem for Linux), or Docker Desktop, or similar.

During the course, you will be editing files a lot. You will be creating
files, creating directories, renaming files and directories, copying
stuff from one file to another, and so on. It will be really better if
you do this in your favorite editor, and be graphically viewing the
directory tree if you are accustomed to that. So logging into your
virtual machine and working in there with vim is really suboptimal
(unless this is the way you commonly work). It’s probably better if you
work in a directory shared with Windows, in which case you will do all
editing and file management in Windows, and use the virtual machine only
in order to execute Ansible. So go ahead and create this shared
directory and make sure you can access it both from Windows and from the
virtual machine.

Creating a server
=================

During the course we will be installing Ansible on a Ubuntu 22.04
server. You will need to create one. I like creating test servers on
Hetzner but you can use any service you like, or even setup a virtual
machine on your local machine if you feel comfortable (if you do it
locally you may need to create some tricky NAT configuration; I've
chosen to stay away from all that and use Hetzner instead). A 1-GB-RAM
server is fine.

If, like Hetzner, your virtual server is charged by the hour, it's fine
to destroy it after each lesson and recreate it before the next one.

To avoid typing passwords, it will be really better to use ssh keys to
login. If you have difficulty doing this, my `Deploying Django book`_
demystifies it in the first chapter.
 
Other things you need to do
===========================

One of the things we will learn in the course is reading the Ansible
library reference. In order to make that easy, please create a
quicksearch in your browser with keyword “ansible” and URL
``https://docs.ansible.com/ansible/latest/collections/ansible/builtin/%s_module.html``
(if you don't understand what I'm talking about please read `An easy tip
to quickly access the Python reference`_).

You must have clear understanding of ``virtualenv``, particularly of the
fact that it does nothing more than alter the system path and the python
path. If this is not entirely clear to you, please read `virtualenv
demystified`_.

We will be deploying Django with nginx, gunicorn and systemd,
essentially following `this cheat sheet`_. In order to be able to
concentrate on Ansible, it will be best if you have at least a general
understanding of it. If it looks too mysterious, you might want to pause
learning Ansible and instead read the `Deploying Django book`_, then come
back here.

Finally, we will be practicing with a specific Django application,
greeter. Get a quick overview of greeter by reading `its README`_.

This is all for today. Tomorrow we will start configuring Ansible and
check that it can connect to the server.

.. _Deploying Django book: https://djangodeployment.com
.. _An easy tip to quickly access the Python reference: https://djangodeployment.com/2017/03/31/an-easy-tip-to-access-the-docs/
.. _virtualenv demystified: https://djangodeployment.com/2016/11/01/virtualenv-demystified/
.. _this cheat sheet: https://djangodeployment.com/wp-content/uploads/2017/03/Django-deployment-cheatsheet.pdf
.. _its README: https://github.com/djangodeployment/greeter/
