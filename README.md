# ELK Compose

Elasticsearch + Logstash + Kibana on Docker Compose.


## Provisioning

Run containers on Docker Compose.
This may take a few minutes.

```sh
# Wildcard DNS
echo 'REVERSE_PROXY_DOMAIN_NAME=192.168.1.2.xip.io' > .env

# Custom domain
echo 'REVERSE_PROXY_DOMAIN_NAME=example.com' > .env

# Kernel parameter for Elasticsearch
sudo sysctl -w vm.max_map_count=262144

docker-compose build
docker-compose up -d
```


### CoreOS

Enough swap space and Docker Compose are required.

```sh
#!/bin/bash -xe
cat > /etc/systemd/system/swap.service <<EOF
[Service]
Type=oneshot
ExecStartPre=-/usr/bin/rm -f /swapfile
ExecStartPre=/usr/bin/touch /swapfile
ExecStartPre=/usr/bin/fallocate -l 4G /swapfile
ExecStartPre=/usr/bin/chmod 600 /swapfile
ExecStartPre=/usr/sbin/mkswap /swapfile
ExecStartPre=/usr/sbin/sysctl vm.swappiness=10
ExecStart=/sbin/swapon /swapfile
ExecStop=/sbin/swapoff /swapfile
ExecStopPost=-/usr/bin/rm -f /swapfile
RemainAfterExit=true
[Install]
WantedBy=multi-user.target
EOF
systemctl enable --now /etc/systemd/system/swap.service
mkdir -p /opt/bin
curl -L -o /opt/bin/docker-compose https://github.com/docker/compose/releases/download/1.12.0/docker-compose-Linux-x86_64
chmod +x /opt/bin/docker-compose
```


## Setup

Open http://kibana.example.com (replace to your domain name).
