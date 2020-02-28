# Adding trusted root certificates to the server

1. Install the ca-certificates package: `yum install ca-certificates`
2. Enable the dynamic CA configuration feature: `update-ca-trust force-enable`
3. Add it as a new file to /etc/pki/ca-trust/source/anchors/: `cp foo.crt /etc/pki/ca-trust/source/anchors/`
4. Use command: `update-ca-trust extract`

