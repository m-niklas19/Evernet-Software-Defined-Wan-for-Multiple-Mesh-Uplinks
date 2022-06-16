# MutterRouter /etc/config/network
```
config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fdec:51d8:ac6a::/48'

config interface 'lan'
        option type 'bridge'
        option ifname 'eth0'
        option proto 'dhcp'

config interface 'wg0'
        option proto 'wireguard'
        option private_key 'QBzf3RwApbJbyg/1C2qHUTZwUybmTaTZvBg4ggjNmGM='
        list addresses '10.20.0.1/32'
        option metric '10'

config wireguard_wg0
        option description 'tunnel1_uplink'
        list allowed_ips '0.0.0.0/0'
        option endpoint_host '172.30.2.4'
        option endpoint_port '7777'
        option persistent_alive '25'
        option public_key 'eybSiN18RJgcvvXW8nGLMHCV2bRGF6xFMKucDYrpaFg='
        option route_allowed_ips 'true'

config interface 'wg1'
        option proto 'wireguard'
        option private_key 'aK5Zq/eYz8LIOmMJRcxWqp/Kx8FgLmtPKbZKdPklqEE='
        list addresses '10.21.0.3/32'
        option metric '20'

config wireguard_wg1
        option description 'tunnel2_uplink'
        list allowed_ips '0.0.0.0/0'
        option endpoint_host '172.30.2.5'
        option endpoint_port '7777'
        option persistent_alive '25'
        option public_key 'h3ExiNRcj8wNQKFkMVQDjwFDIPKfDLYkBWTFI0wSO3Y='
        option route_allowed_ips 'true'
```	

# Uplink1 /etc/config/network
```
config interface 'loopback'
        option device 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fdda:6fd5:a544::/48'

config interface 'lan'
        option type 'bridge'
        option proto 'dhcp'
        option ifname 'eth0'

config interface 'wg0'
        option proto 'wireguard'
        option listen_port '7777'
        option private_key 'SD3c0Xv4fXQ2zX/VD6/fVawQcw28ZM9yAGHcWZaf+HA='
        list addresses '10.20.0.2/24'

config wireguard_wg0
        option description 'mutter_peer'
        list allowed_ips '10.20.0.1/32'
        option public_key 'PZDi7cVg10VhC8MujKyeQgCOYiDTKI6I9/rrKRc1sQA='
```

# Uplink2 /etc/config/network
```
config interface 'loopback'
        option device 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fdda:6fd5:a544::/48'

config interface 'lan'
        option type bridge
        option ifname eth0
        option proto 'dhcp'

config interface 'wg0'
        option proto 'wireguard'
        option listen_port '7777'
        option private_key '8GybwseqbDu//KsyafNRQUnsQyu0K9M2IR11vVmfmH8='
        list addresses '10.21.0.4/24'

config wireguard_wg0
        option description 'mutter_peer'
        list allowed_ips '10.21.0.3/32'
        option public_key 'x+BHlpjNJHbaA8Mhh/Lr1+4LApQjZsQiSsXrjh56qno='
```
