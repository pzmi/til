# Run cloud-init user data provisioning in a lxc/lxd container

```bash
lxc launch ubuntu:bionic test-container --config=user.user-data="$(cat user-data)"
```
