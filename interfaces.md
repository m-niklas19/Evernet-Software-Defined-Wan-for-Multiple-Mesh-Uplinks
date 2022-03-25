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