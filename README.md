# freeswitch-config (Production Testing)

Freeswitch configuration for a development box testing the production server over VPN

## Version Control

Make sure you're on the production_testing_branch.

```shell
sudo -u freeswitch git checkout production_testing
```

This branch is for testing the production FreeSwitch server from your local FreeSwitch development box. It is set up to simulate incoming and outgoing calls over the VPN from an operator. SIP is set up to use an SDP of `27.109.112.25`. This means that you need to set up a NAT rule to change the source address of all packets going out to the production FreeSwitch server. You also need to setup a NAT rule to change the destination address of all packets with the destination of the SDP back to your local IP address. In addition you need to double check the public IP address of your development machine on *both* the FreeSwitch production server and your local development maching in `/etc/ipsec.conf`. See below for more details.

### Network Setup

Follow these instructions every time since your local ip address is likely to change.

Todo: Write a script to do this automatically

1. Check your local IP address and note it down. `ifconfig`
2. Check your IP tables. `sudo iptables -t nat -L`
3. Check that your local IP address matches the IP table rule for changing the destination of packets with the destination address `27.109.112.25`
4. Modify the rule if required. `sudo iptables -t nat -D PREROUTING -d 27.109.112.25/32 -j NETMAP --to your_old_local_ip/32` and `sudo iptables -t nat -A PREROUTING -d 27.109.112.25/32 -j NETMAP --to your_new_local_ip/32`
5. Create NAT rule to change source IP for all packets destined for the FreeSwitch production server if required. This should only need to be done once. `sudo iptables -t nat -A POSTROUTING -d 54.251.107.12/32 -j SNAT --to-source 27.109.112.25`
6. Persist your iptables so they exist after reboot. `sudo apt-get install iptables-persistent` and `sudo sh -c "iptables-save > /etc/iptables/rules.v4"`
7. Verify the public IP of your development box in `/etc/ipsec.conf` on *both* the FreeSwitch production server and on your development box. Need to check in `/etc/ipsec.conf` and `/etc/ipsec.secrets`
8. Bring up the VPN connection `sudo /etc/init.d/ipsec restart` and `sudo ipsec auto --up freeswitch`
9. Verify it works by pinging. From the FreeSwitch production server: `ping 27.109.112.25`. From your development box `ping 54.251.107.12`

### Simulating Calls

Sign in to your dev-box FreeSwith as `855977100860@local_ip` using a SIP client such as QuteCom or make a new profile under `directory/default`. The sign-in name is important as it will simulate the caller_id for the calling party.

#### Incoming

Placing a call to 2442 it will bridge to the production FreeSwitch box over the VPN simulating an incoming call from the operator.

#### Outgoing

Placing a call to 2443 will simulate an outgoing call to whoever is in `dialplan/public/00_test_vpn_sip.xml` on the FreeSwitch production server over the VPN.

Placing a call to 2444 will simulate an outgoing call to whoever is in `$${test_number}` on the FreeSwitch production server though the external SIP provider. See `passwords.xml` for configuration.

## Ip Addresses

### External IP

??? (Look it up each time)

### Natted SIP IP

27.109.112.25
