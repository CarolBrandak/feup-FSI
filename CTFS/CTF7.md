# CTF Semana 10 - Weak Encryption

Neste desafio, foi nos fornecido acesso a um servidor que nos dá a flag cifrada em AES-CTR mas sabemos também que a maneira como esta foi cifrada cria uma vulnerabilidade do tipo Weak Encryption. É nos tambem fornecido o programa **cipherspec.py**:

```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
import os

KEYLEN = 16

def gen(): 
	offset = 3 # Hotfix to make Crypto blazing fast!!
	key = bytearray(b'\x00'*(KEYLEN-offset)) 
	key.extend(os.urandom(offset))
	return bytes(key)

def enc(k, m, nonce):
	cipher = Cipher(algorithms.AES(k), modes.CTR(nonce))
	encryptor = cipher.encryptor()
	cph = b""
	cph += encryptor.update(m)
	cph += encryptor.finalize()
	return cph

def dec(k, c, nonce):
	cipher = Cipher(algorithms.AES(k), modes.CTR(nonce))
	decryptor = cipher.decryptor()
	msg = b""
	msg += decryptor.update(c)
	msg += decryptor.finalize()
	return msg
```

Ao analisar o código, conseguimos perceber que a vulnerabilidade surge na geração da key usada para cifrar a flag. As keys geradas são sempre de 16 bytes mas os seus primeiros 13 bytes vão ser sempre 0, logo, sempre que geramos uma key nova apenas os últimos 3 bytes vão ser diferentes. Com este conhecimento em mente, criámos o seguinte código para chergarmos ao valor da flag, usando um método brute force para tentar todas as combinações possíveis deste:

```python
for a in range(0, 256):
	for b in range(0, 256):
		for c in range(0,256):
			s = b'\x00'*(KEYLEN-3)
			s = s + bytes([a,b,c])
			msg = dec(s, bytes.fromhex("9569067051c016042fbab5bf786b4b03f6e8696ba82334d66853e1379506a61c9c902f9f98a58b"), b'\x37\xbc\xae\xe8\x7a\x03\x9d\x4d\x37\xbc\x8e\x59\x69\x01\x71\x59')
			if msg[:4] == b"flag":
				print(msg)
				exit()
```

Depois corremos o comando **nc ctf-fsi.fe.up.pt 6003** para obter o cyphertext: 

```shell
ricardo@DESKTOP-GTOG6DJ:/mnt/c/Users/ricar/fsi-ctf$ nc ctf-fsi.fe.up.pt 6003
nonce: e97a3690a0b6bb2e14ae035868ed4932
ciphertext: c87bce0891509e58671baf5117691337dae5f4d4cb80db4de0aab8ddd82e25a2b5df06f5d1da0d
```

Depois corremos o programa **cipherspec.py* e obtemos a flag: flag{7e9b910628e107d06abf9b832cd71df9}

