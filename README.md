# ipsec_exporter
Ipsec exporter to prometheus. This program creates an endpoint in that exposes
the state of ipsec tunnels.


## Requirements

Docker installed:
```text
yum install docker
```

Python packages:

```text
flask>=1.0.2
prometheus-client>=0.5.0
```

Python versions:
- python2.7
- python3.5
- python3.6

But the tests only work with python3.

## Usage

```bash
docker run -p 9116:9116 --rm --name ipsec_exporter -d \
    -v /etc/ipsec.d/:/etc/ipsec.d/ -v \
    /var/run/pluto/pluto.ctl:/var/run/pluto/pluto.ctl \
    -v /sbin/ipsec:/usr/sbin/ipsec \
    -v /usr/libexec/ipsec/:/usr/libexec/ipsec/ \
    ebrainte/ipsec-exporter:latest
```

This will create an endpoint in the direction http://localhost:9116/metrics.

## Build docker image


``` bash
docker login
docker build -f Dockerfile -t ebrainte/ipsec-exporter:latest .
docker push ebrainte/ipsec-exporter:latest
```

## Automatic deployment

To create a sysvinit script that uses the docker image, there's a playbook. You
must pass the docker registry url, username and password to download the image
from the instance. The credentials will be removed, so you don't have to worry
about that.

``` bash
ansible-playbook -i "$ip," -e ansible_ssh_user=$aws_user -e \
    docker_registry=$docker_registry -e \
    docker_username=$docker_username -e \
    docker_password=$docker_password ansible/main.yml
```

Note the comma after $ip, to use an inventory from the CLI you must use it like
this.
```

## Third party

Based on: https://github.com/paradigmadigital/prometheus-ipsec-exporter/

The script's source used to check if the tunnel is up may be checked in the
[zabbix-ipsec](https://github.com/a-schild/zabbix-ipsec) github repository. It's
a pretty cool option if you use zabbix.
