# Ansible Playbook for Django on Webfaction

DO NOT USE THIS! PLAYBOOK NOT EXECUTABLE! WORK IN PROGRESS!

## Overview

This is an Ansible playbook that sets up a new Webfaction server. You can, of
course, just use the 1-click installer which is provided by Webfaction, but if
you want to run your project professionally, you will outgrow that installer in
no time.

This playbook sets up Django and serves it via nginx & uWSGI. This needs less
memory than the Apache processes that Webfaction's 1-click installer would
spawn and at the same time it's faster and allows more concurrent requests per
second.

Static files will be handled by Webfaction's global nginx instance, as usual,
mainly because I got used to it and it doesn't add memory usage to your
account. The only downside here is that you cannot enable gzip compression for
your static files. Webfaction disabled this on purpose because of security
converns. We could use our own nginx instance for this as well, and maybe we
will in the future.

Additionally we will install statsd, carbon, whisper and graphite-api, which
will enable you to create awesome dashboards to monitor any aspect of your app
/ server. In a different repository we will create a reusable Django app that
works as a proxy before your graphite-api installation. This would enable you
to add authentication and authorization in front of your graphite graphs to
ensure that one user cannot see the graphs of another user (out of the box,
graphite does not assume a multi-user setup).

Supervisor will be used to keep all components up and running. A
cronjob will restart Supervisor every 5 mintues, in case Webfaction killed the
process or re-booted the server.

## Installation in a nutshell

We assume that you have a Django project setup on your local machine. A good
starting point to setup a new Django project is our
[django-project-template](https://github.com/bitmazk/django-project-template).

Using this playbook involves several steps:

* Setup your Webfaction apps and websites in the control panel
* Change the hosts variables of this playbook
* Upload your Django repo into a git repo and clone it on the server
* Create your local_settings.py
* Run the playbook

# Prerequisites

## Setup apps and websites

First, login to the Webfaction control panel and create the following
applications:

* `git` - Git
* `nginx` - Custom app listening on port
* `statsd` - Custom app listening on port
* `carbon` - Custom app listening on port
* `whisper` - Custom app listening on port
* `graphite` - Custom app listening on port
* `static` - Static only (no .htaccess) / "expires max"
* `media` - Static only (no .htaccess) / "expires max"
* Delete the default `htdocs` app

You might also want to create a Postgres database and an email account.

## Change variables

Next, copy the `hosts.example` file and change the USERNAME to your Webfaction
account name.

    cp hosts.example hosts
    vim hosts

Similarly, copy the `vars/external_vars.yml` file and change all values to
your liking. The values you need to change are all uppercase letters.

    cd vars
    cp external_vars.yml.example external_vars.yml
    vim external_vars.yml

## Prepare the git repository

We assume that you have a Django project on your local machine. Naturally, you
should have that project under version control. You might want to host your
project on Bitbucket or Github, or you might just want to host the repository
on the Webfaction server itself.

If you want to host it yourself, create a new repository on your server:

    ssh username@username.webfactional.com
    cd webapps/git
    git init --bare repos/project_name.git

On your local machine:

    cd /path/to/your/django/repo/
    git remote add origin username@username.webfactional.com:/home/username/webapps/git/repos/project_name.git
    git push -u origin master

Regardless of where you host it, you should now clone the repository on your
server:

    ssh username@username.webfactional.com
    mkdir -p ~/src
    cd ~/src
    git clone ~/webapps/git/repos/project_name.git
    git submodule init
    git submodule update

The snippet above assumes that you host the repo yourself. If you are hosting
it somewhere else, of course the clone command will be different.

Once you have cloned the repository, create your `local_settings.py`. This
file should be in `.gitignore` so having it lying around in this folder will
be no problem but it will make the first deployment easier.

    ssh username@username.webfactional.com
    cd ~/src/project_name/project_name/project_name/settings
    cp local_settings.py.sample local_settings.py
    vim local_settings.py

# Usage

Now execute the playbook:

    ansible-playbook -i hosts site.yml

If you have correctly setup your Website in the webfaction panel, you should
be able to see your Django project now if you visit 
`http://username.webfactional.com`.
