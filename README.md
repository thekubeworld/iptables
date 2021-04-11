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

|  TABLE TYPES  |  COMMAND TYPES                                                  |  CHAIN NAMES       |   MATCHES                 | TARGET/JUMP  |
|---------------|-----------------------------------------------------------------|--------------------|---------------------------|--------------|
|  filter       |  -A Append the rule to the end of selected chain                |  INPUT             | -s source_ip              | ACCEPT       |
|               |                                                                 |                    |                           |              |
|  nat          |  -I Insert one or more rules in the selected chain              |                    |                           |              |
|               |     on specific position, by default on the top (position 1)    |  OUTPUT            | -d dest_ip                | DROP         |
|               |                                                                 |                    |                           |              |
|               |  -L List all rules in the selected chain.                       |                    |                           |              |
|               |     If no chain is selected all chains as listed                |                    | -o outgoing_int           | MASQUERADE   |
|               |                                                                 |                    |                           |              |
|               |  -F Flush the selected chain. If no chain is selected, all      |                    |                           |              |
|               |     chains are selected                                         |                    |                           |              |
|               |                                                                 |                    |                           |              |        
|  mangle       |  -D Delete rule                                                 |  FORWARD           | -p protocol               | REJECT       |
|               |                                                                 |                    |                           |              |           
|  raw          |  -R Replace rule                                                |  PREROUTING        | --sport source port       | LOG          |
|               |                                                                 |  POSTROUTING       | --dport destionation port | SNAT         |
|               |                                                                 |                    |                           |              |
|               |  -Z Zero the packet and byte counters. If no chain is specified |  USER_DEFINED      | -i incoming int           | DNAT         |
|               |     all chains counters are clean                               |                    |                           |              |
|               |  -S Show                                                        |                    | -m mac                    | LIMIT        |
|               |                                                                 |                    |                           |              |
|               |  -N Create a new user defined chain by the given name           |                    | -m time                   | RETURN       |
|               |  -X Delete the user-defined chain specified                     |                    | -m quota                  | TEE          |
|               |  -P Set the policy for the built-in chain                       |                    |                           |              |
|               |    (INPUT, OUTPUT or FORWARD)                                   |                    | -m limit                  | TOS          |
|               |                                                                 |                    | -m recent                 | TTL          |

