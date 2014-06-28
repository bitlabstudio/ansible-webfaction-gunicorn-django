DO NOT USE THIS! WORK IN PROGRESS

This is an Ansible playbook that sets up a new Webfaction server.

Before executing the playbook, copy the `hosts.example` file and change the
USERNAME to your Webfaction account name:

    cp hosts.example hosts
    vim hosts

Execute the playbook like so: 

    ansible-playbook -i hosts site.yml
