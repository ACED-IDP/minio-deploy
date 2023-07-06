# minio-deploy

An Ansible playbook to install, configure, and deploy a Minio instance.

Uses [ansible-minio](https://github.com/atosatto/ansible-minio) as the Ansible Role that will do a lot of the heavy lifting for us!

## Usage

Copy and edit the inventory file to fit your server and credentials: 

```sh
cp inventory-example inventory
vi inventory
```

Then run the following to install the required role and run the playbook -- 

```sh
ansible-galaxy install atosatto.minio
ansible-playbook minio-deploy.yaml
ansible-playbook minio-deploy.yaml -i inventory --ask-pass --ask-become
```

A given run is expected to take ~10 minutes to complete.

To change the values provided to MinIO simply edit the inventory file and rerun the playbook command.

## Requirements

```sh
python3 -m venv venv
source ./venv/bin/activate
pip install ansible
```
## Next Steps

### 1. Nginx installation

Nginx can now be installed to provide a web server for MinIO.

```sh
# Ubuntu running SystemD example
apt-get update
apt-get install nginx
systemctl start nginx
systemctl enable nginx

cp minio.conf /etc/nginx/conf.d/minio.conf
systemctl reload nginx
```

### 2. Credentials & User Management

[MinIO Reference](https://min.io/docs/minio/linux/administration/identity-access-management/minio-user-management.html#create-a-user)

Set MinIO admin credentials in `/etc/default/minio`:

```
MINIO_ROOT_USER=<admin_username>
MINIO_ROOT_PASSWORD=<admin_password>
```

Create initial user with `readwrite` permissions via the `mc` command or through the web console:

![MinIO user creation](./minio-user.png)

### 3. Data Upload & Download

It should now be possible to access the MinIO endpoint using the newly created user for data upload and download tasks.

## Manual Minio Installation

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

- https://github.com/ricsanfre/ansible-role-minio
    - An alternative Ansible Role with a self signed TLS step included

- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html
    -  The official Ansible Playbook documentation.
