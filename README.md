# minio-deploy

An Ansible playbook to install, configure, and deploy a Minio instance.

Uses [ansible-role-minio](https://galaxy.ansible.com/ricsanfre/minio) by [ricsanfre](https://github.com/ricsanfre/) as the Ansible Role that will do a lot of the heavy lifting for us!

## Usage

To install the required role and run the playbook run -- 

```sh
ansible-galaxy install ricsanfre.minio
ansible-playbook minio-deploy.yaml
```

## Initial Instillation

This playbook aims to replicate the steps used in the initial ACED-IDP Minio deployment at OHSU (based on CentOS).

```sh
sudo yum -y install wget
wget https://dl.min.io/server/minio/release/linux-amd64/archive/minio-20230210184839.0.0.x86_64.rpm -O minio.rpm

sudo yum -y install minio.rpm
test -f  /etc/systemd/system/minio.service && echo "minio.service config exists, installed bu minio.rpm"

sudo groupadd -r minio-user
sudo useradd -M -r -g minio-user minio-user

# where /dev/vdb is the openstack volume, formatted with fdisk and mkfs.xfs /dev/vdb
sudo chown minio-user:minio-user /dev/vdb
sudo fdisk -l

test -f  /etc/default/minio || echo "creating /etc/default/minio"
```

## References

- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html
    -  The official Ansible Playbook documentation.

- https://github.com/atosatto/ansible-minio:
    - An alternative Ansible Role -- popular (100 GH stars), but somewhat outdated Ubuntu versions.
