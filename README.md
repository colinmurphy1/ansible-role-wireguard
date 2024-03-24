# WireGuard Ansible role

Ansible role that configures WireGuard interfaces on Linux machines.

## Requirements

None, but requires the ability to create WireGuard interfaces. This may not work on unprivileged Linux containers, but will work on bare metal and fully-virtualized virtual machines.

This role has been developed for use with Debian and Alpine Linux. Other distros may work and support may be added on a best-effort basis. Feel free to submit a pull request if you would like support to be added to other distributions.

## Known issues

Currently, the role does not restart the WireGuard interface when changes are made. One must restart the interface using `systemctl` or `rc-service` manually after changing the interface.

## Role Variables

* `wg_interfaces`: List of interfaces managed by this role.
  * `private_key`: Private key used by the wg interface.
  * `listen`: Enable listening for connections on the interface. Defaults to false.
  * `listen_port`: If listen is true, the port to listen on. defaults to 51820.
  * `addresses`: List of IPv4 and IPv6 addresses.
  * `started`: Start the interface after creation
  * `enabled`: Enable the interface to start on boot
  * `dns`: DNS server to advertise to peers
  * `post_up`: Post-up commands
  * `pre_down`: Pre-down commands
  * `post_down`: Post-down commands
  * `peers`: List of peers
    * `name`: Name of the peer. Optional.
    * `public_key`: Peer public key
    * `allowed_ips`: IP addresses the peer can use
    * `endpoint`: If connecting to another peer that is listening, the endpoint to connect to. Optional.
    * `persistent_keepalive`: Enable persistent keepalive. Recommended for peers behind NAT.
* `wg_persistent_keepalive_time`: For peers using persistent keepalive, set the seconds used for keepalive timers. Defaults to 30 seconds. 
* `wg_sysctl`: If set to true, the role will manage sysctls for IPv4 and IPv6 forwarding.
* `wg_sysctl_ipv4_fwd`: Enable IPv4 forwarding
* `wg_sysctl_ipv6_fwd`: Enable IPv6 forwarding

Default interface settings: see `defaults/main.yml`.

## Dependencies

None.

## Example playbook

```yaml

- host: peer1
  become: yes

  roles:
    - wireguard
  
  vars:
    wg_interfaces:
      - interface: wg0
        listen: true
        addresses:
          - 192.168.100.1/24
        private_key: peer1 private key
        started: true
        enabled: true
        peers:
          - name: peer2
            public_key: peer2 public key
            allowed_ips:
              - 192.168.100.2/32
              - 192.168.1.0/24 # adds a route for this network
            persistent_keepalive: true
```

## License 

MIT

## Author

Colin Murphy <colin@colinmurphy.me>
