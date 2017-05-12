# ansible-role-vic

Install and manage [vSphere Integrated Containers](https://github.com/vmware/vic-product)

VIC has 3 components:

* [vic-engine](https://github.com/vmware/vic) support only runs on linux hosts, though the vic-machine binaries can support OSX and Windows
* [harbor](https://github.com/vmware/harbor) not yet supported
* [admiral](https://github.com/vmware/admiral) not yet supported

## Requirements:

This rolle only runs on linux hosts, though the vic-machine binaries can support OSX and Windows

vic-engine requires the following installed:
- openssl
- awk, mawk, or gawk

The vic OVA must already be local to the machine you are running the role against.

## Role Variables

Many of these variables are described in the [vic appliance installation guide](https://vmware.github.io/vic-product/assets/files/html/1.1/vic_vsphere_admin/deploy_vic_appliance.html).  
Go there for in depth descriptions of the variables


### You really should set these:

All passwords ought to be set!  *Note root ssh access is off by default.  Change that using `vic_permit_root_login`*

    vic_root_password: this_is_a_bad_password
    vic_registry_db_password: this_is_a_bad_password
    vic_registry_admin_password: this_is_a_bad_password

If you want specific networking setting for the VIC server, specify them using these vars.
Leaving them unset should use DHCP to boot the server.

    vic_network_fqdn: localhost.localdomain
    vic_network_searchpath:
    vic_network_dns:
    vic_network_gateway:
    vic_network_netmask0:
    vic_network_ip0:

Validate certs for the url we download vic from?  **Default is false, but should be True in production.**

    vic_download_validate_certs: False

To configure SSL certificates for harbor (registry), admiral (management), and the fileserver use these.
If none are set, self-signed certs will be created.

    vic_registry_ssl_cert:
    vic_registry_ssl_cert_key:
    vic_management_portal_ssl_cert:
    vic_management_portal_ssl_cert_key:
    vic_fileserver_ssl_cert:
    vic_fileserver_ssl_cert_key:

### Defaults for these are reasonable, so probably no need to change:

Specific version of VIC to install and run

    vic_version: "1.1.0"

Where VIC files will be installed

    vic_install_path: /opt

Generally shouldn't need to update this, any VIC version found at this url ought to be usable.  
If you're installing the VIC appliance, we'll use the locally hosted version instead of this url.

    vic_download_url: "https://bintray.com/vmware/vic/download_file?file_path=vic_{{ vic_version }}.tar.gz"

To (dis)allow ssh into the VIC server as root

    vic_permit_root_login: False 

### Alternatives not tested... you probably shouldn't change them.

    vic_poweron: True
    vic_registry_gc_enabled: False

To change ports of the various components:

    vic_registry_port: 443
    vic_registry_notary_port: 4443
    vic_management_portal_port: 8282
    vic_fileserver_port: 9443

Temporary storage for downloads

    vic_tmp: /tmp



### Change these to create/delete VIC Container Hosts

List of of vic hosts create.
Each key/value pair will be passed to the vic-machine create as-is, so
any configuration supported by [vic-machine](https://github.com/vmware/vic/blob/master/doc/user/usage.md)
should be supported here

## Examples

See [vsphere-install.yml](tests/vsphere-install.yml) for good examples of deploying against vcenter.

#### Example, create a vch
```yamlex
vic_controller_hosts:
  - name: test
    timeout: 5m
    target: https://vcenter.corp.local/Goddard
    user: administrator@home.local
    password: 'some_password'
    tls-cname: test1.home.local
    image-store: esx-a-ssd
    bridge-network: bridge-vic1
    compute-resource: BasementLab
    thumbprint: "29:03:72:8B:73:ED:D8:B6:D7:36:E8:EE:4F:1D:91:DE:9A:2C:3D:4A"
    bridge-network-range: 172.16.0.0/12
    management-network: Management
    management-network-gateway: 0.0.0.0/0:192.168.1.254
    management-network-ip: 192.168.1.19/24
    dns-server: 192.168.1.1
    public-network: VMNet
    organization: tscanlan
    volume-store: "esx-a-ssd/test1-volumes:default"
```

List of of vic hosts delete
each key/value pair will be passed to the vic-machine create as-is, so
any configuration supported by [vic-machine delete](https://github.com/vmware/vic/blob/master/doc/user/usage.md#deleting-a-virtual-container-host)
should be supported here

#### Example, delete two vch
```yamlex
vic_controller_hosts_delete:
  - name: test3
    timeout: 5m
    target: https://vcenter.corp.local/Goddard
    user: administrator@home.local
    password: 'some_password'
    thumbprint: "29:03:72:8B:73:ED:D8:B6:D7:36:E8:EE:4F:1D:91:DE:9A:2C:3D:4A"
  - name: test4
    timeout: 5m
    target: https://vcenter.corp.local/Goddard
    user: administrator@home.local
    password: 'some_password'
    thumbprint: "29:03:72:8B:73:ED:D8:B6:D7:36:E8:EE:4F:1D:91:DE:9A:2C:3D:4A"
```

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yamlex
- hosts: all
  roles:
    - role: vic
  vars:
    vic_controller_hosts:
      - name: test1
        timeout: 5m
        target: https://192.168.1.9/Goddard
        user: administrator@home.local
        password: 'some_password'
        tls-cname: test1.home.local
        image-store: esx-a-ssd
        bridge-network: bridge-vic1
        compute-resource: BasementLab
        thumbprint: "29:03:72:8B:73:ED:D8:B6:D7:36:E8:EE:4F:1D:91:DE:9A:2C:3D:4A"
        bridge-network-range: 172.16.0.0/12
        management-network: Management
        management-network-gateway: 0.0.0.0/0:192.168.1.254
        management-network-ip: 192.168.1.19/24
        dns-server: 192.168.1.1
        public-network: VMNet
        organization: tscanlan
        volume-store: "esx-a-ssd/test1-volumes:default"

```

## License

Copyright © 2017 VMware, Inc. All Rights Reserved.
SPDX-License-Identifier: MIT


## Author Information

Tom Scanlan
<tscanlan@vmware.com>
