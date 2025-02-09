## Prerequisites
### Domain
You have to get a domain for yourself.
ddclient is configured for using dynu.net for domain provider.
`ddclient_login` and `ddclient_password` have to be set in the vault file.
`domain_name` and `domain_email` have to be set in the `host_vars/google-cloud-freetier.yaml` host var file.

### Install required packages from the packages.txt
The command is for Arch Linux's Yay package manager. On other distros, you need the look up the packages you have to install.

### Create and add secrets to vault
```yaml
ansible-vault create secret-vars.vault
```

#### Secrets needed:
```yaml
gitea_username: 
gitea_password: 
ntfy_user_password: 
ddclient_login: 
ddclient_password: 
dynu_api_key:
```
You can also add your Ntfy user's passwords to your vault to prevent password leaks.

## Setting up variables
Fill in the empty variables in the files in `inventory`, `group_vars` and `host_vars` files.

### /etc/ansible/hosts
The hosts file will be updated by the change_ssh_port role with the public ip of the instance automatically. More info in this is below.

# Noteworthy roles
## change_ssh_port role
This role will change the default ssh port to the given port in your host vars. **It only supports the first group in your inventory currently.**
> Sometimes connection can fail after the port change happened because the ssh service doesn't pick up the port change. Just restart the vm by hand and re-run the playbook. This role takes actions on the first run anyway.

## Nginx role
This role configures Nginx as a reverse proxy to forward HTTP(S) requests to backend services. It relies on a separate Lego role for SSL certificate management, which handles the retrieval and renewal of wildcard certificates.

## Lego role
This Ansible role automates the process of obtaining and renewing SSL certificates from Let’s Encrypt using the `lego` client. The role will ensure that your certificates are renewed periodically via a `cron` job, and it will manage the process of copying the certificates to your Nginx configuration. The Nginx service is reloaded with zero downtime after every certificate renewal.

### Start with the following command
`ansible-playbook -e @secret-vars.vault main.yaml --ask-vault-pass`

