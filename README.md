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
* Upload your Django repo into a git repo
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

Now you want to upload your Django project into a Git repo on your server:

    ssh username@username.webfactional.com
    cd webapps/git
    git init --bare repos/project_name.git

On your local machine:

    cd /path/to/your/django/repo/
    git remote add origin username@username.webfactional.com:/home/username/webapps/git/repos/project_name.git
    git push -u origin master

Now execute the playbook:

    ansible-playbook -i hosts site.yml
