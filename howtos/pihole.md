1. Create a new LXC container (this how-to is based on either: Ubuntu xenial or Debian Jessie).
2. Assign a static IP address for the LXC container at:
  - Regular LXC: ???
  - Turris Omnia LXC: `http://192.168.1.1/cgi-bin/luci/admin/network/dhcp`
3. Create a new static entry for the LXC container e.g.
  1. Hostname: `pihole`.
  2. MAC-Address: can be found at: `http://192.168.1.1/cgi-bin/luci/admin/services/lxc` **> container dropdown more > configure >** search for the phrase `lxc.network.hwaddr`.
  3. IPv4-Address: whatever suits your situation (e.g. `192.168.1.2`).
4. Either:
  - Regular LXC: Add the line `lxc.start.auto = 1` to the container's configuration file.
  - Turris omnia: Edit configuration file `/etc/config/lxc-auto` as for example:

    ```shell
    config container
      option name pihole
      option timeout 30
    ```
    Every container configured here will get started at the boot and each of them will be correctly halted during the shutdown. Set timeout option to specify how much time in seconds do the containers have to gracefully shutdown before being killed. The default value is 300.
5. Boot the container and gain entrance:

  ```shell
$ lxc-start -n pihole
$ lxc-attach -n pihole
```
6. Install base packages required to get the installer going:

  ```shell
$ apt-get install -y ca-certificates curl
```
7. Install pi-hole (ncurses installer):

  ```shell
$ curl -sSL https://install.pi-hole.net | bash
```
  1. When asked for (ipv4) nameservers, select `custom` and enter `192.168.1.1` (Turris Omnia IP).
  2. Note the password at the end of the installation wizard (although it can be changed later too if you forget with the command: `pihole -a -p new_password`).
8. Check if it's running `http://192.168.1.2/admin` (the password is generated just after the installation).
9. Change DHCP settings of the LAN interface to use the pihole as primary DNS, router as fallback:
  1. Browse to: `http://192.168.1.1/cgi-bin/luci/admin/network/network/lan`
  2. Navigate to: **DHCP Server > Advanced Settings**
  3. **DHCP-Options**: `6,192.168.1.2,192.168.1.1`
> Alternatively configure clients statically to use the pihole as their DNS server.
10. Renew your existing IP/leases on your (desktop) clients (to apply the new DNS servers), if not configured statically (as previously mentioned as an alternative method).