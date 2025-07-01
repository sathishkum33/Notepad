🛠 Step 1: Check and Set SELinux to Permissive Mode

SELinux (Security-Enhanced Linux) can interfere with certain services and installations. This step ensures that it is set to Permissive mode if it is currently Enforcing.

🔍 1.1 Verify SELinux Status

Run the following command to check the current SELinux status:

sestatus

Example output:

SELinux status:                 enabled
Current mode:                   enforcing

> ✅ If SELinux status is disabled, you can skip the next step.
⚠️ If Current mode is enforcing, proceed to set it to permissive.




---

🔧 1.2 Set SELinux to Permissive Temporarily

This change will be lost after reboot.

sudo setenforce 0

Verify again:

sestatus

Expected output:

Current mode:                   permissive


---

🔧 1.3 (Optional) Set SELinux to Permissive Permanently

Edit the SELinux configuration file:

sudo vi /etc/selinux/config

Find the line:

SELINUX=enforcing

Change it to:

SELINUX=permissive

Save and exit the file, then reboot your system:

sudo reboot
