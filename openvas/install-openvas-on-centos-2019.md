# Install OpenVAS on CentOS \(2019\)

A few prerequisites:

1. Disable SELinux.
2. Permit TCP port 9392, 443 and 80.

Run this command. It’s downloading a script and executing it:

```text
wget -q -O - https://updates.atomicorp.com/installers/atomic | sudo sh
```

Clear YUM:

```text
yum clean all
```

Install OpenVAS from YUM:

```text
yum install openvas atomic-sqlite-sqlite sshpass
```

Run the setup process and follow the prompts. This takes a few hours due to all the downloads it needs:

```text
openvas-setup
```

You can set a new Admin password at any time later for the web portal:

```text
openvasmd --user=admin --new-password=MySecretPassword
```

Set the NVT signature checking in “/etc/openvas/openvassd.conf”. The following is set to ‘yes’ by default. Change it to no:

```text
nasl_no_signature_check = no
```

Restart the services:

```text
systemctl enable redis
systemctl enable gsad
systemctl enable gvmd 
systemctl enable openvas-manager 
systemctl enable openvas-scanner

systemctl restart redis
systemctl restart gsad
systemctl restart gvmd
systemctl restart openvas-manager
systemctl restart openvas-scanner
```

Open your web browser and navigate to the following address and login with the username and password you’ve previously set:

```text
"https://localhost:9392" or "https://localhost"
```

## Further random notes

The following are my notes and attempts to resolve minor issues.

### Schedule updates

It’s important to keep OpenVAS up to date. Schedule the following command\(s\):

```text
greenbone-nvt-sync ; openvasmd --rebuild
```

