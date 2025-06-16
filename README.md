# SSH_Remote_Tunnel
Quick guide on setting up ssh remote tunnel

# Use Case Scenario
Suppose we have 2 server machines:\
one allows public access and the other does not\
[ A ] Private Server\
[ B ] Public Server\
[ C ] Client

We now want to achieve ssh connection from [ C ] to [ A ] with the help from [ B ].

# Port Forwarding Approach
This approach requires [ B ] to at least open one more port, eg. 2222.
```
# On [ A ]
ssh -fNR 2222:localhost:22 <Host_B>
```
```
# On [ B ] : /etc/ssh/sshd_config
GatewayPorts yes
```
```
# On [ C ]
ssh -p 2222 <user_for_A>@<Host_B>
```

# Unix Socket Approach
This approach requires netcat-openbsd or socat\
It is a 2-step connection\
[ C ] ssh connects to [ B ], and from [ B ] connects to [ A ]
```
# On [ A ]
ssh -fNR </path/to/socket>:localhost:22 <Host_B>
```
```
# On [ B ] : /etc/ssh/sshd_config
StreamLocalBindUnlink yes
```
```
# On [ C ]
ssh <Host_B> -t \
'ssh -o "ProxyCommand nc -U </path/to/socket>" <user_for_A>@localhost
```

# Requirement
OpenSSH version 3\
netcat-openbsd for ssh through unix socket\
enable GatewayPorts in remote server ( config file: /etc/ssh/sshd_config )