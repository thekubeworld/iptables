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
    + [Block](#block)
      + [Block INCOMING traffic to IP Addr](#Block-INCOMING-traffic-to-IP-Addr)
      + [Block INCOMING traffic to a range of IP using iprange](#Block-INCOMING-traffic-to-a-range-of-IP-using-iprange)
      + [Block OUTGOING traffic to a subnet](#Block-OUTGOING-traffic-to-a-subnet)
      + [Block OUTGOING traffic to a site](#Block-OUTGOING-traffic-to-a-site)
    + [Zero Counters](#zero-counters)
    + [Flush](#flush)
      + [Clean rules in all chains in filter table](#clean-rules-in-all-chains-in-filter-table)
      + [Clean rules in the INPUT chain in filter table](#Clean-rules-in-the-INPUT-chain-in-filter-table)
      + [Clean rules in all chains in nat table](#Clean-rules-in-all-chains-in-nat-table)
    + [Output](#output)
      + [Allow OUTGOING ssh connection from the machine](#Allow-OUTGOING-ssh-connection-from-the-machine)
    + [Chains](#Chains)
      + [Create a chain](#create-a-chain)
      + [Change default policy for a chain](#CHANGE-DEFAULT-POLICY-FOR-A-CHAIN)
      + [Delete a created chain](#delete-a-created-chain)
      + [Delete a rule in a chain](#DELETE-A-RULE-IN-A-CHAIN)
      + [Insert a rule on TOP of the chain](#Insert-a-rule-on-TOP-of-the-chain)
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
iptables -Lnv INPUT
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

### Block
##### Block INCOMING traffic to a range of IP using iprange
Block range from 192.168.1.20 to 192.168.1.25
```

# iptables -I INPUT -p tcp --dport 25 -m iprange --src range 192.168.1.20-192.168.1.25 -j DROP
```
##### Block INCOMING traffic to IP Addr
```
# iptables -I INPUT -s 192.168.1.20 -j DROP
```

##### Block OUTGOING traffic to a subnet
```
# iptables -I OUTPUT -s 192.168.0.0/24 -j DROP
```

##### Block OUTGOING traffic to a site

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

$ iptables -I OUTPUT -d 104.98.115.161 -j DROP
$ iptables -I OUTPUT -d 104.98.115.145 -j DROP
```

However, **both approaches won't work** for sites like **google**, **facebook** can have several IPs address but only list a few of them here. In that case, is required to have a proxy like squid in front of the your subnet.

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

### Output
#### Allow OUTGOING ssh connection from the machine
From the machine I am writing this iptables rule, Allow ssh to any machine via 22 port.
```
# iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT
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

## Additional Resources
[Linux Security: The Complete Iptables Firewall Guide](https://www.udemy.com/course/linux-security-the-complete-iptables-firewall-guide/)
