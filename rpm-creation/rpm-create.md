# RPM Create

```text
yum install rpm-build rpmdevtools

//No need of manual create files & dir
#mkdir -p ~/rpmbuild/{BUILD,RPMS,SOURCES,SPECS,SRPMS}
#echo '%_topdir %(echo $HOME)/rpmbuild' > ~/.rpmmacros
rpmdev-setuptree

//extarct rpmbild tar
cd /root/rpmbuild/SPECS

rpmbuild -ba merlin-agent-vml.spec

systemctl enable ${PACKAGE_NAME}
systemctl daemon-reload
systemctl start ${PACKAGE_NAME}
rpm -Uivh --replacefiles --replacepkgs merlin-agent-vml-1.0.0-0.x86_64.rpm


rpm -e package_name [package_name…]

tar czf mydumpadmin-1.0.tar.gz mydumpadmin-1
```

