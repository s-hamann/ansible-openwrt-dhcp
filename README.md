OpenWrt DHCP
============

This role handles DHCP server (Dnsmasq/odhcpd) configuration on [OpenWrt](https://www.openwrt.org/) targets.

Requirements
------------

This role has no special requirements on the controller.

It does, however, require a working [Python](https://www.python.org/) installation on the target system or [gekmihesg's Ansible library for OpenWrt](https://github.com/gekmihesg/ansible-openwrt) on the Ansible controller.

Role Variables
--------------

* `dhcp_dnsmasq`  
    A dictionary of OpenWrt configuration settings for Dnsmasq.
    Keys are UCI options, values are their values.
    Refer to [the documentation](https://openwrt.org/docs/guide-user/base-system/dhcp) for valid values and what they do.
    Optional.
* `dhcp_odhcpd`  
    A dictionary of OpenWrt configuration settings for odhcpd.
    Keys are UCI options, values are their values.
    Refer to [the documentation](https://openwrt.org/docs/techref/odhcpd) for valid values and what they do.
    Optional.
* `dhcp_pools`  
    A dictionary of OpenWrt configuration settings for DHCP pools.
    Dictionary keys are pool names and values are in turn dictionaries that describe the pool configuration.
    Of these dictionaries, the keys are UCI option names and value the respective configuration values.
    Refer to [the documentation](https://openwrt.org/docs/guide-user/base-system/dhcp#dhcp_pools) for valid values and what they do.
    Defaults to OpenWrt's default pools, i.e. provide DHCP on the `lan` interface and ignore the `wan` interface.
* `dhcp_static_leases`  
    A list of static DHCP leases.
    Each list element is a dictionary where the key are UCI option and value are their values.
    Refer to [the documentation](https://openwrt.org/docs/guide-user/base-system/dhcp#static_leases) for valid values and what they do.
    Static leases are added to UCI as well as `/etc/ethers`.
    Optional.
* `dhcp_pxe`  
    A list of PXE booting options.
    Each list element is a dictionary where the key are UCI option and value are their values.
    Refer to [the documentation](https://openwrt.org/docs/guide-user/base-system/dhcp#booting_options) for valid values and what they do.
    Optional.
* `dhcp_ipsets`  
    A list of Netfilter IP sets to populate.
    Each list element is a dictionary where the key are UCI option and value are their values.
    Refer to [the documentation](https://openwrt.org/docs/guide-user/base-system/dhcp#ip_sets) for valid values and what they do.
    Optional.
* `dhcp_relays`  
    A dictionary of OpenWrt configuration settings for DHCP relays.
    Dictionary keys are relay names and values are in turn dictionaries that describe the relay configuration.
    Of these dictionaries, the keys are UCI option names and value the respective configuration values.
    Refer to [the documentation](https://openwrt.org/docs/guide-user/base-system/dhcp#dhcp_relay) for valid values and what they do.
    Optional.
* `dhcp_dnsmasq_instances`  
    A dictionary of OpenWrt configuration settings for additional Dnsmasq instances.
    Dictionary keys are instance names and values are in turn dictionaries that describe the instance configuration (see `dhcp_dnsmasq`).
    Optional.
* `dhcp_dnsmasq_extra_options`  
    A dictionary of Dnsmasq configuration options to set.
    Keys are Dnsmasq options, values are their values.
    Refer to [the documentation](https://thekelleys.org.uk/dnsmasq/docs/dnsmasq-man.html) for valid values and what they do.
    For options that do not accept a value, set the value to `true` (e.g. `no-hosts: true`).
    Optional.
* `dhcp_dnsmasq_package`  
    The name of the package that provides Dnsmasq.
    Choices are `dnsmasq-full` and `dnsmasq`.
    The latter has fewer features but installed by default on OpenWrt.
    If the configuration requires features that are only available in the `dnsmasq-full` package, it is selected automatically.
    Note that switching between Dnsmasq packages may cause a short downtime of the DNS service.
    Default is `dnsmasq`.
* `dhcp_dnsmasq_enabled`  
    Whether to enable the `dnsmasq` service.
    Default is `true`.
* `dhcp_odhcpd_package`  
    The name of the package that provides odhcpd.
    Choices are `odhcpd` and `odhcpd-ipv6only`.
    The latter has fewer features but installed by default on OpenWrt.
    If the configuration requires features that are only available in the `odhcpd` package, it is selected automatically.
    Default is `odhcpd-ipv6only`.
* `dhcp_odhcpd_enabled`  
    Whether to enable the `odhcpd` service.
    Default is `true`.

Dependencies
------------

This role does not depend on any specific roles.

Example Configuration
---------------------

The following is a short example for some of the configuration options this role provides:

```yaml
dhcp_dnsmasq:
  local: "/example.lan/"
  domain: "example.lan"
  min_cache_ttl: 300
  max_cache_ttl: 3600
  address:
    - /example.com/
    - /example.org/192.168.1.1
dhcp_pools:
  wan:
    ignore: true
  lan:
    dhcpv4: server
    start: 100
    limit: 150
    leasetime: 12h
  guest:
    dhcpv4: server
    start: 10
    limit: 40
    leasetime: 30m
dhcp_static_leases:
  - ip: 192.168.1.10
    mac: 12:34:56:78:90:ab
    name: mail1.example.lan
    leasetime: 7d
  - ip: 192.168.1.20
    mac: fe:dc:ba:09:87:65
    name: mail2.example.lan
dhcp_dnsmasq_extra_options:
  neg-ttl: 1200
  dns-loop-detect: true
  mx-host:
    - example.lan,mail1.example.lan,10
    - example.lan,mail2.example.lan,20
dhcp_odhcpd_enabled: false
```

License
-------

MIT
