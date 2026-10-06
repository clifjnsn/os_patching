# Ansible Stable Execution Environment
*This readme is a WIP (work in progress)*   
OH-Ansible is a project to give us a common/stable/predictable location to launch our ansible code on the command line.

## How to get it:
1. Download the repo
> git clone git@ltv-gitlab-ap01.orhs.org:ohlinux/oh-ansible.git
2. cd into the oh-ansible directory
> cd oh-ansible
3. cp the ansible.cfg.sample file to ansible.cfg
> cp ansible.cfg.sample ansible.cfg
(You may need to edit this file to insure it matches your setup - especially the sections which refer to directory/file paths)

## Directory Structure:
~~~
oh-ansible/
├── ansible.cfg.sample  ← example ansible.cfg to set up a stable common environment for all playbooks
├── ansible-cache  ← Directory to hold collected Ansible "facts" about each host - used by ansible-cmdb reporting tool
├── archived  ← Directory which contains playbooks which are only for reference
├── group_vars/  ← put files in here named after groups from the inventory to specify variables that will apply to all members of that group.
├── group_vars/all/ohvault.yml  ← Encrypted ansible-vault file, containing account passwords, which are referenced in the hosts file.
├── host_vars/  ← put files in here named after specific hosts from the inventory to specify variables that will apply only to that host.  Variables here override group_vars
├── playbooks/   ← put your playbooks here. (NOTE: AWX cannot find roles loaded from playbooks located here.  If a playbook contains a role, place it at the root folder)
├── roles/  ← all roles.  anything bigger than a playbook.  requirements.yml will import roles from other places.  .gitignore will keep them from getting added to our repo.
│   ├── patching   ← RedHat and Ubuntu automated system patching
│   ├── oneagent   ← Dynatrace agent installation/removal
└── site.yml ← should launch all the idempotent playbooks on the site, can use tags or limits to limit what is done.
~~~

## To Use the encrypted vault file for an Ansible Ad-hoc, or playbook, run
> ansible --ask-vault-password -m ping all

> ansible-playbook --ask-vault-password cli_gather.yml

## To stop being prompted for the vault password
1. Create a "secrets" file in oh-ansible/
> touch oh-ansible/.secret.txt

> chmod 600 oh-ansible/.secret.txt
2. Edit the file and place the KeePass password into the file (in plain text)
3. Run ansible/ansible-playbook commands using this file instead of being prompted for a password:
> ansible --vault-password-file .secret.txt -m ping all

> ansible-playbook --vault-password-file .secret.txt cli_gather.yml
