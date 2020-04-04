# Connecting to an Encrypted Redis instance using `redis-cli`

If encryption is enabled on a Redis cache, `redis-cli` cannot natively connect to it as it does not support TLS. To overcome this issue, we need to tunnel the connection through SSL using `stunnel`.

## Procedure

1. SSH onto the appropriate client machine
2. Install the `stunnel` and `redis` using whichever package manager is available.
  * For Amazon's Alpine image, use `apk --update add [package]`
3. Create `/etc/stunnel/redis-cli.conf` (Don't put other shit in here, websites are wrong and stupid):
```
fips = no
setuid = root
setgid = root
pid = /var/run/stunnel.pid
debug = 7
delay = yes
[redis-cli]
    client = yes
    accept = 127.0.0.1:6379
    connect = [Redis_url_including_port]
```
4. Run `stunnel`
5. Verify tunnel creation using `netstat -tulnp | grep -i stunnel`
6. Connect to redis using `redis-cli -h localhost -p 6379 -a [password]`
