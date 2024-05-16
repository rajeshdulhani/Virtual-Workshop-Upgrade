## Chapter 7. Performing post-upgrade tasks

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index#performing-post-upgrade-tasks-rhel-7-to-rhel-8_upgrading-from-rhel-7-to-rhel-8


- Remove any remaining Leapp packages from the exclude list in the /etc/dnf/dnf.conf configuration file, including the snactor package. During the in-place upgrade, Leapp packages that were installed with the Leapp utility are automatically added to the exclude list to prevent critical files from being removed or updated. After the in-place upgrade, you must remove these Leapp packages from the exclude list before they can be removed from the system.

    -  To manually remove packages from the exclude list, edit the /etc/dnf/dnf.conf configuration file and remove the desired Leapp packages from the exclude list.

    -  To remove all packages from the exclude list:


    ~~~
    # yum config-manager --save --setopt exclude=''
    ~~~



## Determine old kernel versions: 
~~~
cd /lib/modules && ls -d *.el7*
~~~

## Remove weak modules from the old kernel. If you have multiple old kernels, repeat the following step for each kernel: 
~~~
[ -x /usr/sbin/weak-modules ] && /usr/sbin/weak-modules --remove-kernel 3.10.0-1160.108.1.el7.x86_64
[ -x /usr/sbin/weak-modules ] && /usr/sbin/weak-modules --remove-kernel 3.10.0-1160.114.2.el7.x86_64
[ -x /usr/sbin/weak-modules ] && /usr/sbin/weak-modules --remove-kernel 3.10.0-1160.118.1.el7.x86_64
~~~

## Remove the old kernel from the boot loader entry. If you have multiple old kernels, repeat this step for each kernel: 
~~~
/bin/kernel-install remove 3.10.0-1160.108.1.el7.x86_64 /lib/modules/3.10.0-1160.108.1.el7.x86_64/vmlinuz
/bin/kernel-install remove 3.10.0-1160.114.2.el7.x86_64 /lib/modules/3.10.0-1160.114.2.el7.x86_64/vmlinuz
/bin/kernel-install remove 3.10.0-1160.118.1.el7.x86_64 /lib/modules/3.10.0-1160.118.1.el7.x86_64/vmlinuz
~~~
## Locate remaining RHEL 7 packages
~~~
rpm -qa | grep -e '\.el[67]' | grep -vE '^(gpg-pubkey|libmodulemd|katello-ca-consumer)' | sort
gd3php-2.3.3-7.el7.remi.x86_64
kernel-3.10.0-1160.114.2.el7.x86_64
kernel-3.10.0-1160.118.1.el7.x86_64
leapp-0.16.0-1.el7_9.noarch
leapp-upgrade-el7toel8-0.19.0-1.el7_9.noarch
libmcrypt-2.5.8-13.el7.x86_64
libraqm-0.7.0-4.el7.x86_64
libwebp7-1.0.3-2.el7.remi.x86_64
python2-leapp-0.16.0-1.el7_9.noarch
remi-release-7.9-6.el7.remi.noarch
ustr-1.0.4-16.el7.x86_64
~~~

## Remove remaining RHEL 7 packages, including old kernel packages, and the kernel-workaround package from your RHEL 8 system. 
~~~
yum erase  kernel-3.10.0-1160.114.2.el7.x86_64 kernel-3.10.0-1160.118.1.el7.x86_64 remi-release-7.9-6.el7.remi.noarch
~~~

## Remove remaining Leapp dependency packages:
~~~
yum remove leapp-deps-el8 leapp-repository-deps-el8
~~~

## Check leftover el7 packages
~~~
rpm -qa | grep -e '\.el[67]' | grep -vE '^(gpg-pubkey|libmodulemd|katello-ca-consumer)' | sort
gd3php-2.3.3-7.el7.remi.x86_64
libmcrypt-2.5.8-13.el7.x86_64
libraqm-0.7.0-4.el7.x86_64
libwebp7-1.0.3-2.el7.remi.x86_64
remi-release-7.9-6.el7.remi.noarch
ustr-1.0.4-16.el7.x86_64
~~~

## Check package browser for repository name 
~~~
https://access.redhat.com/downloads/content/package-browser
~~~
- For ustr : https://access.redhat.com/downloads/content/ustr/1.0.4-26.el8/x86_64/fd431d51/package

## Enable codeready-builder-for-rhel-8-x86_64-rpms repository
~~~
 subscription-manager repos --enable=codeready-builder-for-rhel-8-x86_64-rpms
~~~
~~~
yum update ustr
~~~


## Install EPEL8 repo ##
~~~
dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
~~~

## Install REMI8 repo
~~~
dnf install https://rpms.remirepo.net/enterprise/remi-release-8.rpm
~~~

## Install php-fpm
~~~
yum install php-fpm
~~~

## Find RHEL7 packages
~~~

~~~

## Replace RHEL7 packages 
~~~

~~~
