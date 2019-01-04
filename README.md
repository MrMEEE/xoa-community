Here is howto install XOA community edition on a CentOS7:

```
Install NodeJS and the Redis DB
yum install rh-nodejs8-npm.x86_64 rh-redis32.x86_64 git gcc make

Start and enable Redis
systemctl start rh-redis32-redis.service
systemctl enable rh-redis32-redis.service

Create a service user for XOA
useradd -m xoa

Download the source
cd /opt && git clone -b master http://github.com/vatesfr/xen-orchestra
chown xoa:xoa -R /opt/xen-orchestra/

Create Data folder
mkdir /var/lib/xo-server
chown xoa:xoa /var/lib/xo-server/

Install yarn (package manager + more)
scl enable rh-nodejs8 "npm -g install yarn"

Build XOA
scl enable rh-nodejs8 "su xoa -c \"cd /opt/xen-orchestra && yarn && yarn build\""

Install XOA systemd service
echo "[Unit]
Description=XOA
After=network.target

[Service]
Type=simple
User=xoa
WorkingDirectory=/opt/xen-orchestra/packages/xo-server/
ExecStart=/usr/bin/scl enable rh-nodejs8 "yarn start"
Restart=on-failure # or always, on-abort, etc

[Install]
WantedBy=multi-user.target" >> /etc/systemd/system/xoa.service

cp /opt/xen-orchestra/packages/xo-server/sample.config.yaml /opt/xen-orchestra/packages/xo-server/.xo-server.yaml

Edit /opt/xen-orchestra/packages/xo-server/.xo-server.yaml and change:

mounts:
    #'/': '/path/to/xo-web/dist/'

to

mounts:
    '/': '../xo-web/dist/'


then start XOA:
systemctl start xoa
systemctl enable xoa

Now the XOA webinterface should be available at port 80 on your server.. (Port can be changed in /opt/xen-orchestra/packages/xo-server/.xo-server.yaml)

Username: admin@admin.net
Pass: admin

Have fun, and report bugs:
https://github.com/MrMEEE/xoa-ansible/issues

In time, I will provide RPM packages as well..

```

Updating

```
scl enable rh-nodejs8 "npm -g install yarn"
scl enable rh-nodejs8 "su xoa -c \"cd /opt/xen-orchestra && git pull --ff-only && yarn && yarn build\""
systemctl restart xoa

```
