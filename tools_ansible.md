# Ansible

### Install

Please refer to this link https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

```
# Currently using ansible version 2.16.3 & sshpass
sudo apt install ansible sshpass
```

### Workspace

Workspace tree

```
├── ansible.cfg
├── inventory
│   ├── README.md
│   ├── group_vars
│   │   ├── all
│   │   │   └── vars.yaml
│   │   ├── k3s_cluster
│   │   │   └── vars.yaml
│   │   └── utility
│   │       └── vars.yaml
│   └── inventory.yaml
├── playbook.yaml
├── playbooks
│   └── bootstrap.yaml
└── roles.yaml
```

**\[FOR NOW NOT USED\]** If we want to separate between environment, we can use different branch. And for `inventory and playbooks` directory, we can specify per environment at `.gitmodules` file. For example:

```
[submodule "playbooks"]
	path = playbooks
	url = git@gitlab.com:playground-server-project/devops/ansible/ansible-workspace/playbooks.git
	branch = dev
[submodule "inventory"]
	path = inventory
	url = git@gitlab.com:playground-server-project/devops/ansible/ansible-workspace/inventory.git
	branch = dev
```

### \[UNUSED\] PHPIpan Dynamic Inventory

Dynamic inventory copied from https://github.com/pooryasheikh/phpipam-inventory. We just need to install `python-dotenv` from `pip`. And then make the python script executable with `chmod +x`. After that we can test it with below command.

```bash
ansible-inventory --inventory=inventory/phpipam_inventory.py --list --yaml
```

### Inventory

### Ansible Vault
Create pass file, and then type command. Type value that want to encrypted. Once done press `ctrl+d` twice (if value doesn't have newline).

```bash
ansible-vault encrypt_string --vault-password-file pass
# Reading plaintext input from stdin. (ctrl-d to end input, twice if your content does not already have a newline)
# Very_S3cret_passw0rd    <-- Input goes here!
# Encryption successful
```

For running playbook with those vault values. We have to specify password. We can use same password file used by encrypting step.

```
ansible-playbook playbook.yaml --vault-password-file pass
```

### Roles
We're gonna using ansible galaxy but rather than publicly upload at galaxy website, we're gonna using private repository (git). To create a role, we can use command like `ansible-galaxy init sample-role` or create our own directory structure like this.

```bash
tree sample-role/
sample-role/
├── README.md
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
└── vars
    └── main.yml
```

When is done creating, we can push to our repository.

### Installing Roles
We can specifying roles on one yaml file like this https://gitlab.com/playground-server-project/devops/ansible/ansible-workspace/ansible-workspace/-/blob/master/roles.yaml?ref_type=heads.

Then install them with this command.

```
ansible-galaxy install -r roles.yml --force
```

### References
* https://opencredo.com/blogs/reusing-ansible-roles-with-private-git-repos-and-dependencies/
* \[Ansible Vault\] https://stackoverflow.com/a/44044094
* https://www.digitalocean.com/community/tutorials/how-to-use-vault-to-protect-sensitive-ansible-data
* \[Git submodules branch\] https://stackoverflow.com/a/18797720