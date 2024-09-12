# ansible-role-rtpengine

Ansible role for installing rtpengine

![CI](https://github.com/miarec/ansible-role-rtpengine/actions/workflows/ci.yml/badge.svg?event=push)

# To do

- Configure rtpengine
- tshoot nftables/kernel forwarding issue - this was issue with WSL and molecule
- User permission for rtpengine user
- Add steps to instal andconfigure nftables


apt install nftables
systemctl enable nftables
systemctl start nftables

nft add table ip filter

# Role Variables

For a full list of variables, see [defaults/main.yml](./defaults/main.yml)

## Installation Variables

 - `rtpengine_version`: version of rtpengine to install, default = `latest`. Check the available releases at [rtpengine github repo](https://github.com/sippy/rtpengine/releases)
 - `rtpengine_user`: linux user to run the service, default = `rtpengine`
 - `rtpengine_group`: linux group the linux user belongs to, default = `rtpengine`

 - `rtpengine_cleanup_downloads`: when true, source download files will be deleted after successful install, default = `true`
 - `rtpengine_force_install`: when true, rtpengine will be installed and configured, even if rtpengine is already installed. default = `false`

## Network Settings

 - `rtpengine_ctrl_socket`: The control socket for rtpengine.

## Logging

- `rtpengine_log_dir`: directory for log files, default = `/var/log/rtpengine`
- `rtpengine_log_file`: name of path to redis log file, default = `rtpengine.log`



# Known issues

rtpengine release files for 3.x are broken. They do not include external, like libucl

During complication, we see the error:

    libucl_test.c:10:10: fatal error: ucl.h: No such file or directory

It looks, the developers didn't expect the application is compiled from GitHub source files rather than release files.


At the same time, the release files for 2.x version are referencing `<sys/sysctl.h>` file that has been removed in the latest glibc.
