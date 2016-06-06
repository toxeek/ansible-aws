# ansible-aws
ansible aws provisioning PoC with multiple deployment environments

The intention is to create a centralised but also well seggregated infrastructure so we can easily divide between different deployment environments such as staging and production.

inventory files: production and staging have different inventories
new spun ec2 instances: are added to the specific environment's inventory file from within the playbook
deleted ec2 instances: are deleted from the environment's inventory file

The ec2 dynamic inventory script lives under the playbook directory, in this case
```
/etc/ansible/provisioning/environments/{staging,production}/AWS/ec2/playbooks
```

Roles live all under /etc/ansible/roles and are divided given the functionality they provide, e.g: install and configure apache, install and configure mysql (for stacks like vpc + subnet + gateway Cloudfront module may be better for this).

We could add different cloud providers, data centers etc. under provisioning/environments/{{environment}} and the playbook/s will live as in the AWS use case.

If you are going to delete an instance, you better use the playbook or role for it but dynamically deleting from the inventory file, as this architecture adds hosts from the inventory dynamically from within the playbook.

An example of playbook execution to provision instances in ec2 (cd first into the playbooks dir):
```bash
# ansible-playbook --private-key ~/.ssh/tricky-key-pair-ireland.pem -e "env=staging" -vvvv create_instance.yml

If you want to provision using roles in the playbook, then there is a demo role under the roles/ directory taken from http://allandenot.com/devops/2015/01/31/provisioning-ec2-hosts-with-ansible.html

This repository just serves as a demo/poc of how you can organise your AWS's (and other cloud providers) Ansible code, avoiding decentralisation and providing a fair structure to let you scale your environments. Appart from that, the create_instance.yml playbook is fully functional :)





