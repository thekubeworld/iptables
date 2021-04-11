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

|  TABLE        |  COMMAND           |  CHAIN_NAME        |   MATCHES                 | TARGET/JUMP  |
|---------------|--------------------|--------------------|---------------------------|--------------|
|  filter       |  -A (append)       |  INPUT             | -s source_ip              | ACCEPT       |
|  nat          |  -I (insert)       |  OUTPUT            | -d dest_ip                | DROP         |
|  mangle       |  -D (delete)       |  FORWARD           | -p protocol               | REJECT       |
|  raw          |  -R (replace)      |  PREROUTING        | --sport source port       | LOG          |
|               |  -F (flush)        |  POSTROUTING       | --dport destionation port | SNAT         |
|               |  -Z (zero)         |  USER_DEFINED      | -i incoming int           | DNAT         |
|               |  -L (list)         |                    | -o outgoing int           | MASQUERADE   |
|               |  -S (show)         |                    | -m mac                    | LIMIT        |
|               |  -N (create)       |                    | -m time                   | RETURN       |
|               |  -X (delete)       |                    | -m quota                  | TEE          |
|               |  -P (set policy)   |                    | -m limit                  | TOS          |
|               |                    |                    | -m recent                 | TTL          |

