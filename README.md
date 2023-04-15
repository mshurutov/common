[TOC]

Role: common
============

Role common is base role for deploy system settings for all hosts in your infrastructure. All my other roles use any variables from this role.
* kernel options;
* disk options: extended partitions, LVM etc;
* network settings: IP address(es), hostname, domainname etc;
* package manager settings;
* base system packages installing.
Copyright (C) 2023  Mikhail Shurutov

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301, USA.

Requirements
------------
There is required python v3.* because python v2.* is out of live.

Example Playbook
----------------

There is example of playbook:
    - hosts: all
      roles:
        - role: common

Role Variables
--------------

### Variables that must be changed
* `common_ip4_default: "127.0.0.1"` should be unique for every host;
* `common_short_hostname: "myhost"` should be unique for every host;
* `common_domain_name: "example.com"` is "example.com" your real domain name? I don't believe it!
and
* `common_full_hostname: "{{ common_short_hostname }}.{{ common_domain_name }}"`
These variables is used in every my role.

### Variables and group of variables that are recommended changed
* `common_country_name: "RU"`: set your country;
* any `*ssl*` variables: dir and file names, may be system-depended;
#### **locales, languages and console keyboard, font and font map**
I set these variables for use in Russian.
* `common_locales_list`: list of locales (see default/main.yml);
* `common_lang_settings`: list of language settings (see default/main.yml);
* `common_kbd_n_cfont_settings`: list of console keyboard, font and font map settings (see default/main.yml);

#### Timesync parameters
These parameters is set for "systemd-timesyncd" service and sync time with pool.ntp.org. If you use other timesync service you should change this settings, for example, chrony, that is default in debian bullseye:
    common_timesync_service: "chrony"
    common_time_config_path: "/etc/chrony/chrony.conf"
    common_timesync_params:
      - name: "pool"
        value: "2.debian.pool.ntp.org iburst"

#### Network parameteres
Many OS have NetworkManager as service for setup network connections, but it settings is difficult for templating, so by default `common_network_manager` set to dhcp and `common_nm_connections` is empty. For example follow settings is used for one of my testing groups (`common_ip4_default` is unique for each host in my network, and `conn_name` as `ifname` is set by default during installation):
    common_nm_connections:
      - conn_name: 'enp1s0'
        state: present
        ifname: 'enp1s0'
        ip4: "{{ common_ip4_default }}/24"
        gw4: "192.168.10.1"
        dns4:
          - "192.168.1.3"

#### DNS resolver settings
These parameters is set for `systemd-resolved`. But Debian bullseye use old system resolver, so there is an examle of dns resolver for this system:
    common_resolver: "system"
    common_resolver_config: "/etc/resolv.conf"
    common_resolver_params:
      - name: "nameserver"
        value: "{{ common_dns }}"
      - name: "search"
        value: "{{ common_domain_name }}"

#### Package manager settings
I use Gentoo, so there are many variables, beginning on `portage_` prefix is used for this distribution.
I define system packages for Gentoo, RedHat8-based, Debian-based Linux distribution depend on OS an include follow packages:
##### common packages
- "acl"
- "bash-completion" 
- "mlocate"
- "p7zip"
- "python3"
- "sudo"
- "vim"
- "wipe"
##### network utilities (with diagnostic)
- "bind-utils"
- "net-tools"
- "network-manager"
- "tcpdump"
- "traceroute"
- "telnet"
##### common diagnostic utilities
- "strace"
- "lsof"

These utilities may be in several (no same name) packages (see default/main.yml).

License
-------

[GPLv2](https://www.gnu.org/licenses/old-licenses/gpl-2.0.txt)
