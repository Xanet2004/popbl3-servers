Example configuration:

```json title="example configuration"
{
	# DHCPv4 configuration starts on the next line
	"Dhcp4": {
	
	# First we set up global values
	    "valid-lifetime": 4000,
	    "renew-timer": 1000,
	    "rebind-timer": 2000,
	
	# Next we set up the interfaces to be used by the server.
	    "interfaces-config": {
	        "interfaces": [ "eth0" ]
	    },
	
	# And we specify the type of lease database
	    "lease-database": {
	        "type": "memfile",
	        "persist": true,
	        "name": "/var/lib/kea/dhcp4.leases"
	    },
	
	# Finally, we list the subnets from which we will be leasing addresses.
	    "subnet4": [
	        {
	            "id": 1,
	            "subnet": "192.0.2.0/24",
	            "pools": [
	                {
	                    "pool": "192.0.2.1 - 192.0.2.200"
	                }
	            ],
	            "reservations": [
				{
					"hw-address": "1a:1b:1c:1d:1e:1f",
					"ip-address": "192.0.2.202"
				}
	        },
	        {
		        "id": 2,
	            "subnet": "192.0.3.0/24",
	            "pools": [
	                {
	                    "pool": "192.0.3.1 - 192.0.3.200"
	                }
	            ]
	        }
	    ]
	# DHCPv4 configuration ends with the next line
	}
}
```