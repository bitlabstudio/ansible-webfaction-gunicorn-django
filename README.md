# Ansible Playbook for Django on Webfaction

DO NOT USE THIS! WORK IN PROGRESS

This is an Ansible playbook that sets up a new Webfaction server. You cank of
course, just use the 1-click installer provided by Webfaction, but if you want
to run your project professionally, you will outgrow that installer in no time.

This playbook sets up Django and serves it via gunicorn & gevent. Static files
will be handled by Webfaction's global nginx instance, as usual. Additionally
we will install statsd and graphite, which will enable you to create awesome
dashboards to monitor any aspect of your app / server. Supervisor will be used
to keep all components up and running. A cronjob will restart Supervisor every
5 mintues, in case Webfaction killed the process or re-booted the server.

We assume that you have a Django project setup on your local machine. A good
starting point to setup a new Django project is our
[django-project-template](https://github.com/bitmazk/django-project-template).

Using this playbook involves several steps:

* Setup your Webfaction apps and websites in the control panel
* Change the variables of this playbook
* Upload your Django repo into a git repo and clone it on the server
* Create your local_settings.py
* Run the playbook

# Prerequisites

## Setup apps and websites

First, login to the Webfaction control panel and create the following
applications:

* `gunicorn` - Custom app listening on port
* `static` - Static only (no .htaccess) / "expires max"
* `media` - Static only (no .htaccess) / "expires max"
* `git` - Git
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

The snippet above assumes that you host the repo yourself. If you are hosting
it somewhere else, of course the clone command will be different.

Once you have cloned the repository, create your `local_settings.py`. This
file should be in `.gitignore` so having it lying around in this folder will
be no problem but it will make the first deployment easier.

    ssh username@username.webfactional.com
    cd ~/src/project_name/project_name/project_name/settings
    cp local_settings.py.sample local_settings.py
    vim local_settings.py

# Execute the playbook

Now execute the playbook:

    ansible-playbook -i hosts site.yml
