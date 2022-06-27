# mpls_l3vpn

## Topology

![topology](topology.png)

## Host Requirement

```shell
% sudo apt install linux-modules-extra-`uname -r` 
% sudo modprobe mpls_router mpls_iptunnel mpls_gso
% lsmod | grep mpls 
```

## Run

Nsnet command

```shell
% docker run --rm -it -v $PWD:/cue/app -w /cue/app shinch13/cue:0.4.3 cue export config/networks*.yaml config/commands*.yaml --out yaml > net.yaml
% sudo nsnet create 
```

ce1

```shell
% sudo nsnet shell ce1 
root@44cb05a2a797:/# ping -I 10.0.0.1 10.0.1.1
root@44cb05a2a797:/# ping -I 10.0.0.1 10.0.2.1
root@44cb05a2a797:/# ping -I 10.0.0.1 192.168.0.1
```

ce3

```shell
% sudo nsnet shell ce3
root@8ba1d38c5af0:/# ping -I 10.0.0.1 10.0.1.1
```

pe1

```shell
% sudo nsnet shell pe1
root@0a9249058405:/# tcpdump -i any -n -l | grep ICMP 
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes

12:07:59.873219 IP 10.0.0.1 > 10.0.1.1: ICMP echo request, id 19, seq 1, length 64
12:07:59.873239 IP 10.0.0.1 > 10.0.1.1: ICMP echo request, id 19, seq 1, length 64
12:07:59.873254 IP 10.0.1.1 > 10.0.0.1: ICMP echo reply, id 19, seq 1, length 64
12:07:59.873257 IP 10.0.1.1 > 10.0.0.1: ICMP echo reply, id 19, seq 1, length 64

12:12:00.428617 IP 10.0.0.1 > 10.0.2.1: ICMP echo request, id 26, seq 1, length 64
12:12:00.428665 MPLS (label 25, exp 0, ttl 63) (label 80, exp 0, [S], ttl 63) IP 10.0.0.1 > 10.0.2.1: ICMP echo request, id 26, seq 1, length 64
12:12:00.428775 MPLS (label 80, exp 0, [S], ttl 63) IP 10.0.2.1 > 10.0.0.1: ICMP echo reply, id 26, seq 1, length 64
12:12:00.428778 IP 10.0.2.1 > 10.0.0.1: ICMP echo reply, id 26, seq 1, length 64
12:12:00.428781 IP 10.0.2.1 > 10.0.0.1: ICMP echo reply, id 26, seq 1, length 64

12:12:05.169709 IP 10.0.0.1 > 192.168.0.1: ICMP echo request, id 27, seq 1, length 64
12:12:05.169742 MPLS (label 24, exp 0, ttl 63) (label 80, exp 0, [S], ttl 63) IP 10.0.0.1 > 192.168.0.1: ICMP echo request, id 27, seq 1, length 64
12:12:05.169821 MPLS (label 80, exp 0, [S], ttl 63) IP 192.168.0.1 > 10.0.0.1: ICMP echo reply, id 27, seq 1, length 64
12:12:05.169824 IP 192.168.0.1 > 10.0.0.1: ICMP echo reply, id 27, seq 1, length 64
12:12:05.169845 IP 192.168.0.1 > 10.0.0.1: ICMP echo reply, id 27, seq 1, length 64

12:09:51.411966 IP 10.0.0.1 > 10.0.1.1: ICMP echo request, id 22, seq 1, length 64
12:09:51.411986 MPLS (label 25, exp 0, ttl 63) (label 81, exp 0, [S], ttl 63) IP 10.0.0.1 > 10.0.1.1: ICMP echo request, id 22, seq 1, length 64
12:09:51.412026 MPLS (label 81, exp 0, [S], ttl 63) IP 10.0.1.1 > 10.0.0.1: ICMP echo reply, id 22, seq 1, length 64
12:09:51.412028 IP 10.0.1.1 > 10.0.0.1: ICMP echo reply, id 22, seq 1, length 64
12:09:51.412030 IP 10.0.1.1 > 10.0.0.1: ICMP echo reply, id 22, seq 1, length 64
```

## BGP UPDATE Message

![bgp_update_message](bgp_update_message.png)
