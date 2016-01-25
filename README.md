# Simple Port Forwarding for BOSH
## Summary
This release was created for the express purpose of allowing me to easily (and repeatably) forward
any ports I want from bosh-lite via my manifest.

## TODO
- Add support for specifying different port mappings for multiple jobs in the manifest.

## Usage

To use this bosh release, first upload it to your bosh:

```
bosh target BOSH_HOST
git clone https://github.com/cloudfoundry-community/port-forwarding-boshrelease.git
cd port-forwarding-boshrelease
bosh upload release releases/port-forwarding-1.yml
```

Next, add the release to your manifest:

<pre>
<code>
releases:
- name: bosh
  version: 246
- name: bosh-warden-cpi
  version: 29
- name: garden-linux
  version: 0.330.0
<b>- name: port-forwarding</b>
<b>  version: latest</b>
</code>
</pre>

Simply add the "port_forwarding" template to your job as in this example:

```
jobs:
- name: bosh
  instances: 1
  resource_pool: default
  persistent_disk_pool: disks
  networks:
  - name: default
    static_ips: (( static_ips(0) ))
  templates:
  - {name: nats, release: bosh}
  - {name: redis, release: bosh}
  - {name: blobstore, release: bosh}
  - {name: postgres, release: bosh}
  - {name: director, release: bosh}
  - {name: health_monitor, release: bosh}
  - {name: warden_cpi, release: bosh-warden-cpi}
  - {name: garden, release: garden-linux}
  - {name: port_forwarding, release: port-forwarding}
```

Then, define the ports you would like to forward:

```
properties:
  port_forwarding:
    rules:
      - external_port: 9200
        internal_ip: 10.244.1.2
        internal_port: 9200
```
