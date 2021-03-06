<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Prerequisites](#prerequisites)
- [File list](#file-list)
- [Instructions](#instructions)
  - [Vault password](#vault-password)
  - [secrets.yml](#secretsyml)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Some basic stuff to bootstrap a standalone ansible repo.

## Prerequisites

1. [Ansible][]
1. At least one gpg key to use with ansible-vault

## File list

1. `bin/{ansible-test,open_the_vault.sh}`
1. `ansible.cfg`
1. `secrets.yml`
1. `deploy.yml`
1. `vault-passwd.gpg` (not in the ansible-bootstrap repo, read below)

## Instructions

### Vault password

Using the method described in [Eric Call's blog post][vault-gpg], generate a
strong password to use with [ansible-vault][] in order to encrypt
`secrets.yml` and everything else needed. This will be stored in a gpg
encrypted file:

```
pwgen -sy 64 | head -n42 | gpg -e -o vault-passwd.gpg
```

The above command will ask you which IDs to use with the encryption. That way
you can add multiple collaborators. Enter all the e-mail addresses you want
and finalize the encryption with a blank entry.

Now every time you run `ansible-playbook`, ansible will look in `ansible.cfg`,
run the script in `/bin/open_the_vault.sh` and feed the passphrase to
`ansible-vault`.

Finally, add `vault-passwd.gpg` in git control.

**Note:** [`open_the_vault.sh`](/bin/open_the_vault.sh) needs to be
    executable.

### secrets.yml

Place here any role variables. A convention to know when a variable is secret,
is to define it in uppercase. For example:

```yaml
MARIADB_DB_PASSWD: "OzO=Qeg*IJQ"
```

Then in `roles/mariadb/vars/main.yml` define the database password like:

```yaml
db_passwd: "{{ MARIADB_DB_PASSWD }}"
```

which then can be called in your tasks.

The `secrets.yml` is always loaded in the general playbook `deploy.yml`.

Finally, encrypt `secrets.yml` with `ansible-vault`:

```
ansible-vault encrypt secrets.yml
```

which will encrypt the file with the password defined in the previous section.
When prompted, enter your gpg password.

[Ansible]: https://ansible.com/
[ansible-vault]: https://docs.ansible.com/ansible/playbooks_vault.html
[vault-gpg]: https://blog.erincall.com/p/using-pgp-to-encrypt-the-ansible-vault
