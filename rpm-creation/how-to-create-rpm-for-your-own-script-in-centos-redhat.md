# How to Create RPM for Your Own Script in CentOS/RedHat

Written by [Rahul](https://tecadmin.net/author/myadmin/), Updated on October 25, 2017 [Linux Tutorials](https://tecadmin.net/category/linux-tutorials/) [Create own RPM](https://tecadmin.net/tag/create-own-rpm/), [Create RPM](https://tecadmin.net/tag/create-rpm/), [Create RPM for my Script](https://tecadmin.net/tag/create-rpm-for-my-script/), [Create RPM in CentOS](https://tecadmin.net/tag/create-rpm-in-centos/), [Create RPM in Redhat](https://tecadmin.net/tag/create-rpm-in-redhat/), [Create RPM of your own script in CentOS](https://tecadmin.net/tag/create-rpm-of-your-own-script-in-centos/), [Create RPM of your own script in Redhat](https://tecadmin.net/tag/create-rpm-of-your-own-script-in-redhat/), [How can I create RPM](https://tecadmin.net/tag/how-can-i-create-rpm/), [How do i create rpm](https://tecadmin.net/tag/how-do-i-create-rpm/), [How to create RPM](https://tecadmin.net/tag/how-to-create-rpm/), [Steps to create RPM](https://tecadmin.net/tag/steps-to-create-rpm/) 

**RPM \(RedHat Package Manager\)** is a package management system for RHEL based systems. You may have seen that all the packages in Redhat based systems have extension **.rpm**. This tutorial will help you to how to create RPM for your own script

I had created a script to take database backup, Today I have created an RPM file of that script, This is my first RPM created ever. Below are the steps which I follow to do it.

### Step 1 – Install Required Packages

First of all, you need to install the required packages on your system to create rpm files.

```text
$ yum install rpm-build rpmdevtools
```

### Step 2 – Create Directory Structure

Go to users home directory, and create required directory structure using below command.

```text
$ rpmdev-setuptree
```

Above command will create a directory structure like below.

```text
$ ls -l rpmbuild

drwxr-xr-x 2 root root 6 Oct 25 03:09 BUILD
drwxr-xr-x 2 root root 6 Oct 25 03:09 RPMS
drwxr-xr-x 2 root root 6 Oct 25 03:09 SOURCES
drwxr-xr-x 2 root root 6 Oct 25 03:09 SPECS
drwxr-xr-x 2 root root 6 Oct 25 03:09 SRPMS
```

In case **rpmdev-setuptree** command failed to create structure, you can manually create it

```text
$ mkdir -p ~/rpmbuild/{BUILD,RPMS,SOURCES,SPECS,SRPMS,tmp}
```

### Step 3 – Create rpmmacro File

Now create ~/.rpmmacros file, with the following contents. Change the values of red highlighted names

**~/.rpmmacros**

```text
%packager YOUR_NAME

%_topdir %(echo $HOME)/rpmbuild

%_smp_mflags %( \
    [ -z "$RPM_BUILD_NCPUS" ] \\\
        && RPM_BUILD_NCPUS="`/usr/bin/nproc 2>/dev/null || \\\
                             /usr/bin/getconf _NPROCESSORS_ONLN`"; \\\
    if [ "$RPM_BUILD_NCPUS" -gt 16 ]; then \\\
        echo "-j16"; \\\
    elif [ "$RPM_BUILD_NCPUS" -gt 3 ]; then \\\
        echo "-j$RPM_BUILD_NCPUS"; \\\
    else \\\
        echo "-j3"; \\\
    fi )

%__arch_install_post \
    [ "%{buildarch}" = "noarch" ] || QA_CHECK_RPATHS=1 ; \
    case "${QA_CHECK_RPATHS:-}" in [1yY]*) /usr/lib/rpm/check-rpaths ;; esac \
    /usr/lib/rpm/check-buildroot

```

### Step 4 – Copy Files under SOURCES Directory

Copy all your files and scripts folder inside **~/rpmbuild/SOURCES** directory, which we need to add in rpm file. For this tutorial, I have used all the files created in our tutorial [Advance Bash Script for MySQL](https://tecadmin.net/advance-bash-script-for-mysql-database-backup/).

```text
$ cd ~/rpmbuild/SOURCES
$ ls -l mydumpadmin-1

-rw-r--r-- 1 root root  125 Oct 25 03:54 credentials.txt
-rw-r--r-- 1 root root 4693 Oct 25 03:54 mysql-dump.sh
-rw-r--r-- 1 root root 1008 Oct 25 03:54 README.md
-rw-r--r-- 1 root root 3445 Oct 25 03:54 settings.conf
```

Create a tarball of your code.

```text
$ tar czf mydumpadmin-1.0.tar.gz mydumpadmin-1
```

### Step 5 – Create SPEC File

Create a spec file **~/rpmbuild/SPECS/mydumpadmin.spec** using below content.

**~/rpmbuild/SPECS/mydumpadmin.spec**

```text
Name:           mydumpadmin
Version:        1
Release:        0
Summary:        An Advance Bash Script for MySQL Database Backup

Group:          TecAdmin
BuildArch:      noarch
License:        GPL
URL:            https://github.com/tecrahul/mydumpadmin.git
Source0:        mydumpadmin-1.0.tar.gz

%description
Write some description about your package here

%prep
%setup -q
%build
%install
install -m 0755 -d $RPM_BUILD_ROOT/etc/mydumpadmin
install -m 0600 credentials.txt $RPM_BUILD_ROOT/etc/mydumpadmin/credentials.txt
install -m 0755 mysql-dump.sh $RPM_BUILD_ROOT/etc/mydumpadmin/mysql-dump.sh
install -m 0644 README.md $RPM_BUILD_ROOT/etc/mydumpadmin/README.md
install -m 0644 settings.conf $RPM_BUILD_ROOT/etc/mydumpadmin/settings.conf

%files
/etc/mydumpadmin
/etc/mydumpadmin/credentials.txt
/etc/mydumpadmin/mysql-dump.sh
/etc/mydumpadmin/README.md
/etc/mydumpadmin/settings.conf

%changelog
* Tue Oct 24 2017 Rahul Kumar  1.0.0
  - Initial rpm release
```

Change package name, script path, archive name, description etc, as per your requirement.

### Step 6 – Build RPM

After completing above steps, lets build your rpm by executing following command.

```text
$ cd ~/rpmbuild
$ rpmbuild -ba SPECS/mydumpadmin.spec
```

After successful built, a rpm file will created like **~/rpmbuild/RPMS/noarch/mydumpadmin-1-0.noarch.rpm**

Install your rpm using the below command. After install check, the files are properly installed as defined location.

```text
$ rpm -ivh mydumpadmin-1-0.noarch.rpm
```

Thank You for using this article. I hope above steps will help you to create your own rpm.

