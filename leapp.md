## What is Leapp ?

LEAPP is a CLI tool that helps users with the porting and installation process for Red Hat Enterprise Linux, making your in-place upgrade easier.

## Why Leapp?

With Leapp you can upgrade with confidence and benefit from the new features of Red Hat® Enterprise Linux® without the need to reinstall your systems. Performance improvements in Red Hat Enterprise Linux lower your total cost of ownership, influence productivity, and maximize your technology investment. Read this detail about Leapp, the tool used to perform in-place system upgrades from one major version of Red Hat® Enterprise Linux® to another.

## Leapp Upgrade Process Phases

![leapp upgrade process](Screenshot%20from%202024-04-28%2013-05-43.png)

## How to perform Leapp Upgrade ?

1] Register & subscribe the system using subscription-manager to customer portal , also verify the status.

~~~
# subscription-manager register --username <username> --password <password> --auto-attach
# subscription-manager status
~~~

2] Enable the base and extras repositories.

~~~
# subscription-manager repos --enable rhel-7-server-rpms
# subscription-manager repos --enable rhel-7-server-extras-rpms 
~~~

3] Update the RHEL 7.9 system to the latest version of the packages and boot the system with the latest version.

~~~
# yum update
# reboot
~~~

4] Install the leapp packages.

~~~
 yum install leapp-upgrade
~~~

5] On your RHEL 7 system, perform the pre-upgrade phase which will generate the `/var/log/leapp/leapp-report.txt` file

~~~
leapp preupgrade --target <target_os_version>
~~~

6] Reviewing the report in the `/var/log/leapp/leapp-report.txt` file and manually resolve all the reported problems. Some reported problems contain remediation suggestions. Inhibitor problems prevent you from upgrading until you have resolved them.

The report contains the following risk factor levels:

  **High**: Very likely to result in a deteriorated system state.

  **Medium**: Can impact both the system and applications.

  **Low**: Should not impact the system but can have an impact on applications.

  **Info**: Informational with no expected impact to the system or applications.

7] In certain system configurations, the Leapp utility generates true or false questions that you must answer manually. If the pre-upgrade report contains a Missing required answers in the answer file message, complete the following steps:

  a] Open the `/var/log/leapp/answerfile` file and review the true or false questions.
  
  b] Manually edit the `/var/log/leapp/answerfile` file, uncomment the confirm line of the file by deleting the # symbol, and confirm your answer as True or False
  
~~~
 leapp answer --section <question_section>.<field_name>=<answer>
~~~

8] On your RHEL 7 system, start the upgrade process: 

~~~
# leapp upgrade --target <target_os_version>
# reboot
~~~

9] Perform the post upgrade tasks post successfull completion of leapp upgrade phase

a] Verify that the current OS version is Red Hat Enterprise Linux 8:

~~~
# (OVERRIDABLE) IS_LOADED_KERNEL_LATEST::INVALID_KERNEL_VERSION - Invalid kernel version detected
cat /etc/redhat-release
# uname -r
~~~

b] Verify that the correct product is installed:

~~~
# subscription-manager list --installed
# subscription-manager release
~~~

c] To remove all packages from the exclude list:

~~~
  yum config-manager --save --setopt exclude=''
~~~

d] Remove remaining RHEL 7 packages, including remaining Leapp packages.

 - Determine old kernel versions:

 ~~~
  cd /lib/modules && ls -d *.el7*
 ~~~

 - Remove weak modules from the old kernel. If you have multiple old kernels, repeat the following step for each kernel:

~~~
 [ -x /usr/sbin/weak-modules ] && /usr/sbin/weak-modules --remove-kernel <version>
~~~

- Remove the old kernel from the boot loader entry. If you have multiple old kernels, repeat this step for each kernel:

~~~
 /bin/kernel-install remove <version> /lib/modules/<version>/vmlinuz
~~~

- Locate remaining RHEL 7 packages:

~~~
 rpm -qa | grep -e '\.el[67]' | grep -vE '^(gpg-pubkey|libmodulemd|katello-ca-consumer)' | sort
~~~

- Remove remaining RHEL 7 packages, including old kernel packages, and the kernel-workaround package from your RHEL 8 system.

~~~
 yum remove $(rpm -qa | grep -e '\.el[67]' | grep -vE '^(gpg-pubkey|libmodulemd|katello-ca-consumer)' | sort)
~~~

- Remove remaining Leapp dependency packages:
  
~~~
 yum remove leapp-deps-el8 leapp-repository-deps-el8
~~~

- Remove any remaining empty directories:

~~~
 rm -r /lib/modules/*el7*
~~~

9] Verification Steps:

- Verify that the old kernels have been removed from the bootloader entry:


~~~
 grubby --info=ALL | grep "\.el7" || echo "Old kernels are not present in the bootloader."
~~~

- Verify that the previously removed rescue kernel and rescue initial RAM disk files have been created for the current kernel:

~~~
 ls /boot/vmlinuz-*rescue* /boot/initramfs-*rescue*
 lsinitrd /boot/initramfs-*rescue*.img | grep -qm1 "$(uname -r)/kernel/" && echo "OK" || echo "FAIL"
~~~

- Verify the rescue boot entry refers to the existing rescue files. See the grubby output:

~~~
 grubby --info $(ls /boot/vmlinuz-*rescue*)
~~~



## References:

[Supported in-place upgrade paths for Red Hat Enterprise Linux ](https://access.redhat.com/articles/4263361)

[The best practices and recommendations for performing RHEL Upgrade using Leapp](https://access.redhat.com/articles/7012979)

[Upgrading from RHEL 7 to RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)

[Upgrading from RHEL 8 to RHEL 9](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/upgrading_from_rhel_8_to_rhel_9/index)

[Interactive Lab for in-place  upgrade form RHEL 7 to RHEL8 ](https://www.redhat.com/en/interactive-labs/perform-in-place-upgrade-with-leapp)

[Workshop Exercise - Perform Recommended Remediation](https://aap2.demoredhat.com/exercises/ansible_ripu/1.4-remediate/#workshop-exercise---perform-recommended-remediation)
