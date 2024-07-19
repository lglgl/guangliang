### 配置HOST
默认配置`inventory = /etc/ansible/hosts`
```shell
[local-node001]
192.168.139.11 hostname=local-node001 ansible_ssh_user=idszt ansible_ssh_pass='idszt' ansible_become_pass="idszt"
[local-node002]
192.168.139.12 hostname=local-node002 ansible_ssh_user=idszt ansible_ssh_pass='idszt' ansible_become_pass="idszt"
[local-node003]
192.168.139.13 hostname=local-node003 ansible_ssh_user=idszt ansible_ssh_pass='idszt' ansible_become_pass="idszt"
[local-node004]
192.168.139.14 hostname=local-node004 ansible_ssh_user=idszt ansible_ssh_pass='idszt' ansible_become_pass="idszt"
[local-node005]
192.168.139.15 hostname=local-node005 ansible_ssh_user=idszt ansible_ssh_pass='idszt' ansible_become_pass="idszt"
[local-node006]
192.168.139.16 hostname=local-node006 ansible_ssh_user=idszt ansible_ssh_pass='idszt' ansible_become_pass="idszt"
[local-node007]
192.168.139.17 hostname=local-node007 ansible_ssh_user=idszt ansible_ssh_pass='idszt' ansible_become_pass="idszt"
[local-node008]
192.168.139.18 hostname=local-node008 ansible_ssh_user=idszt ansible_ssh_pass='idszt' ansible_become_pass="idszt"
[localcluser:children]
local-node001
local-node002
local-node003
local-node004
local-node005
local-node006
local-node007
local-node008
```

### 主机命名
创建文件：`set_hostname.yaml`
```shell
- hosts: localcluser
  become: yes
  become_user: root
  become_method: sudo
  tasks:
  - name: set hostname
    shell: hostnamectl set-hostname {{hostname}}

# 执行
ansible-playbook set_hostname.yaml
[WARNING]: Invalid characters were found in group names but not replaced, use -vvvv to see details
PLAY [localcluser] *****************
TASK [Gathering Facts] *************
ok: [192.168.10.182]
ok: [192.168.10.183]
ok: [192.168.10.185]
TASK [set hostname] ****************
changed: [192.168.10.182]
changed: [192.168.10.183]
changed: [192.168.10.181]
PLAY RECAP *************************
192.168.10.181 : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
 


### 文件下发
