# reproduce-ansible-issue-72719
https://github.com/ansible/ansible/issues/72719

# Steps to reproduce

1. `cd` to root of this repo

2. `cp .envrc.tpl .envrc`

3. Edit `.envrc` to include appropriate `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_DEFAULT_REGION`

4. `source .envrc`

5. `cd packer`

6. `ansible-galaxy install -r ../ansible/requirements.yml`

7. `packer build -debug ec2-base.json`

8. Keep hitting Enter until after `Pausing before the next provisioner . Press enter to continue.` (Ansible will start provisioning)

9. In a seperate shell (same directory), `ssh -i ec2_amazon-ebs.pem ubuntu@<instance-public-ip>` getting `<instance-public-ip>` from the EC2 console

10. `ps aux | grep '/bin/sh -c /usr/bin/python3 && sleep 0'` to find the process causing the hanging

11. End process by `kill -s HUP <pid>` where `<pid>` is from the previous step

12. Note that process in the initial shell now continues successfully despite warnings of the form (keep hitting Enter at prompts):

```
    amazon-ebs: TASK [Gathering Facts] *********************************************************
    amazon-ebs: [WARNING]: Unhandled error in Python interpreter discovery for host default:
    amazon-ebs: Expecting value: line 1 column 1 (char 0)
    amazon-ebs: ok: [default]
    amazon-ebs: [WARNING]: Platform linux on host default is using the discovered Python
    amazon-ebs: interpreter at /usr/bin/python3, but future installation of another Python
    amazon-ebs: interpreter could change this. See https://docs.ansible.com/ansible/2.9/referen
    amazon-ebs: ce_appendices/interpreter_discovery.html for more information.
```
