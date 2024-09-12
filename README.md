# ansible-role-rtpengine

Ansible role for installing rtpengine

![CI](https://github.com/miarec/ansible-role-rtpengine/actions/workflows/ci.yml/badge.svg?event=push)




# Role Variables

For a full list of variables, see [defaults/main.yml](./defaults/main.yml)

## Installation Variables

 - `rtpengine_version`: version of rtpengine to install, See ttps://github.com/sipwise/rtpengine/tags for available version
 - `rtpengine_user`: linux user to run the service, default = `rtpengine`
 - `rtpengine_group`: linux group the linux user belongs to, default = `rtpengine`

 - `rtpengine_cleanup_downloads`: when true, source download files will be deleted after successful install, default = `true`
 - `rtpengine_force_install`: when true, rtpengine will be installed and configured, even if rtpengine is already installed. default = `false`

## Network Settings
 - `rtpengine_addr`: IPv4 listen IP address
 - `rtpengine_advaddr`: Optional - Set advertised address of rtpengine. Useful if the rtpengine is behind a NAT firewall
 - `rtpengine_ctrl_protocol`: protocol used to communicate with Kamailio, options are: 'ng', 'tcp-ng', 'udp', 'tcp', 'http', or 'https', default is `ng`
 - `rtpengine_ctrl_port`: Port that rtpengine will listen on, default is `2223`
 - `rtpengine_min_port`: Lower limit on UDP ports range that the rtpengine uses for RTP/RTCP sessions
 - `rtpengine_max_port`: Upper limit on UDP ports range that the rtpengine uses for RTP/RTCP sessions

## Logging

- `rtpengine_log_dir`: directory for log files, default = `/var/log/rtpengine`
- `rtpengine_log_file`: name of path to redis log file, default = `rtpengine.log`

## Configuration
 - `rtpengine_config_file`: Optional, you can supply a path to a config file with addiotnal configuration

# Example Playbook

```yaml
# ------------------------------------------------
# Install rtpengine
# ------------------------------------------------
- name: Deploy rtpengine
  hosts: rtp
  become: true

  pre_tasks:

    - include_vars: override_vars/custom.yml
      failed_when: false

    - name: Install packages
      package:
        name: "{{ item }}"
        state: present
      with_items:
        - rsyslog  # required for logging
        - nftables # required for kernel forwarding

    - name: Enable nftables
      systemd:
        name: nftables
        enabled: true
        state: started

    - set_fact:
        rtpengine_addr: 192.168.1.10


  roles:
    - role: 'rtpengine'

  tags:
```


# Kamailio Config
to support rtpengine, the following should be provided in the kamailio config

```
#!define WITH_RTPENGINE

...

#!ifdef WITH_RTPENGINE
loadmodule "rtpengine.so"
#!else
loadmodule "rtpproxy.so"
#!endif

...

#!ifdef WITH_RTPENGINE
# ----- rtpengine params -----
modparam("rtpengine", "rtpengine_sock", "udp:172.31.9.87:2223")
#!else
# ----- rtpproxy params -----
{% for addr in rtpproxy_sock_addresses %}
modparam("rtpproxy", "rtpproxy_sock", "{{ addr }}")
{% endfor %}
#!endif

```