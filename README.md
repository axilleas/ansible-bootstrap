# ansible-bootstrap

Some basic stuff to bootstrap a standalone ansible repo.

## Prerequisites

1. [Ansible][]
1. A configured gpg key to use with ansible-vault

## File list

1. `bin/{ansible-test,open_the_vault.sh}`
1. `ansible.cfg`
1. `secrets.yml`
1. `deploy.yml`
1. `vault-passwd.gpg` (not in the ansible-bootstrap repo, read below)

## Instructions

### Vault password

Using the method described in [Eric Call's blog post][vault-gpg], generate a
strong password to use with `[ansible-vault][]` in order to encrypt
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

[Ansible]: https://ansible.com/
[ansible-vault]: https://docs.ansible.com/ansible/playbooks_vault.html
[vault-gpg]: https://blog.erincall.com/p/using-pgp-to-encrypt-the-ansible-vault
