DO NOT USE THIS! WORK IN PROGRESS

This is an Ansible playbook that sets up a new Webfaction server.

First, login to the Webfaction control panel and create the following
applications:

* `gunicorn` - Custom app listening on port
* `static` - Static only (no .htaccess) / "expires max"
* `media` - Static only (no .htaccess) / "expires max"
* Delete the default `htdocs` app

Next, copy the `hosts.example` file and change the USERNAME to your Webfaction
account name. Also change the port to the port of your gunicorn app:

    cp hosts.example hosts
    vim hosts

Now execute the playbook: 

    ansible-playbook -i hosts site.yml
