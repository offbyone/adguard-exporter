# AdguardHome Prometheus Exporter

[![GoDoc](https://godoc.org/github.com/relrod/adguard-exporter?status.png)](https://godoc.org/github.com/relrod/adguard-exporter)
[![GoReportCard](https://goreportcard.com/badge/github.com/relrod/adguard-exporter)](https://goreportcard.com/report/github.com/relrod/adguard-exporter)

This is a Prometheus exporter for [AdguardHome](https://github.com/AdguardTeam/AdguardHome)'s Raspberry PI ad blocker.
It is based on the famous pihole-exporter [available here](https://github.com/eko/pihole-exporter/)

![Grafana dashboard](https://raw.githubusercontent.com/relrod/adguard-exporter/master/grafana/dashboard.png)

Grafana dashboard is [available here](https://grafana.com/dashboards/13330) on the Grafana dashboard website and also [here](https://raw.githubusercontent.com/relrod/adguard-exporter/master/grafana/dashboard.json) on the GitHub repository.

## Prerequisites

* [Go](https://golang.org/doc/)

## Installation

### Download binary

You can download the latest version of the binary built for your architecture here:

* Architecture **i386** [
[Darwin](https://github.com/relrod/adguard-exporter/releases/latest/download/adguard_exporter-darwin-386) /
[Linux](https://github.com/relrod/adguard-exporter/releases/latest/download/adguard_exporter-linux-386) /
[Windows](https://github.com/relrod/adguard-exporter/releases/latest/download/adguard_exporter-windows-386.exe)
]
* Architecture **amd64** [
[Darwin](https://github.com/relrod/adguard-exporter/releases/latest/download/adguard_exporter-darwin-amd64) /
[Linux](https://github.com/relrod/adguard-exporter/releases/latest/download/adguard_exporter-linux-amd64) /
[Windows](https://github.com/relrod/adguard-exporter/releases/latest/download/adguard_exporter-windows-amd64.exe)
]
* Architecture **arm** [
[Linux](https://github.com/relrod/adguard-exporter/releases/latest/download/adguard_exporter-linux-arm)
]

### From sources

Optionally, you can download and build it from the sources. You have to retrieve the project sources by using one of the following way:
```bash
$ go get -u github.com/relrod/adguard-exporter
# or
$ git clone https://github.com/relrod/adguard-exporter.git
```

Install the needed vendors:

```
$ GO111MODULE=on go mod vendor
```

Then, build the binary (here, an example to run on Raspberry PI ARM architecture):
```bash
$ GOOS=linux GOARCH=arm GOARM=7 go build -o adguard_exporter .
```

## Usage

In order to run the exporter, type the following command (arguments are optional):

Using a password

```bash
$ ./adguard_exporter -adguard_protocol https -adguard_hostname 192.168.10.252 -adguard_username admin -adguard_password qwerty -log_limit 10000
```

```bash
2020/11/04 17:16:14 ---------------------------------------
2020/11/04 17:16:14 - AdGuard Home exporter configuration -
2020/11/04 17:16:14 ---------------------------------------
2020/11/04 17:16:14 AdguardProtocol : https
2020/11/04 17:16:14 AdguardHostname : 192.168.10.252
2020/11/04 17:16:14 AdguardUsername : admin
2020/11/04 17:16:14 AdGuard Authentication Method : AdguardPassword
2020/11/04 17:16:14 ServerPort : 9617
2020/11/04 17:16:14 Interval : 10s
2020/11/04 17:16:14 LogLimit : 10000
2020/11/04 17:16:14 ---------------------------------------
2020/11/04 17:16:14 New Prometheus metric registered: avg_processing_time
2020/11/04 17:16:14 New Prometheus metric registered: num_dns_queries
2020/11/04 17:16:14 New Prometheus metric registered: num_blocked_filtering
2020/11/04 17:16:14 New Prometheus metric registered: num_replaced_parental
2020/11/04 17:16:14 New Prometheus metric registered: num_replaced_safebrowsing
2020/11/04 17:16:14 New Prometheus metric registered: num_replaced_safesearch
2020/11/04 17:16:14 New Prometheus metric registered: top_queried_domains
2020/11/04 17:16:14 New Prometheus metric registered: top_blocked_domains
2020/11/04 17:16:14 New Prometheus metric registered: top_clients
2020/11/04 17:16:14 New Prometheus metric registered: query_types
2020/11/04 17:16:14 New Prometheus metric registered: running
2020/11/04 17:16:14 New Prometheus metric registered: protection_enabled
2020/11/04 17:16:14 Starting HTTP server
2020/11/04 17:16:30 New tick of statistics: 3824 ads blocked / 36367 total DNS queries
```

Once the exporter is running, you also have to update your `prometheus.yml` configuration to let it scrape the exporter:

```yaml
scrape_configs:
  - job_name: 'adguard'
  static_configs:
  - targets: ['localhost:9617']
```

## Available CLI options
```bash
# Interval of time the exporter will fetch data from Adguard
-interval duration (optional) (default 10s)

# Protocol to use to query Adguard
-adguard_protocol string (optional: "http", "https") (default "http")

# Hostname of the Raspberry PI where Adguard is installed
-adguard_hostname string (optional) (default "127.0.0.1")

# Custom port for the Adguard API
-adguard_port string (optional) (default "", becomes 80 if protocol is http, or 443 if https)

# Username to login to Adguard Home
-adguard_username string (optional)

# Password defined on the Adguard interface
-adguard_password string (optional)

# Limit for the return log data
-log_limit string (optional) (default "1000")

# Port to be used for the exporter
-server_port string (optional) (default "9617")
```

## Available Prometheus metrics

| Metric name                       | Description                                                          |
|:---------------------------------:|----------------------------------------------------------------------|
| adguard_avg_processing_time       | This represent the average DNS query processing time                 |
| adguard_num_blocked_filtering     | This represent the number of blocked DNS queries                     |
| adguard_num_dns_queries           | This represent the number of DNS queries                             |
| adguard_num_replaced_parental     | This represent the number of blocked DNS queries (parental)          |
| adguard_num_replaced_safebrowsing | This represent the number of blocked DNS queries (safe browsing)     |
| adguard_num_replaced_safesearch   | This represent the number of blocked DNS queries (safe search)       |
| adguard_top_blocked_domains       | This represent the top blocked domains                               |
| adguard_top_clients               | This represent the top clients                                       |
| adguard_top_queried_domains       | This represent the top domains that are queried                      |
| adguard_query_types               | This represent the types of DNS queries                              |
| running                           | Is Adguard running?                                                  |
| protection_enabled                | Is the protection enabled?                                           |
