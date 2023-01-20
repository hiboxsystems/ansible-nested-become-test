# README

To test this locally (ensure you have `authorized_keys`-based SSH access to your local account):

```shell
$ ansible-playbook playbook.yml -vvv -i localhost,
```

### With Ansible 2.13.4

This is the expected behavior. The `become: true` statement in `roles/role2/meta/main.yml` is honored:

```
$ ansible-playbook playbook.yml -v -i localhost, --user plundberg
No config file found; using defaults

PLAY [Runs role1] ***********************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /usr/bin/python3.10, but future installation of another Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-
core/2.13/reference_appendices/interpreter_discovery.html for more information.
ok: [localhost]

TASK [include_role : role1] *************************************************************************************************************************************************************************************************************************************

TASK [role3 : whoami] *******************************************************************************************************************************************************************************************************************************************
changed: [localhost] => {"changed": true, "cmd": ["whoami"], "delta": "0:00:00.001482", "end": "2023-01-20 15:57:10.027900", "msg": "", "rc": 0, "start": "2023-01-20 15:57:10.026418", "stderr": "", "stderr_lines": [], "stdout": "root", "stdout_lines": ["root"]}

PLAY RECAP ******************************************************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### With Ansible 2.14.1

Here, the semantics have changed. The `become: true` statement in `roles/role2/meta/main.yml` is ignored, making `stdout_lines` display the current user name (`plundberg`) instead of the expected `root`:


```
$ ansible-playbook playbook.yml -v -i localhost, --user plundberg
No config file found; using defaults
PLAY [Runs role1] ***********************************************************************************************************************************************************************************************************************************************
TASK [Gathering Facts] ******************************************************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /usr/bin/python3.11, but future installation of another Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-
core/2.14/reference_appendices/interpreter_discovery.html for more information.
ok: [localhost]
TASK [include_role : role1] *************************************************************************************************************************************************************************************************************************************
TASK [role3 : whoami] *******************************************************************************************************************************************************************************************************************************************
changed: [localhost] => {"changed": true, "cmd": ["whoami"], "delta": "0:00:00.001548", "end": "2023-01-20 15:57:52.962146", "msg": "", "rc": 0, "start": "2023-01-20 15:57:52.960598", "stderr": "", "stderr_lines": [], "stdout": "plundberg", "stdout_lines": ["plundberg"]}
PLAY RECAP ******************************************************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
