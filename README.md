# ansible-aws
ansible aws provisioning PoC with multiple deployment environments

The intention is to create a centralised but also well seggregated infrastructure so we can easily divide between different deployment environments such as staging and production.

inventory files: production and staging have different inventories. New span ec2 instances: are added to the specific environment's inventory file from within the playbook - deleted ec2 instances: are/should be deleted from the environment's inventory file also dynamically.

The ec2 dynamic inventory script lives under the playbook directory, provisioning environment, in this case
```
/etc/ansible/provisioning/environments/{staging,production}/AWS/ec2/playbooks/dynamic_inventory/
```

Roles live all under /etc/ansible/roles and are divided given the functionality they provide, e.g: create ec2 instance (witihn the aws namespace), install and configure apache, install and configure mysql (for stacks like vpc + subnet + gateway Cloudfront module may be better for this). Roles don't have the same deployment/provisioning enviroment seggregation. This is as roles are seggregated by cloud providers (AKA namespaces). This structure aims to be able to house different cloud providers.

We could add different cloud providers, data centers etc. under provisioning/environments/{{environment}} and the playbook/s will live as in the AWS use case.

If you are going to delete an instance, you better use the playbook or role for it but dynamically deleting from the inventory file, as this architecture adds hosts from the inventory dynamically from within the playbook.

An example of playbook execution to provision instances in ec2 (cd first into the playbooks dir):
```bash
# ansible-playbook --private-key ~/.ssh/tricky-key-pair-ireland.pem -e "env=staging server_type=dbserver count=1" -vvvv create_instance.yml
```

This repository just serves as a demo/poc of how you can organise your AWS's (and other cloud providers) Ansible code, avoiding decentralisation and providing a fair structure to let you scale your environments. Appart from that, the create_instance.yml playbook is fully functional :)

UPDATE: the idea now is to have provisioning and deployment separated. Playbooks for deployment (newly span instances) will take the variables from ec2_vars/server.yml, being ec2_vars in the playbook directory (see directory tree). This allow for a seggragated structure where we would place up to date variables in ec2_vars depending on the server type (dbserver,proxyserver,etc.). These vars withe the extra vars we pass on playbook-execution time, will be taken by the role ec2_create.yml. The intention is to provide similar functionality to Hiera with Puppet.

The provisioning has now its own directory tree, which would help us logically devide both, deployment and provision. When provisioning, as the deployed instances are added dynamically to the inventory files, we'd use host_vars and group_vars, not the ec2_vars. We may want to symlink these dirs, but that's sometihng I've not tried with Git (as we would have all this on Git) but I reckon it's completely possible.

If you want to run the playbook to create ec2 instances make sure you edit the variables within files in ec2_vars.





