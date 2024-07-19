Setting up the Ligolo-ng.
```
sudo ip tuntap add user <Username> mode tun ligolo
```
```
sudo ip link set ligolo up
```
Launching Ligolo-Proxy in local Machine.
```
./lin-proxy -selfcert -laddr 0.0.0.0:443
```

Connecting Ligolo-Agent from Target Machine.
For Windows.
```
./agent.exe -connect 192.168.45.xxx:443 -ignore-cert
```
For Linux.
```
./lin-agent -connect 192.168.45.xxx:443 -ignore-cert
```

Add the subnet to our Route table.
```
sudo ip route add 172.16.170.0/24 dev ligolo
```

Back To local Machine and check for connection in Proxy, run this command to list active sessions.
```
sessions
```
Once connected to the session from the Target Machine Strat Tunnelling.
```
start
```

To add listener to the Proxy, run this command in proxy on Target Session
```
listener_add --addr 0.0.0.0:443 --to 127.0.0.1:443 --tcp
```
```
listener_add --addr 0.0.0.0:80 --to 127.0.0.1:80 --tcp
```

To access Local ports of Target Machine for loopback, run this command.
```
sudo ip route add 240.0.0.1/32 dev ligolo
```