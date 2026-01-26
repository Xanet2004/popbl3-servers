Example configuration:

```json title="example configuration"
{
	# Control Agent configuration starts on the next line
	"Control-agent": {

	# First we set up where the HTTP API will listen
		"http-host": "10.20.30.40",
		"http-port": 8000,

	# Optional: add HTTP security headers to responses
		"http-headers": [
			{
				"name": "Strict-Transport-Security",
				"value": "max-age=31536000"
			}
		],

	# Next we configure TLS (HTTPS) settings (certificates and keys)
		"trust-anchor": "/path/to/the/ca-cert.pem",
		"cert-file": "/path/to/the/agent-cert.pem",
		"key-file": "/path/to/the/agent-key.pem",
		"cert-required": true,

	# Then we configure how clients authenticate against the Control Agent
		"authentication": {
			"type": "basic",
			"realm": "kea-control-agent",
			"clients": [
				{
					"user": "admin",
					"password": "1234"
				}
			]
		},

	# Next we specify where the Control Agent will talk to other Kea services
	# (dhcp4, dhcp6, and d2) through control sockets
		"control-sockets": {
			"dhcp4": {
				"comment": "main server",
				"socket-type": "unix",
				"socket-name": "/path/to/the/unix/socket-v4"
			},
			"dhcp6": {
				"socket-type": "unix",
				"socket-name": "/path/to/the/unix/socket-v6",
				"user-context": { "version": 3 }
			},
			"d2": {
				"socket-type": "unix",
				"socket-name": "/path/to/the/unix/socket-d2"
			}
		},

	# Optional: list any hook libraries to extend Control Agent functionality
		"hooks-libraries": [
			{
				"library": "/opt/local/custom_hooks_example.so",
				"parameters": {
					"param1": "foo"
				}
			}
		],

	# Finally, we configure logging
		"loggers": [
			{
				"name": "kea-ctrl-agent",
				"severity": "INFO"
			}
		]

	# Control Agent configuration ends with the next line
	}
}
```