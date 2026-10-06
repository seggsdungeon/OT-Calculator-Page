# Overtime Calculator

A single static page (`index.html`). It's plain HTML and JS, with no build step and no dependencies. Every value is calculated in the browser.

**Inputs:** arrival time, leave time (defaults to now, with a "Now" button), work hours (default 7), break toggle and minutes (default on, 45), and available OT (default 0).
**Outputs:** hours worked, today's OT, new OT balance (all in decimal hours), and the leave time that gives ±0 OT.

## Deploy in an Alpine LXC

Option A: BusyBox httpd. It's already on Alpine, so nothing needs installing.

```sh
mkdir -p /var/www/ot && cp index.html /var/www/ot/
cat > /etc/init.d/ot-httpd <<'S'
#!/sbin/openrc-run
command=/bin/busybox
command_args="httpd -f -p 80 -h /var/www/ot"
command_background=true
pidfile=/run/ot-httpd.pid
S
chmod +x /etc/init.d/ot-httpd
rc-update add ot-httpd default && rc-service ot-httpd start
```

Option B: nginx.

```sh
apk add nginx
mkdir -p /var/www/ot && cp index.html /var/www/ot/
cat > /etc/nginx/http.d/default.conf <<'N'
server { listen 80 default_server; root /var/www/ot; index index.html; }
N
rc-update add nginx default && rc-service nginx start
```

Then open `http://<container-ip>/`.
