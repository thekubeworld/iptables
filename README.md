- [iptables](#iptables)
  * [Kernelspace](#kernelspace)
  * [Userspace](#userspace)
  * [Options](#options)
  * [General info](#general-info)
    + [COMMAND TYPES](#command-types)
    + [TABLE TYPES](#table-types)
    + [CHAIN NAMES](#chain-names)
    + [MATCHES](#MATCHES)
    + [TARGET AND JUMP](#TARGET-AND-JUMP)
  * [Examples](#Examples)
    + [Zero Counters](#zero-counters)
    + [Flush](#flush)
      + [Clean rules in all chains in filter table](#clean-rules-in-all-chains-in-filter-table)
      + [Clean rules in the INPUT chain in filter table](#Clean-rules-in-the-INPUT-chain-in-filter-table)
      + [Clean rules in all chains in nat table](#Clean-rules-in-all-chains-in-nat-table)
    + [OUTPUT](#output)
      + [Allow OUTGOING ssh connection from the machine](#Allow-OUTGOING-ssh-connection-from-the-machine)
# iptables
Requirements: `root user`

## Kernelspace
See `netfilter`

## Userspace
See `iptables` command

## Options
`-j` — Jumps to the specified target when a packet matches a particular rule. Valid targets to use after the -j option include standard options (ACCEPT, DROP, QUEUE, and RETURN) as well as extended options that are available through modules loaded by default with the Red Hat Enterprise Linux iptables RPM package, such as LOG, MARK, and REJECT, among others. Refer to the iptables man page for more information about these and other targets.


## General info

General command is:  
`iptables [-t TABLE] COMMAND CHAIN_NAME MATCHES -k TARGET/JUMP`

### TABLE TYPES
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

### COMMAND TYPES                                                                                               
**-A** - Append the rule to the end of selected chain                                                             
**-I** - Insert one or more rules in the selected chain  on specific position, by default on the top (position 1)  
**-L** - List all rules in the selected chain. If no chain is selected all chains as listed                       
**-F** - Flush (`CLEAN`) the selected chain. If no chain is selected, all chains are `CLEANED`                               
**-D** - Delete rule                                                                                              
**-R** - Replace rule                                                                                             
**-S** - Show                                                                                                     
**-Z** - Zero the packet and byte counters. If no chain is specified all chains counters are clean                
**-N** - Create a new user defined chain by the given name                                                        
**-X** - Delete the user-defined chain specified                                                                  
**-P** - Set the policy for the built-in chain (INPUT, OUTPUT or FORWARD)                                         

### CHAIN NAMES 
**INPUT**  - used for filtering `INCOMING PACKETS`. In our linux host is the `packet DESTINATION`  
**OUTPUT** - used for filtering `OUTGOING PACKETS`. In our linux host is the `packet SOURCE` of the packet  
**MASQUERADE** -  
**FORWARD** -  
**PREROUTING** - used for `DNAT/Port Forwarding`  
**POSTROUTING** - used for `SNAT (MASQUERADE)`  
**USER_DEFINED** -  

### MATCHES
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

### TARGET AND JUMP
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
### Zero Counters
List data  
`# iptables -Lvn`  

Zero everything  
`# iptables -F`

List data  
`# iptables -Lvn`  

### Flush
#### Clean rules in all chains in filter table
Flush all rules in `all chains` in the `filter table`  
`# iptables -F`  
`# iptables -Lvn`  

#### Clean rules in the INPUT chain in filter table
Flush all rules in `INPUT` chain in the `filter table`  
`# iptables -F INPUT`  
`# iptables -Lvn`  


#### Clean rules in all chains in nat table
Flush all rules in `INPUT` chain in the `nat table`  
`# iptables -t nat -F`  
`# iptables -Lvn`  

### OUTPUT
#### Allow OUTGOING ssh connection from the machine
From the machine I am writing this iptables rule, Allow ssh to any machine via 22 port.
```
# iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT
```
