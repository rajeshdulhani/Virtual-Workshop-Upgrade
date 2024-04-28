
# Convert2rhel

#### What is `convert2rhel`
`Convert2RHEL` is an officially supported component of RHEL that enables the conversion of select RHEL derivative distributions into a supportable RHEL state, retaining existing applications and configurations.

#### WHY:
Organizations that use CentOS Linux 7, a freely available, community-supported Linux distribution that is based on Red Hat Enterprise Linux 7, face a crucial choice. On **June 30, 2024**, support by the community for CentOS Linux 7 ends. After that date, CentOS Linux 7 users must migrate to a supported operating system to continue receiving security updates.

#### HOW:

Red Hat provides `Convert2RHEL`, a self-service migration tool for users of selected Linux distributions, including CentOS Linux. This tool can help you to avoid costly redeployment projects, identify potential issues before migration, and maintain your existing configurations, customizations, preferences, and applications.

#### What operating systems are supported for converting to RHEL?

<table>
<thead>
<tr>
<th>Source OS</th>
<th>Target OS</th>
<th>Architecture</th> 
</tr>
<tr>
<th>CentOS Linux 8</th>
<th>RHEL 8</th>
<th>64-bit Intel</th> 
</tr>
<tr>
<th>CentOS Linux 7</th>
<th>RHEL 7</th>
<th>64-bit Intel</th> 
</tr>
<tr>
<th>Oracle Linux 8</th>
<th>RHEL 8</th>
<th>64-bit Intel</th> 
</tr>
<tr>
<th>Oracle Linux 7</th>
<th>RHEL 7</th>
<th>64-bit Intel</th> 
</tr>
<tr>
<th>AlmaLinux OS 8</th>
<th>RHEL 8</th>
<th>64-bit Intel</th> 
</tr>
<tr>
<th>Rocky 8</th>
<th>RHEL 8</th>
<th>64-bit Intel</th> 
</tr>  
</thead>
</table>

#### You should consider the following before converting your system to RHEL

- **Architecture** - The source OS must be installed on a system with 64-bit Intel architecture. It is not possible to convert with other system architectures. 				
- **Security** - Systems in FIPS mode are not supported for conversion. 				
- **Kernel** - Systems using kernel modules that do not exist in RHEL kernel modules are not currently supported for conversion. Red Hat recommends disabling or uninstalling foreign kernel modules before the conversion and then enabling or reinstalling those kernel modules afterwards. Unsupported kernel modules include: 				
    -  Kernel modules for specialized applications, GPUs, network drivers, or storage drivers 	
    -  Custom compiled kernel modules built by DKMS 		
- **Public clouds** - Conversions on public clouds are supported in the following situations: 				
  - Alma Linux, CentOS Linux, and Rocky Linux - Using Red Hat Subscription Manager (RHSM) for the following: 						
      - Images on Amazon Web Services (AWS), Microsoft Azure, and Google Cloud with no associated software cost. 								
      - User-provided custom images on all public clouds 								
  - Oracle Linux - Using RHSM for user-provided custom images on all public clouds. 						
 `Convert2RHEL` is unable to access RHEL packages through Red Hat Update Infrastructure (RHUI) during the conversion of both CentOS Linux and Oracle Linux. 						
- **High Availability** - Systems using high availability cluster software by Red Hat or third parties are not currently tested or supported for conversion to RHEL. Red Hat recommends migrating to newly installed RHEL systems to ensure the integrity of these environments. 				
- **Identity Management** - Performing an in-place conversion of a FreeIPA server is not supported. For more information about how to migrate a FreeIPA deployment to IdM, see Migrating to IdM on RHEL 7 from FreeIPA on non-RHEL Linux distributions and Migrating to IdM on RHEL 8 from FreeIPA on non-RHEL Linux distributions. 				
- **Foreman** - Conversions of systems that use Foreman with the Katello plugin are not supported. To perform a supported conversion, migrate to Red Hat Satellite first and then proceed with the conversion. 		

## Step 1: Validate CentOS

To check whether you have a valid CentOS release version, enter the following:
```
#cat  /etc/centos-release
```
The following output shows that this is a valid version:
```
CentOS Linux release 7.9.2009 (Core)
```
## Step 2: Update your system
~~~
#yum  update  -y
~~~

## Step 3: Secure the packages
Download the signing keys to ensure that we are pulling in only Red Hat created and vetted packages .
~~~
#curl -o /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release https://www.redhat.com/security/data/fd431d51.txt
~~~
#Copy the SSL certificates from Red Hat Subscription Management, which will allow us to pull packages over a secure channel.
~~~
curl --create-dirs -o /etc/rhsm/ca/redhat-uep.pem https://ftp.redhat.com/redhat/convert2rhel/redhat-uep.pem
~~~
## Step 4: Download the repository definition
Download a repository definition to our /etc/yum.repos.d/directory.
~~~
#curl -o /etc/yum.repos.d/convert2rhel.repo https://cdn-public.redhat.com/content/public/repofiles/convert2rhel-for-rhel-7-x86_64.repo
~~~
## Step 5: Install convert2rhel
~~~
#yum install -y convert2rhel
~~~


If you are converting by using RHSM and have not yet registered the system, update the /etc/convert2rhel.ini file to include the following data: 
~~~
[subscription_manager]
org = <organization_ID>
activation_key = <activation_key>
~~~

## Step-6: Reviewing the pre-conversion analysis report
~~~
#convert2rhel analyze
~~~

 Each test results in one of the following statuses:
 
   - **Success** - The test was successful and there are no issues for this component.
   - **Error** - The test encountered an issue that would cause the conversion to fail because it is very likely to result in a deteriorated system state. This issue must be resolved before converting.
   - **Overridable** - The test encountered an issue that would cause the conversion to fail because it is very likely to result in a deteriorated system state. This issue must be either resolved or manually overridden before converting.
   - **Warning** - The test encountered an issue that might cause system and application issues after the conversion. However, this issue would not cause the conversion to fail.
   - **Skip** - Could not run this test because of a prerequisite test failing. Could cause the conversion to fail.
   - **Info** - Informational with no expected impact to the system or applications. 

## Step-7: Convert to RHEL
~~~
#convert2rhel --debug
~~~

## Step-8: . Reboot
~~~
#reboot
~~~

## **Resources:**

- [Convert CentOS Linux to RHEL Cheet Sheet](https://developers.redhat.com/cheat-sheets/convert-centos-linux-rhel)
- [Convert2RHEL Official Documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/converting_from_an_rpm-based_linux_distribution_to_rhel/index)
- [Interactive Lab](https://www.redhat.com/en/interactive-labs/migrate-red-hat-enterprise-linux-centos-linux)
  



