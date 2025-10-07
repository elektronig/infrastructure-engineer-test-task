

##### Test Lab Setup Documentation
GCP: us-central1 (Free Tier region)

2 VM instances:
**app-server** (Docker host)
OS: Rocky Linux 8
Specs: e2-small machine (2 vCPUs, 2 GB Memory) 
20GB boot disk
10GB app data disk (separate disk for \app)
Primary internal IP address 10.128.0.5
External static IP address 104.197.135.180
Hostname: app-server.ipa.test

**ldap-server** (FreeIPA)
OS: Rocky Linux 8
Specs: e2-small machine (2 vCPUs, 2 GB Memory) 
20GB boot disk
Primary internal IP address 10.128.0.7
External static IP address 34.121.70.125 *
Hostname: server.ipa.test

**Firewall сonfiguration**
allow ingress: 
SSH (TCP 22), RDP (TCP 3389) - for both VMs
HTTP (TCP 80) - **app-server*** *
HTTPS (TCP 443 for FreeIPA Web UI) - **ldap-server** *
\* for test purposes only. In this case **app-server** must work like a bastion host with a public IP and internal access to ldap-server. Access to **app-server** must be restricted using IP white list.

## **Access information**
http://104.197.135.180/ nginx running in Docker container on **app-server** as an example

https://34.121.70.125/ipa/ui FreeIPA Web UI access just in case
Please add the new entry to your hosts file 
```
34.121.70.125 server.ipa.test 
```

IPA admin:
admin
```
TestLab_Admin4045!
```

Test users credentials:
user1
```
Testpass1*
```

user2
```
Testpass22+
```

user3
```
Testpass33)
```

## **Setting up the environment**

1. Create new project in GCP and set it as a default

![](data:image/png;base64,/9j/4AAQSkZJRgABAQEAkACQAAD/2wBDAAoHBwkHBgoJCAkLCwoMDxkQDw4ODx4WFxIZJCAmJSMgIyIoLTkwKCo2KyIjMkQyNjs9QEBAJjBGS0U+Sjk/QD3/2wBDAQsLCw8NDx0QEB09KSMpPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT3/wAARCADZA3gDASIAAhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEAAwEBAQEBAQEBAQAAAAAAAAECAwQFBgcICQoL/8QAtREAAgECBAQDBAcFBAQAAQJ3AAECAxEEBSExBhJBUQdhcRMiMoEIFEKRobHBCSMzUvAVYnLRChYkNOEl8RcYGRomJygpKjU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6goOEhYaHiImKkpOUlZaXmJmaoqOkpaanqKmqsrO0tba3uLm6wsPExcbHyMnK0tPU1dbX2Nna4uPk5ebn6Onq8vP09fb3+Pn6/9oADAMBAAIRAxEAPwD2aiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKAMnxR9v8A+Eavv7J3/bPL/d+X9732++M4965zw1Y+G7q9t7jw/qNxb30TZuoXncyzDHzLIjn1PUD6V1esy6hBpc0ukwxT3aYZIpOjjPIzkc4zj3rktVul8T3mm/YNF1C31OG5jka5ntTF9nQHLgueuRkYGc0Abl74mlXUprHSdKuNTmtsfaDHIkaRkjIXcxGWxzgVla5rcWr6Zot1aedF/wATeGKWJxteNgSGRhVOTToNH17VDqtvrLQ3c5uILiwkuNhyBlGWI8MCOCRyO9Ok0pzpGmSWekXtsZdZiuJI5pHml2g48x85K8AZB6UAb974mlXUprHSdKuNTmtsfaDHIkaRkjIXcxGWxzgUv/CX6eNC/tMpcD975H2bZ++87OPL2/3s+9c7Jp0Gj69qh1W31lobuc3EFxYSXGw5AyjLEeGBHBI5HepDpEsej2eqadpF3DJb6h9te0nnaWaZcFSx3E4fBztz29aAN6y8R3UmoR2eo6JeWDzhjAzMkiPgZwSpO049ao+GvEGq6jrmqW15p1wkMVxtVmeLFuNinadpySTzkZ61oWfiVtS1GG3sdMvjEQTPPcQtAsPt8w+Zs9h+dUtGkm07xbrNrcWV3tvbhZ4Z1hLRFfLAOX6A5XofUUAbmq6g2m2fnR2dzeSFgiQ265Yk+pPAHqTVHTPEMl1qR07UdNn068MZljSR1dZEBwdrKSMjIyPeq3jNbxrWy8lbx7EXI+3JZ7vNaLB6bfmxnGcc4rF0eyi/4TewuNJ03UodNS2mUzXPm7WY7egkJK/XjP4UAdR4g8Q2/hy2t57qKaRJ5xCBEu5skEjjv0qpb+KZV1C3ttV0i701bptlvLK6Ort1CttJ2sewNU/iBLLBBostvB58qanEyxZxvOG4+tR6pfSeK59PsLHT7+JY7qO5uZrm3aFYQhzty2MseBxmgC9B4ua+1Oey0/Sbu5a2uTBcSAqqRgHG7JPPrgc0j+LZpppjpOi3mo2tu5jluInRFLD7wQMQXx0470vhO0nt3137RDLEJdSldC6Fd6kDDDPUe9Z2i6rJ4U0xtI1DTdRkmtnfyHtrZpVuVLEghlGAeeQcUAW73V9L1ZvDt4rXTrPd7YPLfZtfa2RIPbBGPX2qxc+KZmvri30nR7rUltG2XEsToio3Uqu4jcR3ArnF0+60/wD4Rs3yCK4utZkuXiBz5W9XO38P55oh0630O+1G31a211vNuZJ7eaxluDHMrnIXEbYDjkcgfWgDqH8W6eugw6ogmkSdxFHAqfvWkzjy9v8AeyD+VMtPEl294LS/0O8sp5UZ4Mukiy7RnbuUkKfrWK+kTWmi6VqNhpFxE9neNeS2LzGWV1YFWOW6vghsfUda3rLxI2p6jFb2WmX3kbS09xcQtAsXooDDLHPYdPWgCh4R17U9VutQjvrC4SOO6kVZXaPEQGMRkKckjPXke9dVXL+GpZrHWNW065srtHmvZLmObySYWRgCPn6Z9q6igAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAx/Fd1NZ+GL+e3do5VjwrqcEZIHB9ea8itZNWvpGW0kvZ3X5iI2ZiOcZ49zXrHjX/kUNQ/3F/9CFcH8P8AVrPSdQunvr028ckYUKVyrnPUntjP6mvay98mGnOMbu/+R5mLXNXjFuysbOqa34v8PoiXEVtcxpEGa4jidlHOPmbgZ/xq/wCCvF154gu7i2vY4g0aeYrRgjvgg8+9R69cLo/hq903Udcknvp0aSMmP5mUnAXHoSD37n0rE+F3/Ibu/wDr3/8AZhSdOE8NObirrZpWv5jU5RrxipOz6HqFFFFeMekYPiq81C0hshpcgWeW424IBDjaTt59cVFDr7X2q6QbeQpb3EUpmjIGQygcHuMHNaWqafLfXGnyRMgFtcCV9xPIwRxx15rOfwww8UDUIZFWzdH82Ekgh2XBZeMc8ZrspypciUt7P9f6RzTVTmutrosR+J7eRon+y3a2k0nlR3TIPLY5wO+QCehIouPFVja2Et3Kk4WK4NuybRv3Drxnpjn6VlWPhGW0kgiay0l44pMm5ZWMrqOny4wG981el8MtP4hmupJENhKhYw8580rsJ6YxtqnDDqW+n9fmJSrNbGrDqcNxqUllEHZ44llZwBtAboM568ZqlceJY4ry5tobC+uZLXHm+TGpABGc5JGfp14NJ4a0WfR7aYXkqTXErjLrnGxQFUcj0FZ1v/ao1/W/7MFoymVARcFl2nYMEYBz9KiNOm5SS1SX+RTnPlV9GzSm8T2iLZmCG5uWvELwrCgJOMZByRg8/oaSXxLGkkkcWn307QoG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jcKKKKACiuOv9WmTUdXWTX1sfspHkQssZ3fID0I3Hn0rXi191tLBZrOeXULmEStbwqMqMck7iABn1NdEsNNJPv8A5XMVWi21/XY2qKxz4ltBYJcrFcNI8pgW2CfvfMHVcZxx9cUp8RQx2s01zaXlu8TiPyZI/mkY9AuCQ2frUexqdivaw7mvRWSniGELdi5tbm2ltYfPaKQLuZPVSCQenrUVv4nhuJoE+xXsa3KM1u7ooEuBnA54JHTOKPY1Owe1h3NuisXw7rU+rx3BuLSSExyuoYqApAOAPvE7h37VtVM4OEuVlRkpK6CiiioKCiiigAornfEd3Pb3tqst1cWWmsjGS4gTJD54BODtFMudbutMsNO8t/7T8+YIbiJFw6kngfMBu/Tg0AdLRWXd601pz/Zt9IqxiRyqLhB6csMkdwM1HceJLaKS2jhgubp7qLzolhQHcv4kY/GgDYorIXxDHJqLWUVnePLGV80hBtjDAHJOff8AQ0weJ7Y4k+zXX2My+ULvaPLznGeucZ4zigDaorDv/EaRfbY7a1u5vsylZJ4owUjbHucnHfANXdEuJbvRLKedt8skKszYxkkUAX6KKKACiiigAoorltX1u8t9aZ7Z8WFgYxdrtB3bz6+wwaAOporD1bULi31a2igl2xPazyEAA5KgFTV3RLiW70SynnbfLJCrM2MZJFAF+iiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKAOdufC5u5tTmdokmnlSW1mX70TKoAzx6jp6VHqXh651C5tL6e30+5uUh8qeCYt5Z5zuVsZBz6jvXTUVusTUVv66WMnRgzml8O3UFlaSWq2MF9bTtMI41ZYWDDBUnr0747dKfqGjalrenOmoGxEqTLLBEoZo+OocnBIOT0HFdFRR9Yne/UPYxtbocxbeG5ltdQX7Hplm9xbmGMW+48kclmIHGccYq++kTs+ikPHiw/1vJ5+Tbxx6+uK2KKUsRNu7/rSwKjFf18zI0XT7zTZbuKYwNa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)

```
gcloud config set project iron-envelope-466516-p5
```

Enable Compute Engine API

```
gcloud services enable compute.googleapis.com
```

Set default region us-central1 (Free Tier region) and zone

```
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-a
```

LDAP Server 
```
gcloud compute instances create ldap-server \
  --zone=us-central1-a \
  --machine-type=e2-small \
  --image-family=rocky-linux-8 \
  --image-project=rocky-linux-cloud \
  --boot-disk-size=20GB \
  --tags=freeipa \
  --no-address \
  --hostname=server.ipa.test
  --metadata=enable-oslogin=true
```
To avoid potential issues with FreeIPA configuration, I created a VM instance with a custom fully qualified domain hostname *server.ipa.test*

Reserved IP:
```
gcloud compute addresses create ldap-ip \
  --region=us-central1
```
Assign it temporarily:
```
gcloud compute instances add-access-config ldap-server \
  --zone=us-central1-a \
  --address=34.121.70.125 
```

App server 

```
gcloud compute instances create app-server \
  --zone=us-central1-a \
  --machine-type=e2-small \
  --image-family=rocky-linux-8 \
  --image-project=rocky-linux-cloud \
  --boot-disk-size=20GB \
  --address=104.197.135.180 \
  --tags=app-server \
  --metadata=enable-oslogin=true
```

Create a 10GB persistent disk for /app directory on application server (we can adjust the size later):
```
gcloud compute disks create app-data-disk \
  --size=10GB \
  --type=pd-standard \
  --zone=us-central1-a
```

And attach the disk to the **app-server** VM:
```
gcloud compute instances attach-disk app-server \
  --disk=app-data-disk \
  --zone=us-central1-a
```

Update the OS on both VMs and reboot the system
```
sudo dnf update -y
sudo reboot
```
Create a Physical Volume
```
sudo dnf install lvm2
sudo pvcreate /dev/sdb
```
Create a Volume Group
```
sudo vgcreate appvg /dev/sdb
```
Create a Logical Volume
```
sudo lvcreate -n applv -L 9G appvg
```
Left ~1 GB unallocated for metadata or snapshots if needed.

Create Filesystem, Mount to /app, and make persistent
Format the logical volume, use xfs (default on Rocky)
```
sudo mkfs.xfs /dev/appvg/applv
```
Create mount point
```
sudo mkdir /app
```
Add to /etc/fstab for persistence
```
echo '/dev/appvg/applv /app xfs defaults 0 0' | sudo tee -a /etc/fstab
```
Mount it now
```
sudo mount -a
```

Install and Configure Docker to Use /app
Install Docker
```
!sudo dnf install -y dnf-utils device-mapper-persistent-data lvm2
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf install -y docker-ce
systemctl enable --now docker
!systemctl start docker
```
Move Docker’s data directory to /app

```
sudo systemctl stop docker
sudo mv /var/lib/docker /app/docker
```
Create symlink
```
sudo ln -s /app/docker /var/lib/docker
```
and start Docker
```
sudo systemctl start docker 
```
Enable Docker on Boot 
```
sudo systemctl enable docker
```
Create a Simple "Hello World" HTML Page for nginx
```
echo '<!DOCTYPE html><html><body><h1>Hello, Eqvilent!</h1></body></html>' | sudo tee /app/index.html
-- mkdir /app/web
-- tee /app/web/index.html
```
Run NGINX in Docker with /app as the web root , restart each time after reboot
```
sudo docker run -d \
  --name hello-nginx \
  --restart unless-stopped \
  -p 80:80 \
  -v /app/web:/usr/share/nginx/html:ro \
  nginx
```

## FreeIPA Server Setup

On **app-server** added to /etc/hosts
```
10.128.0.7 server.ipa.test
```
Install FreeIPA
```
sudo dnf module enable idm:DL1 -y
sudo dnf install ipa-server -y
sudo ipa-server-install --setup-dns
```
To authenticate as the admin and acquire Kerberos ticket
```
kinit admin
```
##### LDAP Users + SSH Access + Controlled /app permissions via FreeIPA
Create LDAP Groups in FreeIPA - one with read-only access and other with write access
```
ipa group-add docker-read --desc="Read-only access to /app"
ipa group-add docker-write --desc="Write access to /app"
```
Create LDAP Users
```
ipa user-add user1 --first=User --last=One --password
ipa user-add user2 --first=User --last=Two --password
ipa user-add user3 --first=User --last=Three --password
```
Assign Users to Groups according to the task given
```
ipa group-add-member docker-read --users=user1
ipa group-add-member docker-read --users=user3
ipa group-add-member docker-write --users=user3
ipa group-add-member docker-write --users=user2
```
Confirm IPA Group Resolution on **app-server**
SSH into **app-server** and verify SSSD can see the groups:
```
getent group docker-read
getent group docker-write
```
Set Permissions on /app
```
sudo chown root:docker-write /app/web
sudo chmod 2775 /app/web  # chmod 2775
```
Configure ACLs: 
```
sudo setfacl -m g:docker-read:rx /app
--sudo setfacl -m g:docker-write:rwx /app
?sudo setfacl -m g:docker-write:w+x /app
```
Enable SSH Access for IPA Users
Authselect profile (enables home dir creation and SSSD):
```
authselect select sssd with-mkhomedir --force
```
!Check if SSHD config: /etc/ssh/sshd_config contains:
```
UsePAM yes
AuthorizedKeysCommand /usr/bin/sss_ssh_authorizedkeys
AuthorizedKeysCommandUser nobody
```
Then restart SSHD: (if changes have been made)
```
systemctl restart sshd

```

