# Packet Sniffing and Spoofing

## Lab Task Set 1: Using Scapy to Sniff and Spoof Packets

Scapy, é uma ferramenta de manipulação de packets. Com o Scapy, é possível criar e manipular packets de vários tipos de protocolos, enviá-los, recebe-los e corresponder pedidos e respostas. 

Inicialmente, é nos dado o seguinte programa em Python ao qual vamos chamar **mycode.py**:

```python
from scapy.all import*

a = IP()
a.show()
```

Compilando e correndo o programa obtemos o seguinte output:

```shell
[12/20/23]seed@VM:~/.../Labsetup$ python3 mycode.py
###[ IP ]### 
  version   = 4
  ihl       = None
  tos       = 0x0
  len       = None
  id        = 1
  flags     = 
  frag      = 0
  ttl       = 64
  proto     = hopopt
  chksum    = None
  src       = 127.0.0.1
  dst       = 127.0.0.1
  \options   \
```

Ou seja, o código através do método **IP()** cria um um novo endereço IP default e um packet vazio e ao correr o comando **show()** devolve o conteúdo deste packet. 


### Task 1.1: Sniffing Packets

#### Task 1.1A

Nesta tarefa, é nos pedido para utilizar o Scappy para fazer sniffing de packets. Para isso criámos o seguinte programa **sniffer.py**:

```python
from scapy.all import *

def print_pkt(pkt):
  pkt.show()

interface = ['br-f93ee0917def', 'enp0s3']
pkt = sniff(iface = interface, filter='icmp', prn=print_pkt)
```

O que programa acima faz, é o sniffing de todos os packets que passem pela interface **br-f93ee0917def**, que neste caso corresponde á interface dos containers que são dados para utilizar no lab. Primeiro corremos o programa em **sudo**, usando o comando **sudo chmod a+x sniffer.py**. Depois, noutro terminal fazemos ping para o servidor 8.8.8.8 que corresponde ao **google.com** para obtermos informações dos packets ICMP.

```shell
[12/20/23]seed@VM:~/.../Labsetup$ sudo python3 sniffer.py
###[ Ethernet ]### 
  dst       = 08:00:27:d0:79:2c
  src       = 52:54:00:12:35:02
  type      = IPv4
###[ IP ]### 
     version   = 4
     ihl       = 5
     tos       = 0xc0
     len       = 56
     id        = 1208
     flags     = 
     frag      = 0
     ttl       = 255
     proto     = icmp
     chksum    = 0x9e3c
     src       = 10.0.2.2
     dst       = 10.0.2.15
     \options   \
###[ ICMP ]### 
        type      = dest-unreach
        code      = network-unreachable
        chksum    = 0x96fb
        reserved  = 0
        length    = 0
        nexthopmtu= 0
###[ IP in ICMP ]### 
           version   = 4
           ihl       = 5
           tos       = 0x0
           len       = 82
           id        = 29621
           flags     = DF
           frag      = 0
           ttl       = 63
           proto     = udp
           chksum    = 0xfa2d
           src       = 10.0.2.15
           dst       = 192.168.1.1
           \options   \
###[ UDP in ICMP ]### 
              sport     = 46163
              dport     = domain
              len       = 62
              chksum    = 0xb13d
(...)
```

Também nos pedem para tentar correr o programa sem ser em modo **sudo**. Depois de o tentar verificamos rapidamente que não é fácil visto que não temos permissões para tal.

```shell
[12/20/23]seed@VM:~/.../Labsetup$  python3 sniffer.py
Traceback (most recent call last):
  File "sniffer.py", line 7, in <module>
    pkt = sniff(iface = interface, filter='icmp', prn=print_pkt)
  File "/usr/local/lib/python3.8/dist-packages/scapy/sendrecv.py", line 1036, in sniff
    sniffer._run(*args, **kwargs)
  File "/usr/local/lib/python3.8/dist-packages/scapy/sendrecv.py", line 894, in _run
    sniff_sockets.update(
  File "/usr/local/lib/python3.8/dist-packages/scapy/sendrecv.py", line 895, in <genexpr>
    (L2socket(type=ETH_P_ALL, iface=ifname, *arg, **karg),
  File "/usr/local/lib/python3.8/dist-packages/scapy/arch/linux.py", line 398, in __init__
    self.ins = socket.socket(socket.AF_PACKET, socket.SOCK_RAW, socket.htons(type))  # noqa: E501
  File "/usr/lib/python3.8/socket.py", line 231, in __init__
    _socket.socket.__init__(self, family, type, proto, fileno)
PermissionError: [Errno 1] Operation not permitted
```

#### Task 1.1B - Capture only the ICMP packet

De modo a capturar apenas packets ICMP, realizamos as seguintes alterações ao programa **sniffer.py** de modo a que este apena imprima o IP de destino e de origem de um dado packet ICMP.

```python
from scapy.all import *

def print_pkt(pkt):
  
  if pkt[ICMP] is not None:
  	if pkt[ICMP].type == 0 or pkt[ICMP].type == 8:
  		print("ICMP Packet =====")
  		print(f"\tSource: {pkt[IP].src}")
  		print(f"\tDestination: {pkt[IP].dst}")
  		
  		if pkt[ICMP].type == 0:
  			print(f"\tICMP type: echo-reply")
  		
  		if pkt[ICMP].type == 8:
  			print(f"\tICMP type: echo-request")

interface = ['br-f93ee0917def', 'enp0s3']
pkt = sniff(iface = interface, filter='icmp', prn=print_pkt)
```

Depois fizemos de novo um ping a 8.8.8.8 e registamos o resultado:

```shell
[12/20/23]seed@VM:~/.../Labsetup$ sudo python3 sniffer.py
ICMP Packet =====
	Source: 10.0.2.15
	Destination: 8.8.8.8
	ICMP type: echo-request
ICMP Packet =====
	Source: 8.8.8.8
	Destination: 10.0.2.15
	ICMP type: echo-reply
```

#### Task 1.1B: Capture any TCP packet that comes from a particular IP and with a destination port number 23

POR FAZER 

#### Task 1.1C: Capture  packets  comes  from  or  to  go  to  a  particular  subnet

De modo a enviar um packet para um subnet em específico, neste caso **128.230.0.0/16**, retornamos o programa **sniffer.py** ao seu aspeto original de modo a imprimir toda a informação de cada packet que captura, mas alteramos o filtro para analisar apenas os packets que para a subnet especificada anteriormente:

```python
from scapy.all import *

def print_pkt(pkt):
  pkt.show()
	
interface = ['br-f93ee0917def', 'enp0s3']
pkt = sniff(iface = interface, filter='dst net 128.230.0.0/16', prn=print_pkt)
```

Criámos tambem o programa **send_packet.py**:

```python
from scapy.all import *
ip=IP()
ip.dst='128.230.0.0/16'
send(ip,4)
```

Com isto, basta só correr os dois programas em dois terminais e obtemos o seguinte output:

sniffer.py:

```shell
###[ Ethernet ]### 
  dst       = 52:54:00:12:35:02
  src       = 08:00:27:d0:79:2c
  type      = IPv4
###[ IP ]### 
     version   = 4
     ihl       = 5
     tos       = 0x0
     len       = 20
     id        = 1
     flags     = 
     frag      = 0
     ttl       = 64
     proto     = hopopt
     chksum    = 0xedf4
     src       = 10.0.2.15
     dst       = 128.230.0.0
     \options   \
```

send_packet.py:

```shell
[12/20/23]seed@VM:~/.../Labsetup$ sudo python3 send_packet.py
..
Sent 2 packets.
```

### Task 1.2: Spoofing ICMP Packets

Packet Spoofing acontece quando um root user define todos os campos dos headers de um packet. Para testar Packet Spoofing criámos um novo programa **icmp_spoofer.py**:

```python
from scapy.all import *

a = IP()
a.src = '1.2.3.4'
a.dst = '10.0.2.15'
send(a/ICMP())
ls(a)
```

Depois de correr o programa como root user, obtemos o seguinte output: 

```shell
[12/20/23]seed@VM:~/.../Labsetup$ sudo chmod a+x icmp_spoofer.py
[12/20/23]seed@VM:~/.../Labsetup$ sudo python3 icmp_spoofer.py
.
Sent 1 packets.
version    : BitField  (4 bits)                  = 4               (4)
ihl        : BitField  (4 bits)                  = None            (None)
tos        : XByteField                          = 0               (0)
len        : ShortField                          = None            (None)
id         : ShortField                          = 1               (1)
flags      : FlagsField  (3 bits)                = <Flag 0 ()>     (<Flag 0 ()>)
frag       : BitField  (13 bits)                 = 0               (0)
ttl        : ByteField                           = 64              (64)
proto      : ByteEnumField                       = 0               (0)
chksum     : XShortField                         = None            (None)
src        : SourceIPField                       = '1.2.3.4'       (None)
dst        : DestIPField                         = '10.0.2.15'     (None)
options    : PacketListField                     = []              ([])
[12/20/23]seed@VM:~/.../Labsetup$ 
```

### Task 1.3: Traceroute

Traceroute, é um comando na shell do Linux que fornece todas as routes possíveis com os delays associados para se chegar a um qualquer IP na Internet. Para isto, criámos o seguinte programa **my_traceroute.py** que tem o objetivo de realizar o mesmo processo que o **traceroute**:

```python
from scapy.all import *

inRoute = True
i = 1
while inRoute:

	a = IP(dst='8.8.8.8', ttl=i)
	response = sr1(a/ICMP(),timeout=7,verbose=0)

	if response is None:
		print(f"{i}Request timed out")
	elif response.type == 0:
		print(f"{i} {response.src}")
		inRoute = False
	else:
		print(f"{i} {response.src}")
	
	i = i + 1
```

Para testar usámos de novo o 8.8.8.8, Obtendo o seguinte resultado:

```shell
[12/20/23]seed@VM:~/.../Labsetup$ sudo python3 my_traceroute.py
1 10.0.2.2
2 192.168.1.1
3 94.60.0.2
4 83.174.50.26
5 142.250.161.172
6 172.253.67.17
7 192.178.81.218
8 142.250.237.28
9 142.251.55.187
10 74.125.242.161
11 74.125.37.87
12 8.8.8.8
```

### Task 1.4: Sniffing and-then Spoofing

Por último, vamos agora juntar as duas técnicas para implementar um programa sniff-and-then-spoof. Para isso criámos o seguinte programa:

```python
from scapy.all import *

def send_packet(pkt):

	if(pkt[2].type == 8):
		src=pkt[1].src
		dst=pkt[1].dst
		seq=pkt[2].seq
		id=pkt[2].id
		load=pkt[2].load
		print(f"Flip: src {src} dst {dst} type 8 REQUEST")
		print(f"Flop: src {dst} dst {src} type 0 REPLY\n")
		reply=IP(src=dst, dst=src)/ICMP(type=0, id=id, seq=seq)/load
		send(reply,verbose=0)

interfaces = ['enp0s3', 'lo']
pkt = sniff(iface=interfaces, filter='icmp', prn=send_packet)
```

Vamos agora testar 3 cenários diferentes:

>ping 1.2.3.4     # a non-existing host on the Internetping 
>
>10.9.0.99   # a non-existing host on the LAN
>
>ping 8.8.8.8     # an existing host on the Internet

1º Cenário:

User Host: 

```shell
[12/20/23]seed@VM:~/.../Labsetup$ sudo python3 sniff_spoof.py
Flip: src 10.0.2.15 dst 1.2.3.4 type 8 REQUEST
Flop: src 1.2.3.4 dst 10.0.2.15 type 0 REPLY

Flip: src 10.0.2.15 dst 1.2.3.4 type 8 REQUEST
Flop: src 1.2.3.4 dst 10.0.2.15 type 0 REPLY

Flip: src 10.0.2.15 dst 1.2.3.4 type 8 REQUEST
Flop: src 1.2.3.4 dst 10.0.2.15 type 0 REPLY

Flip: src 10.0.2.15 dst 1.2.3.4 type 8 REQUEST
Flop: src 1.2.3.4 dst 10.0.2.15 type 0 REPLY
```

User Container:

```shell
[12/20/23]seed@VM:~/.../Labsetup$ ping 1.2.3.4
PING 1.2.3.4 (1.2.3.4) 56(84) bytes of data.
64 bytes from 1.2.3.4: icmp_seq=1 ttl=64 time=4.04 ms
64 bytes from 1.2.3.4: icmp_seq=2 ttl=64 time=9.44 ms
64 bytes from 1.2.3.4: icmp_seq=3 ttl=64 time=4.44 ms
64 bytes from 1.2.3.4: icmp_seq=4 ttl=64 time=3.77 ms
^C
--- 1.2.3.4 ping statistics ---
5 packets transmitted, 4 received, 20% packet loss, time 4007ms
rtt min/avg/max/mdev = 3.767/5.423/9.444/2.333 ms
```

2º Cenário:

POR FAZER

3º Cenário:

User Host:

```shell
[12/20/23]seed@VM:~/.../Labsetup$ sudo python3 sniff_spoof.py
Flip: src 10.0.2.15 dst 8.8.8.8 type 8 REQUEST
Flop: src 8.8.8.8 dst 10.0.2.15 type 0 REPLY

Flip: src 10.0.2.15 dst 8.8.8.8 type 8 REQUEST
Flop: src 8.8.8.8 dst 10.0.2.15 type 0 REPLY

Flip: src 10.0.2.15 dst 8.8.8.8 type 8 REQUEST
Flop: src 8.8.8.8 dst 10.0.2.15 type 0 REPLY

Flip: src 10.0.2.15 dst 8.8.8.8 type 8 REQUEST
Flop: src 8.8.8.8 dst 10.0.2.15 type 0 REPLY

Flip: src 10.0.2.15 dst 8.8.8.8 type 8 REQUEST
Flop: src 8.8.8.8 dst 10.0.2.15 type 0 REPLY
```

User Container:

```shell
[12/20/23]seed@VM:~/.../Labsetup$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=64 time=5.17 ms
64 bytes from 8.8.8.8: icmp_seq=1 ttl=59 time=20.3 ms (DUP!)
64 bytes from 8.8.8.8: icmp_seq=2 ttl=64 time=3.17 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=59 time=24.2 ms (DUP!)
64 bytes from 8.8.8.8: icmp_seq=3 ttl=64 time=3.03 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=59 time=20.7 ms (DUP!)
64 bytes from 8.8.8.8: icmp_seq=4 ttl=64 time=3.33 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=59 time=18.8 ms (DUP!)
64 bytes from 8.8.8.8: icmp_seq=5 ttl=64 time=14.1 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=59 time=18.6 ms (DUP!)
^C
--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 received, +5 duplicates, 0% packet loss, time 4006ms
rtt min/avg/max/mdev = 3.032/13.136/24.215/8.090 ms
```












