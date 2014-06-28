DO NOT USE THIS! WORK IN PROGRESS

This is an Ansible playbook that sets up a new Webfaction server.

Before executing the playbook, copy the `host.sample` file and change the
USERNAME to your Webfaction account name:

    cp host.sample host
    vim host

Execute the playbook like so: 

    ansible-playbook -i host webfaction.yml
