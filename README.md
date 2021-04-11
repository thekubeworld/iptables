- [iptables](#iptables)
  * [Kernelspace](#kernelspace)
  * [Userspace](#userspace)
  * [Options](#options)
  * [General info](#general-info)
    + [COMMAND TYPES](#command-types)

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
**filter**: 
This is the default and perhaps the most widely used table. It is used to make decisions about whether a packet should be allowed to reach its destination.
iptables filter table has the following built-in chains: INPUT, OUTPUT, FORWARD

nat  
mangle  
raw  
  
### COMMAND TYPES                                                                                               
**-A** Append the rule to the end of selected chain                                                             
**-I** Insert one or more rules in the selected chain  on specific position, by default on the top (position 1)  
**-L** List all rules in the selected chain. If no chain is selected all chains as listed                       
**-F** Flush the selected chain. If no chain is selected, all chains are selected                               
**-D** Delete rule                                                                                              
**-R** Replace rule                                                                                             
**-S** Show                                                                                                     
**-Z** Zero the packet and byte counters. If no chain is specified all chains counters are clean                
**-N** Create a new user defined chain by the given name                                                        
**-X** Delete the user-defined chain specified                                                                  
**-P** Set the policy for the built-in chain (INPUT, OUTPUT or FORWARD)                                         

| CHAIN NAMES    |
|----------------|
| INPUT          |
| OUTPUT         |
| MASQUERADE     |
| FORWARD        |
| PREROUTING     |
| POSTROUTING    |
| USER_DEFINED   |

| MATCHES                   |
|---------------------------|
| -s source_ip              |
| -d dest_ip                |
| -o outgoing_int           |
| -p protocol               |
| --sport source port       |
| --dport destionation port |
| -i incoming int           |
| -m mac                    |
| -m time                   |
| -m quota                  |
| -m limit                  |
| -m recent                 |


| TARGET / JUMP |
|---------------|
| ACCEPT        |
| DROP          |
| REJECT        |
| LOG           |
| SNAT          |
| DNAT          |
| LIMIT         |
| RETURN        |
| TEE           |
| TOS           |
| TTL           |
