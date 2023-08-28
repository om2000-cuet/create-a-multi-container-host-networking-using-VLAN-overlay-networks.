# create-a-multi-container-host-networking-using-VLAN-overlay-networks.
<br/><br/>
<img src="https://github.com/faayam/vxlan-docker-hands-on/raw/main/vxlan-diagram.png"/><br/>
<small>Image Courtesy faysalmehedi</small><br/><br/>
VM1 CODE<br/>

ubuntu@ip-172-31-33-25:~$ sudo apt install -y docker.io<br/><br/>

Reading package lists... Done
Building dependency tree... Done
<br/><br/>

ubuntu@ip-172-31-33-25:~$ sudo docker network create --subnet 172.18.0.0/16 vxlan-net
<br/> 

cd84e0f3bd918f326d5fa2aa002124aa99b2f1886e06181b0dfd748cad1cd3dc
<br/><br/>

ubuntu@ip-172-31-33-25:~$ sudo docker network ls
<br/><br/>

NETWORK ID     NAME        DRIVER    SCOPE<br/>
c965c613cd34   bridge      bridge    local<br/>
bba0f17e25e4   host        host      local<br/>
db15f8ca269e   none        null      local<br/>
cd84e0f3bd91   vxlan-net   bridge    local<br/>
<br/>
ubuntu@ip-172-31-33-25:~$ ip a<br/><br/>

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000<br/> link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00<br/>inet 127.0.0.1/8 scope host lo<br/>valid_lft forever preferred_lft forever<br/>inet6 ::1/128 scope host <br/>valid_lft forever preferred_lft forever<br/><br/>
<br/>
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP group default qlen 1000<br/>
    link/ether 02:eb:ee:1e:2f:90 brd ff:ff:ff:ff:ff:ff<br/>
    inet 172.31.33.25/20 metric 100 brd 172.31.47.255 scope global dynamic eth0<br/>
       valid_lft 2646sec preferred_lft 2646sec<br/>
    inet6 fe80::eb:eeff:fe1e:2f90/64 scope link <br/>
       valid_lft forever preferred_lft forever<br/>
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default <br/>
    link/ether 02:42:7c:c0:14:4d brd ff:ff:ff:ff:ff:ff<br/>
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0<br/>
       valid_lft forever preferred_lft forever<br/>
4: br-cd84e0f3bd91: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default <br/>
    link/ether 02:42:e5:1f:3a:48 brd ff:ff:ff:ff:ff:ff<br/>
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-cd84e0f3bd91<br/>
       valid_lft forever preferred_lft forever<br/><br/>

ubuntu@ip-172-31-33-25:~$ sudo docker run -d --net vxlan-net --ip 172.18.0.11 ubuntu sleep 3000<br/>

Digest: sha256:0bced47fffa3361afa981854fcabcd4577cd43cebbb808cea2b1f33a3dd7f508<br/>

ubuntu@ip-172-31-33-25:~$ sudo docker ps<br/>

CONTAINER ID   IMAGE     COMMAND        CREATED          STATUS          PORTS     NAMES<br/>
1c678074f48a   ubuntu    "sleep 3000"   41 seconds ago   Up 40 seconds             musing_lederberg<br/><br/>

ubuntu@ip-172-31-33-25:~$ sudo docker network ls<br/><br/>

NETWORK ID     NAME        DRIVER    SCOPE<br/>
c965c613cd34   bridge      bridge    local<br/>
bba0f17e25e4   host        host      local<br/>
db15f8ca269e   none        null      local<br/>
cd84e0f3bd91   vxlan-net   bridge    local<br/>

ubuntu@ip-172-31-33-25:~$ sudo docker inspect cd84e0f3bd91 | grep IPAddress<br/>

ubuntu@ip-172-31-33-25:~$ sudo docker inspect 1c678074f48a | grep IPAddress<br/>

            "SecondaryIPAddresses": null,<br/>
            "IPAddress": "",<br/>
                    "IPAddress": "172.18.0.11",<br/><br/>
                    
ubuntu@ip-172-31-33-25:~$ sudo docker exec -it 1c678074f48a bash <br/>

root@1c678074f48a:/# apt-get update <br/><br/>

Fetched 25.9 MB in 4s (6070 kB/s)         <br/>                  
Reading package lists... Done<br/><br/>

root@1c678074f48a:/# apt-get install iputils-ping<br/><br/>

root@1c678074f48a:/# ping 172.18.0.12 -c 2<br/><br/>

PING 172.18.0.12 (172.18.0.12) 56(84) bytes of data.<br/>
From 172.18.0.11 icmp_seq=1 Destination Host Unreachable<br/>
From 172.18.0.11 icmp_seq=2 Destination Host Unreachable<br/>

--- 172.18.0.12 ping statistics ---<br/>
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 1023ms<br/>
pipe 2<br/><br/>

root@1c678074f48a:/# sudo apt-get update<br/>

bash: sudo: command not found
<br/>
root@1c678074f48a:/# apt-get update<br/><br/>

Hit:1 http://security.ubuntu.com/ubuntu jammy-security InRelease<br/>
Hit:2 http://archive.ubuntu.com/ubuntu jammy InRelease<br/>
Hit:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease<br/>
Hit:4 http://archive.ubuntu.com/ubuntu jammy-backports InRelease<br/>
Reading package lists... Done<br/>


root@1c678074f48a:/# apt-get install net-tools<br/><br/>

Reading package lists... Done<br/>

root@1c678074f48a:/# apt-get install iputils-ping<br/><br/>

Reading package lists... Done<br/>
Building dependency tree... Done<br/>
Reading state information... Done<br/>


root@1c678074f48a:/# ping 172.18.0.12 -c 2<br/>

PING 172.18.0.12 (172.18.0.12) 56(84) bytes of data.<br/>
From 172.18.0.11 icmp_seq=1 Destination Host Unreachable<br/>
From 172.18.0.11 icmp_seq=2 Destination Host Unreachable<br/>

--- 172.18.0.12 ping statistics ---<br/>
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 1001ms<br/>
pipe 2<br/><br/>

root@1c678074f48a:/# brctl show<br/>

bash: brctl: command not found<br/><br/>

root@1c678074f48a:/# sudo<br/>

bash: sudo: command not found<br/>

root@1c678074f48a:/# ^C<br/>

root@1c678074f48a:/# exit<br/><br/>

exit<br/>

ubuntu@ip-172-31-33-25:~$ brctl show<br/><br/>

bridge name     bridge id               STP enabled     interfaces<br/>

br-cd84e0f3bd91         8000.0242e51f3a48       no              veth297d610<br/>

docker0         8000.02427cc0144d       no<br/><br/>

ubuntu@ip-172-31-33-25:~$ sudo ip link add vxlan-demo type vxlan id 100 remote 10.0.1.41 dstport 4789 dev eth0<br/><br/>

ubuntu@ip-172-31-33-25:~$ ip a | grep vxlan <br/><br/>

7: vxlan-demo: <BROADCAST,MULTICAST> mtu 8951 qdisc noop state DOWN group default qlen 1000<br/><br/>

ubuntu@ip-172-31-33-25:~$ sudo ip link set vxlan-demo up<br/><br/>

ubuntu@ip-172-31-33-25:~$ route -n<br/><br/>

Command 'route' not found, but can be installed with:<br/>

sudo apt install net-tools<br/>

ubuntu@ip-172-31-33-25:~$ sudo brctl addif br-cd84e0f3bd91 vxlan-demo<br/>

ubuntu@ip-172-31-33-25:~$ route -n <br/><br/>

Command 'route' not found, but can be installed with:<br/>
sudo apt install net-tools<br/>

ubuntu@ip-172-31-33-25:~$ sudo apt install net-tools<br/>

Reading package lists... Done<br/>
Building dependency tree... Done<br/>
Reading state information... Done<br/>



<br/>


ubuntu@ip-172-31-33-25:~$ route -n<br/><br/>

Kernel IP routing table<br/>

Destination     Gateway         Genmask         Flags Metric Ref    Use Iface<br/>
0.0.0.0         172.31.32.1     0.0.0.0         UG    100    0        0 eth0<br/>
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0<br/>
172.18.0.0      0.0.0.0         255.255.0.0     U     0      0        0 br-cd84e0f3bd91<br/>
172.31.0.2      172.31.32.1     255.255.255.255 UGH   100    0        0 eth0<br/>
172.31.32.0     0.0.0.0         255.255.240.0   U     100    0        0 eth0<br/>
172.31.32.1     0.0.0.0         255.255.255.255 UH    100    0        0 eth0<br/>
ubuntu@ip-172-31-33-25:~$ 


<br/>
VM2 CODE <br/>


ubuntu@ip-172-31-35-104:~$ sudo apt install -y docker.io<br/><br/>

Reading package lists... Done<br/>

Building dependency tree... Done<br/>

Reading state information... Done<br/>

ubuntu@ip-172-31-35-104:~$ sudo docker network create --subnet 172.18.0.0/16 vxlan-net<br/><br/>

31a798023030f4d148fbdaab07672bf4d157531f80650a48bcf90a67360f1296<br/><br/>

ubuntu@ip-172-31-35-104:~$ sudo docker network ls<br/><br/>

NETWORK ID     NAME        DRIVER    SCOPE<br/>
3ab0f76a8360   bridge      bridge    local<br/>
0189e04278b0   host        host      local<br/>
96a2a267f8cd   none        null      local<br/>
31a798023030   vxlan-net   bridge    local<br/>
<br/>
ubuntu@ip-172-31-35-104:~$ ip a<br/><br/>

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000<br/>
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00<br/>
    inet 127.0.0.1/8 scope host lo<br/>
       valid_lft forever preferred_lft forever<br/>
    inet6 ::1/128 scope host <br/>
       valid_lft forever preferred_lft forever<br/>
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP group default qlen 1000<br/>
    link/ether 02:3b:98:e4:ab:3a brd ff:ff:ff:ff:ff:ff<br/>
    inet 172.31.35.104/20 metric 100 brd 172.31.47.255 scope global dynamic eth0<br/>
       valid_lft 2332sec preferred_lft 2332sec<br/>
    inet6 fe80::3b:98ff:fee4:ab3a/64 scope link <br/>
       valid_lft forever preferred_lft forever<br/>
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default <br/>
    link/ether 02:42:6f:d3:d5:34 brd ff:ff:ff:ff:ff:ff<br/>
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0<br/>
       valid_lft forever preferred_lft forever<br/>
4: br-31a798023030: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default <br/>
    link/ether 02:42:41:38:8b:85 brd ff:ff:ff:ff:ff:ff<br/>
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-31a798023030<br/>
       valid_lft forever preferred_lft forever<br/>
       
ubuntu@ip-172-31-35-104:~$ sudo docker run -d --net vxlan --ip 172.18.0.12 ubuntu sleep 3000<br/><br/>

Unable to find image 'ubuntu:latest' locally<br/>
latest: Pulling from library/ubuntu
3153aa388d02: Pull complete                                                    Digest: sha256:0bced47fffa3361afa981854fcabcd4577cd43cebbb808cea2b1f33a3dd7f508<br/>
Status: Downloaded newer image for ubuntu:latest<br/>
80884fe024f285231096dfef386381c77221adc0d79b3b600b1d646f6157a070<br/>
docker: Error response from daemon: network vxlan not found.<br/><br/>

ubuntu@ip-172-31-35-104:~$ sudo docker ps<br/>

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES<br/>

ubuntu@ip-172-31-35-104:~$ sudo docker run -d --net vxlan --ip 172.18.0.12 ubunsudo docker run -d --net vxsudo docker run -d --net vxlan --ip 172.18.0.12 ubuntu sleep 3000<br/>
sudo docker run -d --net vxlan --ip 172.18.0.12 ubuntu sleep 3000<br/>
sudo docker run -d --net vxlan --ip 172.18.0.12 ubuntu sleep 3000<br/>
sudo docker network create --subnet 172.18.0.0/16 vxsudo docker run -d --net vxsudo docker run -d --net vxlan-net --ip 172.18.0.12 ubuntu sleep 3000<br/>

d37b0203a377cdb96f6305942fff3660907cd444c845dc077261a6942215fcac<br/><br/>

ubuntu@ip-172-31-35-104:~$ sudo docker ps<br/><br/>

CONTAINER ID   IMAGE     COMMAND        CREATED         STATUS         PORTS     NAMES<br/>
d37b0203a377   ubuntu    "sleep 3000"   5 seconds ago   Up 4 seconds             flamboyant_nightingale<br/><br/>
ubuntu@ip-172-31-35-104:~$ ^C<br/>
ubuntu@ip-172-31-35-104:~$ sudo docker inspect d37b0203a377 | grep IPAddress<br/><br/>

            
ubuntu@ip-172-31-35-104:~$  ping 172.18.0.1 -c 2<br/><br/>

PING 172.18.0.1 (172.18.0.1) 56(84) bytes of data.<br/>
64 bytes from 172.18.0.1: icmp_seq=1 ttl=64 time=0.062 ms<br/>
64 bytes from 172.18.0.1: icmp_seq=2 ttl=64 time=0.047 ms<br/>

--- 172.18.0.1 ping statistics ---<br/>

2 packets transmitted, 2 received, 0% packet loss, time 1023ms<br/>

rtt min/avg/max/mdev = 0.047/0.054/0.062/0.007 ms<br/><br/>

ubuntu@ip-172-31-35-104:~$ apt-get update<br/>

Reading package lists... Done<br/>

ubuntu@ip-172-31-35-104:~$ sudo docker exet -it d37b0203a377 bash<br/><br/>
 

ubuntu@ip-172-31-35-104:~$ sudo docker exec -it d37b0203a377 bash<br/>
root@d37b0203a377:/# apt-get update<br/>
 
root@d37b0203a377:/# apt-get install net-tools<br/>
Reading package lists... Done<br/>
Building dependency tree... Done<br/>
 
root@d37b0203a377:/# apt-get install iputils-ping<br/>
Reading package lists... Done<br/>
Building dependency tree... Done<br/>

root@d37b0203a377:/# ping 172.18.0.11 -c 2<br/><br/>
PING 172.18.0.11 (172.18.0.11) 56(84) bytes of data.<br/>
From 172.18.0.12 icmp_seq=1 Destination Host Unreachable<br/>
From 172.18.0.12 icmp_seq=2 Destination Host Unreachable<br/>

 
root@d37b0203a377:/# apt-get install iputils-ping<br/><br/>
Reading package lists... Done<br/>
Building dependency tree... Done<br/>
 
 
root@d37b0203a377:/# ping 172.18.0.11 -c 2<br/>
PING 172.18.0.11 (172.18.0.11) 56(84) bytes of data.<br/>
From 172.18.0.12 icmp_seq=1 Destination Host Unreachable<br/>
From 172.18.0.12 icmp_seq=2 Destination Host Unreachable<br/>

 <br/>
root@d37b0203a377:/# brctl show<br/>
bash: brctl: command not found<br/>
root@d37b0203a377:/# exit<br/><br/>
exit<br/>
ubuntu@ip-172-31-35-104:~$ brctl show<br/>

bridge name     bridge id               STP enabled     interfaces<br/>

br-31a798023030         8000.024241388b85       no              veth5a974cb<br/>

docker0         8000.02426fd3d534       no<br/><br/>

ubuntu@ip-172-31-35-104:~$ sudo ip link add vxlan-demo type vxlan id 100 remote 10.0.1.4 dstport 4789 dev eth0<br/>

ubuntu@ip-172-31-35-104:~$ sudo ip link set vxlan-demo up<br/>
ubuntu@ip-172-31-35-104:~$ sudo brctl addif br-31a798023030 vxlan-demo<br/>
ubuntu@ip-172-31-35-104:~$ route -n<br/>
Command 'route' not found, but can be installed with:<br/>
sudo apt install net-tools<br/>
ubuntu@ip-172-31-35-104:~$ sudo apt install net-tools<br/><br/>
Reading package lists... Done<br/>
 
 <br/>
ubuntu@ip-172-31-35-104:~$ route -n<br/><br/>
Kernel IP routing table<br/>
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface<br/>
0.0.0.0         172.31.32.1     0.0.0.0         UG    100    0        0 eth0<br/>
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0<br/>
172.18.0.0      0.0.0.0         255.255.0.0     U     0      0        0 br-31a798023030<br/>
172.31.0.2      172.31.32.1     255.255.255.255 UGH   100    0        0 eth0<br/>
172.31.32.0     0.0.0.0         255.255.240.0   U     100    0        0 eth0<br/>
172.31.32.1     0.0.0.0         255.255.255.255 UH    100    0        0 eth0<br/><br/>
ubuntu@ip-172-31-35-104:~$ sudo docker exec -it d37b0203a377 bash<br/><br/>
root@d37b0203a377:/# ping 172.18.0.11 -c 2<br/><br/>
PING 172.18.0.11 (172.18.0.11) 56(84) bytes of data.<br/>
From 172.18.0.12 icmp_seq=1 Destination Host Unreachable<br/>
From 172.18.0.12 icmp_seq=2 Destination Host Unreachable<br/>

--- 172.18.0.11 ping statistics ---<br/>
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 1019ms<br/>
pipe 2<br/>
root@d37b0203a377:/# ubuntu@ip-172-31-35-104:~$ <br/>

