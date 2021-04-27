# Connecting to an Encrypted Redis instance using `redis-cli`

If encryption is enabled on a Redis cache, `redis-cli` cannot natively connect to it as it does not support TLS. To overcome this issue, we need to tunnel the connection through SSL using `stunnel`.

## Procedure

1. SSH onto the appropriate client machine
2. Install `stunnel4`,`redis-tools` and (optionally) `net-tools` using whichever package manager is available.
  * For Amazon's Alpine image, use `apk --update add [package]`
  * For many Debian-based images, use `apt install [packages]`
3. Create `/etc/stunnel/stunnel.conf`:
```
fips = no
setuid = root
setgid = root
pid = /var/run/stunnel.pid
debug = 7
options = NO_SSLv2
options = NO_SSLv3
delay = yes
[redis-cli]
client = yes
accept = 127.0.0.1:6379
connect = [REDIS_URL_INCLUDING_PORT]
```
4. Run `stunnel`
5. (Optionally) Verify tunnel creation using `netstat -tulnp | grep -i stunnel`
6. Connect to redis using `redis-cli -h localhost -p 6379 -a [REDIS_PASSWORD]`
