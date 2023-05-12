## Purpose
Deploy virtual IP (VIP) using keepalived and haproxy
## Setup
```caption=My first C program
apt install keepalived haproxy
nano /etc/keepalived/keepalived.conf
global_defs {
  router_id test1
}
vrrp_script chk_haproxy {
  script "killall -0 haproxy"
  interval 2
  weight 2
}
vrrp_instance VI_1 {
  virtual_router_id 51
  advert_int 1
  priority 100
  state MASTER
  interface enp1s0
  virtual_ipaddress {
    10.1.184.239 dev enp1s0
  }
 authentication {
     auth_type PASS
     auth_pass 123456
     }
  track_script {
    chk_haproxy
  }
}
service keepalived start
nano /etc/sysctl.conf
net.ipv4.ip_nonlocal_bind=1
sysctl -p
nano /etc/haproxy/haproxy.cfg
global
	log /dev/log	local0
	log /dev/log	local1 notice
	chroot /var/lib/haproxy
	stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
	stats timeout 30s
	user haproxy
	group haproxy
	daemon

	# Default SSL material locations
	ca-base /etc/ssl/certs
	crt-base /etc/ssl/private

	# Default ciphers to use on SSL-enabled listening sockets.
	# For more information, see ciphers(1SSL). This list is from:
	#  https://hynek.me/articles/hardening-your-web-servers-ssl-ciphers/
	# An alternative list with additional directives can be obtained from
	#  https://mozilla.github.io/server-side-tls/ssl-config-generator/?server=haproxy
	ssl-default-bind-ciphers ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:RSA+AESGCM:RSA+AES:!aNULL:!MD5:!DSS
	ssl-default-bind-options no-sslv3

defaults
	log	global
	mode	tcp
	option	tcplog
	option	dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
frontend fe
	bind 10.1.184.239:8888
	default_backend be
backend be
	balance roundrobin
	mode tcp
	server be1 10.1.184.233:8088 check
	server be2 10.1.184.234:8088 check
        server be3 10.1.184.235:8088 check
```
