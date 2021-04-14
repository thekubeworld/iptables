- [iptables](#iptables)
  * [Kernelspace](#kernelspace)
  * [Userspace](#userspace)
  * [Options](#options)
  * [General info](#general-info)
    + [Command Types](#command-types)
    + [Table Types](#table-types)
    + [Chain Names](#chain-names)
    + [Matches](#MATCHES)
    + [Target and Jump](#TARGET-AND-JUMP)
  * [Examples](#Examples)
    + [List](#list)
      + [List from filter table](#List-from-filter-table)
      + [List from nat table](#List-from-nat-table)
      + [List from raw table](#List-from-raw-table)
      + [List from mangle table](#List-from-mangle-table)
    + [Allow](#allow)
      + [Allow any website OUTGOING traffic on port 443](#Allow-any-website-OUTGOING-traffic-on-port-443)
      + [Allow OUTGOING traffic by multi PORT](#Allow-OUTGOING-traffic-by-multi-PORT)
      + [Allow OUTGOING and INCOMING traffic via loopback interface](#Allow-OUTGOING-and-INCOMING-traffic-via-loopback-interface)
      + [Allow OUTGOING traffic by interface](#Allow-OUTGOING-traffic-by-interface)
      + [Allow OUTGOING ssh connection from the machine](#Allow-OUTGOING-ssh-connection-from-the-machine)
      + [Allow INCOMING traffic by interface](#Allow-INCOMING-traffic-by-interface)
      + [Allow INCOMING traffic using TCP syn flag](#Allow-traffic-using-TCP-syn-flag)
      + [Allow INCOMING traffic using TCP flags](#Allow-INCOMING-traffic-using-TCP-flags)
      + [Allow INCOMING traffic in a specific time](#Allow-INCOMING-traffic-in-a-specific-time)
      + [Allow INCOMING traffic with number of connections per IP address](#Allow-INCOMING-traffic-with-number-of-connections-per-IP-address)
    + [Block](#block)
      + [Block INCOMING traffic to IP Addr](#Block-INCOMING-traffic-to-IP-Addr)
      + [Block INCOMING traffic to a range of IP using iprange](#Block-INCOMING-traffic-to-a-range-of-IP-using-iprange)
      + [Block INCOMING traffic on port 443 EXCEPT on specific IP](#Block-INCOMING-traffic-on-port-443-EXCEPT-on-specific-IP)
      + [Block INCOMING ssh traffic by PORT](#Block-INCOMING-ssh-traffic-by-PORT)
      + [Block INCOMING traffic by MAC Address](#Block-INCOMING-traffic-by-MAC-Address)
      + [Block OUTGOING traffic to a subnet](#Block-OUTGOING-traffic-to-a-subnet)
      + [Block OUTGOING traffic to a site](#Block-OUTGOING-traffic-to-a-site)
      + [Block OUTGOING traffic by address type](#Block-OUTGOING-traffic-by-address-type)
    + [FORWARD](#forward)
      + [Allow FORWARD packets in a specific time](#Allow-FORWARD-packets-in-a-specific-time)
    + [Zero Counters](#zero-counters)
    + [Flush](#flush)
      + [Clean rules in all chains in filter table](#clean-rules-in-all-chains-in-filter-table)
      + [Clean rules in the INPUT chain in filter table](#Clean-rules-in-the-INPUT-chain-in-filter-table)
      + [Clean rules in all chains in nat table](#Clean-rules-in-all-chains-in-nat-table)
    + [Chains](#Chains)
      + [Create a chain](#create-a-chain)
      + [Change default policy for a chain](#CHANGE-DEFAULT-POLICY-FOR-A-CHAIN)
      + [Delete a created chain](#delete-a-created-chain)
      + [Delete a rule in a chain](#DELETE-A-RULE-IN-A-CHAIN)
      + [Insert a rule on TOP of the chain](#Insert-a-rule-on-TOP-of-the-chain)
  * [Conntrack module and Stateful Firewall](#Conntrack-module-and-Stateful-Firewall)
    + [Allow INCOMING traffic state ESTABLISHED RELATED](#Allow-INCOMING-traffic-state-ESTABLISHED-RELATED)
    + [Allow OUTGOING traffic in initialized connections state NEW ESTABLISHED RELATED](#Allow-OUTGOING-traffic-in-initialized-connections-state-NEW-ESTABLISHED-RELATED)
    + [Drop INVALID packets](#Drop-Invalid-packets)
  * [Additional Resources](#Additional-Resources)

# iptables
- The userspace tool requires: `privileged user`
- All commands executed via iptables are stored in memory, if the machine is rebooted the changes are lost.
- `iptables-save` will output the rules in stdout or file
- `iptables-restore` loads rules from a file in memory

To save the current rules running in memory:  

**RedHat**:  
```# service iptables save```

- 0/0 means any network and any mask

## Kernelspace
See `netfilter`

## Userspace
See `iptables` command

## Options
`-j` — Jumps to the specified target when a packet matches a particular rule. Valid targets to use after the -j option include standard options (ACCEPT, DROP, QUEUE, and RETURN) as well as extended options that are available through modules loaded by default with the Red Hat Enterprise Linux iptables RPM package, such as LOG, MARK, and REJECT, among others. Refer to the iptables man page for more information about these and other targets.


## General info

General command is:  
`iptables [-t TABLE] COMMAND CHAIN_NAME MATCHES -k TARGET/JUMP`

### Table Types
**filter**  
This is the default and perhaps the most widely used table. It is used to make decisions about whether a packet should be allowed to reach its destination.
iptables filter table has the following built-in chains: `INPUT, OUTPUT, FORWARD`

**nat**  
The nat table is specialized for `SNAT` and `DNAT` (`Port-Forwarding`)
This table allows you to route packets to different hosts on `NAT` (`Network Address Translation`) networks by changing the source and destination addresses of packets. It is often used to allow access to services that can’t be accessed directly, because they’re on a NAT network.
iptables NAT table has the following built-in chains: `PREROUTING, POSTROUTING and OUTPUT` (for locally generated packets)

**mangle**  
This table allows you to alter packet headers in various ways, such as changing TTL values.
mangle table has the following built-in chains: `PREROUTING, INPUT, FORWARD, OUTPUT, POSTROUTING`

**raw**  
The raw table is only used to set a mark on packets that should not be handled by the connection tracking system. This is done by using `NOTRACK`
target on packet. raw table has the following builtin-chains: `PREROUTING and OUTPUT`.

### Command Types
**-A** - Append the rule to the end of selected chain                                                             
**-I** - Insert one or more rules in the selected chain  on specific position, by default on the top (position 1)  
**-L** - List all rules in the selected chain. If no chain is selected all chains as listed                       
**-F** - Flush (`CLEAN`) the selected chain. If no chain is selected, all chains are `CLEANED`                               
**-D** - Delete one or more rules from the selected chain
**-R** - Replace rule in a selected chain                                                         
**-S** - Show all rules in the selected chain
**-Z** - Zero the packet and byte counters. If no chain is specified all chains counters are clean                
**-N** - Create a new user defined chain by the given name                                                        
**-X** - Delete the user-defined chain specified                                                                  
**-P** - Set the policy for the built-in chain (INPUT, OUTPUT or FORWARD)                                         
**-v** - Verbose output
**-n** - Avoid long reverse DNS lookup. Print IPs instead of domains and service names.

### Chain Names
**INPUT**  - used for filtering `INCOMING PACKETS`. In our linux host is the `packet DESTINATION`  
**OUTPUT** - used for filtering `OUTGOING PACKETS`. In our linux host is the `packet SOURCE` of the packet  
**MASQUERADE** -  
**FORWARD** -  
**PREROUTING** - used for `DNAT/Port Forwarding`  
**POSTROUTING** - used for `SNAT (MASQUERADE)`  
**USER_DEFINED** -  

### Matches
**-s** - source_ip              
**-d** - dest_ip                
**-o** - outgoing_int           
**-p** - protocol               
**--sport** - source port       
**--dport** - destionation port 
**-i** - incoming int           
**-m** - mac                    
**-m** - time                   
**-m** - quota                  
**-m** - limit                  
**-m** - recent                 

### Target and Jump
**ACCEPT** -         
**DROP** -          
**REJECT** -         
**LOG** -            
**SNAT** -           
**DNAT** -           
**LIMIT** -          
**RETURN** -         
**TEE** -            
**TOS** -            
**TTL** -            

## Examples
### List
#### List from filter table
```
# iptables -Lnv INPUT
```

#### List from mangle table
```
iptables -t mangle -Lvn
```

#### List from raw table
```
iptables -t raw -Lvn
```

#### List from nat table
```
iptables -t nat -Lvn
```

### Allow
#### Allow OUTGOING traffic to any website on port 443
```
# iptables -I OUTPUT -p tcp --dport 443 -d 0/0 -j ACCEPT
```

#### Allow OUTGOING traffic by multi PORT
```
# iptables -A OUTGOING -p tcp -m multiport --dports 80,443 -j ACCEPT
```

##### Allow INCOMING traffic by interface
Available for: INPUT, FORWARD and PREROUTING chains

Example 1:
```
# iptables -A INPUT -i wlan1 -j ACCEPT
```

Example 2: Allow `any wlan` interface, like `wlan0`, `wlan1`, `wlan2` etc
```
# iptables -A INPUT -i wlan+ -j ACCEPT
```

#### Allow OUTGOING traffic by interface
```
# iptables -A OUTPUT -o eth0 ACCEPT
```

#### Allow OUTGOING ssh connection from the machine
From the machine I am writing this iptables rule, Allow ssh to any machine via 22 port.
```
# iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT
```

#### Allow OUTGOING and INCOMING traffic via loopback interface
It's always a good practice allow INCOMING and OUTGOING communication via loopback interface.

- `-i` incoming interface
- `-o` outgoing interface

```
# iptables -A INPUT -i lo -j ACCEPT
# iptables -A OUTPUT -o lo -j ACCEPT
```

#### Allow INCOMING traffic using TCP syn flag
```
# iptables -A INPUT -i eth0 -p tcp --syn -s 192.168.10.1 -j ACCEPT
```

#### Allow INCOMING traffic using TCP flags
Usage `--tcp-flags mask comp`

`mask` is the flags which we should examine, written as a comma-separate list
`comp` is a comma-separate list of flags which must be set

FLAGS:

- `SYNC`: Synchronize
- `ACK`: Acknowledgement
- `FIN`: Finalize
- `RST`: Reset
- `URG`: Urgent
- `PSH`: Push
- `ALL`: All tcp flags
- `NONE`: None tcp flags

```
# logging outgoing traffic that has syn and ack set
iptables -A OUTPUT -p tcp --tcp-flags syn,ack,rst,fin syn,ack -j LOG
```

#### Allow INCOMING traffic in a specific time
Options available:
```
# iptables -m time --help

time match options:
    --datestart time     Start and stop time, to be given in ISO 8601
    --datestop time      (YYYY[-MM[-DD[Thh[:mm[:ss]]]]])
    --timestart time     Start and stop daytime (hh:mm[:ss])
    --timestop time      (between 00:00:00 and 23:59:59)
[!] --monthdays value    List of days on which to match, separated by comma
                         (Possible days: 1 to 31; defaults to all)
[!] --weekdays value     List of weekdays on which to match, sep. by comma
                         (Possible days: Mon,Tue,Wed,Thu,Fri,Sat,Sun or 1 to 7
                         Defaults to all weekdays.)
    --kerneltz           Work with the kernel timezone instead of UTC
```

Example:
```
# iptables -A INPUT -p tcp --dport 22 -m time --timestart 10:00 --timestop 15:00 -j ACCEPT
# iptables -A INPUT -p tcp --dport 22 -j DROP
```

#### Allow INCOMING traffic with number of connections per IP address
Useful to avoid **DDoS attacks**

- `--connlimit-upto` **n**: match if the number of existing connections is less than n
- `--connlimit-above` **n**: match if the number of existing connections is greater then n

```
# iptables -A INPUT -p tcp --dport 25 --syn -m conlimit \
        --connlimit-above 5 -j REJECT --reject-with tcp-rst
```

#### Allow INCOMING ICMP traffic using LIMIT MATCH
Allow INCOMING ICMP traffic (echo-request) as 7 packages (doesn't matter how fast it was) and after
that LIMIT 1 packet PER SEC
```
# iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/sec \
          --limit-burst 7 -j ACCEPT
```

### Block
#### Block INCOMING traffic to a range of IP using iprange
Block range from 192.168.1.20 to 192.168.1.25
```

# iptables -I INPUT -p tcp --dport 25 -m iprange --src range 192.168.1.20-192.168.1.25 -j DROP
```
#### Block INCOMING traffic to IP Addr
```
# iptables -I INPUT -s 192.168.1.20 -j DROP
```

#### Block INCOMING ssh traffic by PORT
```
# iptables -A INPUT -p tcp --dport 22 -j DROP
```

#### Block INCOMING traffic by MAC Address
```
# iptables -A INPUT -i wlan0 -m mac --mac-source 08:00:11:22:44:11:22 -j DROP
```

#### Block INCOMING traffic on port 443 EXCEPT on specific IP
Use the `!` operator.

```
# iptables -A INPUT ! -s 192.168.1.50 -p tcp --dport 443 -j DROP
```

#### Block OUTGOING traffic to a subnet
```
# iptables -I OUTPUT -s 192.168.0.0/24 -j DROP
```

#### Block OUTGOING traffic to a site

User can do a single line, like this one:
```
# iptables -I OUTPUT -d www.terra.com.br -j DROP
```

**Or Block** by IPs address manually assigned to the site:
```
$ dig www.terra.com.br
;; ANSWER SECTION:
www.terra.com.br.	60	IN	CNAME	www.terra.com.br.edgesuite.net.
www.terra.com.br.edgesuite.net.	7199 IN	CNAME	a1799.dscb.akamai.net.
a1799.dscb.akamai.net.	19	IN	A	104.98.115.161
a1799.dscb.akamai.net.	19	IN	A	104.98.115.145

# iptables -I OUTPUT -d 104.98.115.161 -j DROP
# iptables -I OUTPUT -d 104.98.115.145 -j DROP
```

However, **both approaches won't work** for sites like **google**, **facebook** can have several IPs address but only list a few of them here. In that case, is required to have a proxy like squid in front of the your subnet.

#### Block OUTGOING traffic by address type
Address type:  
- UNSPEC, UNICAST, LOCAL, BROADCAST, ANYCAST  
- MULTICAST, BLACKHOLE, UNREACHABLE, PROHIBIT  
- THROW, NAT, XRESOLVE

```
# iptables -A OUTPUT -m addrtype --dst-type MULTICAST -j DROP
``` 

### Zero Counters
List data  
```
# iptables -Lvn
```

Zero everything  
```
# iptables -F
```

List data  
`# iptables -Lvn`  

### FORWARD

The FORWARD chain is only applied when the machine is a router.  

Consider FORWARD as packets that are neither emitted by the host
nor directed to the host. They are the packets that the host is merely routing.

#### Allow FORWARD packets in a specific time
Accepting forwarded traffic (this is the router machine) 
to www.uol.com.br on workdays between 18:00-08:00
```
# iptables -A FORWARD -p tcp --dport 80 -d www.uol.com.br -m time \
    --weekdays Mon,Tue,Wed,Thu,Fri --timestart 18:00 --timestop 8:00 -j ACCEPT
```
 
Packets to www.ubuntu.com are dropped between 8:00 - 18:00 (working hours)
```
# iptables -A FORWARD -p tcp --dport 80 -d www.uol.com.br -j DROP
```

### Flush
#### Clean rules in all chains in filter table
Flush all rules in `all chains` in the `filter table`  
```
# iptables -F
# iptables -Lvn
```

#### Clean rules in the INPUT chain in filter table
Flush all rules in `INPUT` chain in the `filter table`  
```
# iptables -F INPUT
# iptables -Lvn
```

#### Clean rules in all chains in nat table
Flush all rules in `INPUT` chain in the `nat table`  
```
# iptables -t nat -F
# iptables -Lvn
```

### Chains
#### Create a Chain
```
# iptables -N MYCHAIN
# iptables -Lvn
```

#### Change Default Policy For a Chain
Example each packet which is not accepted for a rule, will drop.

```
# iptables -P FORWARD DROP
# iptables -Lvn
```


#### Delete a Created Chain
```
# iptables -X MYCHAIN
# iptables -Lvn
```

#### Delete a Rule in a Chain
Delete the rule number 3 in INPUT chain

```
# iptables -D 3 INPUT
# iptables -Lvn
```

### Insert a rule on TOP of the chain
- `-I` will insert in the `TOP` of the chain.  
- On the other hand, `-A` will `APPEND` the rule.

```
# iptables -I INPUT -p tcp --dport -s 192.168.1.10 -j ACCEPT
# iptables -I INPUT -p tcp --dport -j DROP
```
## Conntrack module and Stateful Firewall
The conntrack module, basically is a connection tracking or stateful firrewall.

- NEW: The first packet from a connection
- ESTABLISHED: Packets that are part of an existing connection
- RELATED: Packets that are requesting a new connection and are already part of an existing connection. (EX: FTP)
- INVALID: Packets that are not part of any existing connection
- UNTRACKED: Packets marked within the raw table with the NOTRACK target

Conntrack module can even be used in non tracking protocol like UDP or ICMP

- `-m state --state` *state* where state is comma separed values of packet states

### Allow INCOMING traffic state ESTABLISHED RELATED
```
# iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

### Allow OUTGOING traffic in initialized connections state NEW ESTABLISHED RELATED
```
# iptables -A OUTPUT -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
```

### Drop INVALID packets
```
# iptables -A INPUT -m state --state INVALID -j DROP
# iptables -A OUTPUT -m state --state INVALID -j DROP
```
## Additional Resources
- [Netfilter.org](https://www.netfilter.org/)
- [Linux Security: The Complete Iptables Firewall Guide](https://www.udemy.com/course/linux-security-the-complete-iptables-firewall-guide/)
