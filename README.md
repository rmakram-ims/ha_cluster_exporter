# ha_cluster_exporter

[![Build Status](https://travis-ci.org/ClusterLabs/ha_cluster_exporter.svg?branch=master)](https://travis-ci.org/ClusterLabs/ha_cluster_exporter)

This is a bespoke Prometheus exporter used to enable the monitoring of Pacemaker based HA clusters.  

## Table of Contents
1. [Features](#features)
2. [Installation](#installation)
3. [Usage](#usage)
   1. [Metrics](doc/metrics.md)
5. [Contributing](#contributing)
   1. [Design](doc/design.md)
   2. [Development](doc/development.md)
5. [License](#license)

## Features

The exporter is a stateless HTTP endpoint. On each HTTP request, it locally inspects the cluster status by parsing pre-existing distributed data, provided by the tools of the various cluster components.

Exported data include:
- Pacemaker cluster summary, nodes and resources stats 
- Corosync ring errors and quorum votes
  (note: only Corosync version 2 is supported)
- SBD devices health status 
- DRBD resources and connections stats  
  (note: only DBRD v9 is supported; for v8.4, please refer to the [Prometheus Node Exporter](https://github.com/prometheus/node_exporter) project)

A comprehensive list of all the metrics can be found in the [metrics document](doc/metrics.md).

## Installation

The project can be installed in many ways, including but not limited to:

1. [Manual clone & build](#manual-clone-&-build)
2. [Go](#go)
3. [RPM](#rpm)

### Manual clone & build

```
git clone https://github.com/ClusterLabs/ha_cluster_exporter
cd ha_cluster_exporter
make
make install
```

### Go

```
go get github.com/ClusterLabs/ha_cluster_exporter
```

### RPM
You can find the repositories for RPM based distributions in [SUSE's Open Build Service](https://build.opensuse.org/package/show/server:monitoring/prometheus-ha_cluster_exporter).  
On openSUSE or SUSE Linux Enterprise you can just use the `zypper` system package manager:
```shell
export DISTRO=SLE_15_SP1 # change as desired
zypper addrepo https://download.opensuse.org/repositories/server:/monitoring/$DISTRO/server:monitoring.repo
zypper install prometheus-ha_cluster_exporter
```

## Usage

You can run the exporter in any of the cluster nodes. 

```
$ ./ha_cluster_exporter  
INFO[0000] Serving metrics on 0.0.0.0:9664
```

Though not strictly required, it is _strongly_ advised to run it in all the nodes.

It will export the metrics under the `/metrics` path, on port `9664` by default.

While the exporter can run outside a HA cluster node, it won't export any metric it can't collect; e.g. it won't export DRBD metrics if it can't be locally inspected with `drbdsetup`.  
A warning message will inform the user of such cases.

Please, refer to [doc/metrics.md](doc/metrics.md) for extensive details about all the exported metrics.

**Hint:**
You can deploy a full HA Cluster via Terraform with [SUSE/ha-sap-terraform-deployments](https://github.com/SUSE/ha-sap-terraform-deployments).

### Configuration

All the runtime parameters can be configured either via CLI flags or via a configuration file, both or which are completely optional.

For more details, refer to the help message via `ha_cluster_exporter --help`.

**Note**:
the built-in defaults are tailored for the latest version of SUSE Linux Enterprise and openSUSE.

The program will scan, in order, the current working directory, `$HOME/.config`, `/etc` and `/usr/etc` for files named `ha_cluster_exporter.(yaml|json|toml)`.
The first match has precedence, and the CLI flags have precedence over the config file.

Please refer to the example [YAML configuration](ha_cluster_exporter.yaml) for more details.

### systemd integration

A [systemd unit file](ha_cluster_exporter.service) is provided with the RPM packages. You can enable and start it as usual:  

```
systemctl --now enable prometheus-ha_cluster_exporter
```

## Development

Pull requests are more than welcome!

We recommend having a look at the [design document](doc/design.md) and the [development notes](doc/development.md) before contributing.

## License

Copyright 2019-2020 SUSE LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
