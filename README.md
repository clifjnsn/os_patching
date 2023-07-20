# Operating System Patching Role
The purpose of this role is to patch/update the operating system of each server.
The first thing this role does is check available disk space on the server (on the following volumes):
root (/)
var  (/var)
tmp  (/tmp)
If the free space on any of these listed volumes is less than the value set in variable "min_free_space", then the update is flagged as failed, and if the variable mail_enabled is True, sends an email indicating the failure.

The tasks for the Operating System updates has been split into separate tasks files, according the operating system distribution:
* Ubuntu / Debian = ubuntu.yml
* Centos / RedHat = redhat.yml

## Default Variables
The following variables have default settings in the defaults/main.yml file:
* mail_enabled    - Controls whether or not an email will be sent upon success/failure of the patching
* mail_server     - The IP or DNS name of the email/SMTP server
* mail_port       - The port number which will be used by the mail module to connect to the mail_server
* mail_success_to - The email address to which to send successful completion emails to
* mail_failure_to - The email address to which to send failure completion emails to
* min_free_space  - The minimum number of bytes available on the 3 volumes listed above, below which no updates should be applied
* mail_from       - The email address to use as the From: address on the success/failure emails

## Patching Steps
Here is a summary of the steps performed during this patching work:
* As mentioned above, we first check that available storage space on a select number of volumes.
* Clean the package manager cache, and pull latest subscription-manager information (in the case of RedHat)
* Pull a list of packages for which an update is available for (and display it to the screen)
* Insure a couple of tools/packages exist on the system
* Apply the updates, wait for the updates to finish, and capture the output of the command
* If an error is detected in the patching output, send an "error" e-mail
* Detect if the package updates now require a system reboot
* Reboot (if required), and time how long that reboot took
* Query the package update /var/log log file, and store this list in a variable, to be included in the final "success" email
* Send a "success" email, which includes: 
  - Start/Stop time for entire patching run for the server
  - Reboot duration (if a server was rebooted)
  - List of packages which were upgraded/installed

## Notes
* The playbook which calls this role must include the option:
~~~
 force_handlers: true
~~~
* This role detects whether or not a reboot is required after a successful update, and reboots only the servers which require the update
* Even though the Ansible mail module supports email CC: addresses, this is not implemented.  If you need to send to more than one address, specify the list of addresses as a YAML list:
~~~
- address1
- address2
~~~
This hasn't been tested yet, but I believe this is how it would work

## Tested Successfully on:
* Ubuntu 18.04
* Ubuntu 20.04
* Ubuntu 22.04
* RHEL 7.9
* RHEL 8.6
* Centos 7
* Debian 9
* Debian 10
