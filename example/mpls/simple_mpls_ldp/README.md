# simple_mpls_ldp

## Topology

<https://milestone-of-se.nesuke.com/nw-advanced/mpls-vpn/1-ldp/>

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
