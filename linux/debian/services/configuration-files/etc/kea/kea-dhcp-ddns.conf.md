Template configuration:

```json title="template configuration"
"DhcpDdns": {
    "ip-address": "127.0.0.1",
    "port": 53001,
    "dns-server-timeout": 500,
    "ncr-protocol": "UDP",
    "ncr-format": "JSON",
    "tsig-keys": [ ],
    "forward-ddns": {
        "ddns-domains": [ ]
    },
    "reverse-ddns": {
        "ddns-domains": [ ]
    }
}
```