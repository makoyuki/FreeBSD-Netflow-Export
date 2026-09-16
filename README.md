# FreeBSD-Netflow-Export
A script for automatically starting a Netflow exporter on boot for FreeBSD.

It uses the `ng_netflow(4)` netgraph module to capture traffic on a chosen
interface and export Netflow v9 records via UDP to a collector.

## Installation

Copy the script to the rc.d directory and mark it executable:

```sh
cp ng_netflow /usr/local/etc/rc.d/ng_netflow
chmod +x /usr/local/etc/rc.d/ng_netflow
```

## Configuration

Set the following variables in `/etc/rc.conf`:

| Variable                     | Required | Default | Description                                  |
|-------------------------------|----------|---------|-----------------------------------------------|
| `ng_netflow_enable`           | no       | `NO`    | Set to `YES` to enable the service on boot    |
| `ng_netflow_interface`        | yes      | -       | Interface to monitor (e.g. `em0`)             |
| `ng_netflow_collect_addr`     | yes      | -       | IP address of the Netflow collector           |
| `ng_netflow_collect_port`     | no       | `4444`  | UDP port of the Netflow collector             |

Example:

```sh
ng_netflow_enable="YES"
ng_netflow_interface="em0"
ng_netflow_collect_addr="192.0.2.10"
ng_netflow_collect_port="2055"
```

## Usage

```sh
service ng_netflow start
service ng_netflow stop
```

See `ng_netflow(4)` for details on the underlying netgraph module.
