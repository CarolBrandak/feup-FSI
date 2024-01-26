# Environment Variable and Set-UID Lab

## Task 1

Ao usar o comando **printenv** ou **env** conseguimos fazer print de todas as variáveis do sistema e ```printenv (váriavel de sistema)``` e ```env | grep (variável de sistema)``` faz print de uma variável em específico.<br>
Os comando export e unset podem ser usados para definir ou apagar novas variáveis.

```shell
[10/10/23]seed@VM:~/.../Labsetup$ export MYVAR="testvariable"
[10/10/23]seed@VM:~/.../Labsetup$ printenv MYVAR
testvariable
[10/10/23]seed@VM:~/.../Labsetup$ unset MYVAR
[10/10/23]seed@VM:~/.../Labsetup$ printenv MYVAR
[10/10/23]seed@VM:~/.../Labsetup$
```

## Task 2

Guardamos o output do ficheiro ```myprintenv.c``` no ficheiro de texto ```parentenv.txt``` e depois de alterar o codigo como pedido guardamos o novo output em ```childenv.txt```.<br>
Por último, de modo a comparar os dois outputs obtidos usamos o comando ```diff``` e concluimos que não existem diferenças nos dois ficheiros.<br>
Com isto podemos também concluir que as variáveis de ambiente do processo child são herdadas do processo parent.<br>

```shell
[10/10/23]seed@VM:~/.../Labsetup$ diff parentenv childenv
[10/10/23]seed@VM:~/.../Labsetup$
```

## Task 3

Correndo o programa ```myenv.c``` sem qualquer alteração não gera nenhum output visto que é passado o valor ```NULL``` a função ```execve()``` logo mudando este valor para ```environ``` conseguimos obter todas as variáveis do sistema.

```shell
[10/10/23]seed@VM:~/.../Labsetup$ gcc myenv.c
[10/10/23]seed@VM:~/.../Labsetup$ ./a.out
[10/10/23]seed@VM:~/.../Labsetup$ gcc myenv.c
[10/10/23]seed@VM:~/.../Labsetup$ ./a.out
SHELL=/bin/bash
SESSION_MANAGER=local/VM:@/tmp/.ICE-unix/1761,unix/VM:/tmp/.ICE-unix/1761
QT_ACCESSIBILITY=1
COLORTERM=truecolor
XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
XDG_MENU_PREFIX=gnome-
GNOME_DESKTOP_SESSION_ID=this-is-deprecated
GNOME_SHELL_SESSION_MODE=ubuntu
SSH_AUTH_SOCK=/run/user/1000/keyring/ssh
XMODIFIERS=@im=ibus
DESKTOP_SESSION=ubuntu
SSH_AGENT_PID=1723
GTK_MODULES=gail:atk-bridge
PWD=/home/seed/Desktop/seed-labs-master/category-software/Environment_Variable_and_SetUID/Labsetup
LOGNAME=seed
XDG_SESSION_DESKTOP=ubuntu
XDG_SESSION_TYPE=x11
GPG_AGENT_INFO=/run/user/1000/gnupg/S.gpg-agent:0:1
XAUTHORITY=/run/user/1000/gdm/Xauthority
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
WINDOWPATH=2
HOME=/home/seed
USERNAME=seed
IM_CONFIG_PHASE=1
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:.tar=01;31:.tgz=01;31:.arc=01;31:.arj=01;31:.taz=01;31:.lha=01;31:.lz4=01;31:.lzh=01;31:.lzma=01;31:.tlz=01;31:.txz=01;31:.tzo=01;31:.t7z=01;31:.zip=01;31:.z=01;31:.dz=01;31:.gz=01;31:.lrz=01;31:.lz=01;31:.lzo=01;31:.xz=01;31:.zst=01;31:.tzst=01;31:.bz2=01;31:.bz=01;31:.tbz=01;31:.tbz2=01;31:.tz=01;31:.deb=01;31:.rpm=01;31:.jar=01;31:.war=01;31:.ear=01;31:.sar=01;31:.rar=01;31:.alz=01;31:.ace=01;31:.zoo=01;31:.cpio=01;31:.7z=01;31:.rz=01;31:.cab=01;31:.wim=01;31:.swm=01;31:.dwm=01;31:.esd=01;31:.jpg=01;35:.jpeg=01;35:.mjpg=01;35:.mjpeg=01;35:.gif=01;35:.bmp=01;35:.pbm=01;35:.pgm=01;35:.ppm=01;35:.tga=01;35:.xbm=01;35:.xpm=01;35:.tif=01;35:.tiff=01;35:.png=01;35:.svg=01;35:.svgz=01;35:.mng=01;35:.pcx=01;35:.mov=01;35:.mpg=01;35:.mpeg=01;35:.m2v=01;35:.mkv=01;35:.webm=01;35:.ogm=01;35:.mp4=01;35:.m4v=01;35:.mp4v=01;35:.vob=01;35:.qt=01;35:.nuv=01;35:.wmv=01;35:.asf=01;35:.rm=01;35:.rmvb=01;35:.flc=01;35:.avi=01;35:.fli=01;35:.flv=01;35:.gl=01;35:.dl=01;35:.xcf=01;35:.xwd=01;35:.yuv=01;35:.cgm=01;35:.emf=01;35:.ogv=01;35:.ogx=01;35:.aac=00;36:.au=00;36:.flac=00;36:.m4a=00;36:.mid=00;36:.midi=00;36:.mka=00;36:.mp3=00;36:.mpc=00;36:.ogg=00;36:.ra=00;36:.wav=00;36:.oga=00;36:.opus=00;36:.spx=00;36:*.xspf=00;36:
XDG_CURRENT_DESKTOP=ubuntu:GNOME
VTE_VERSION=6003
GNOME_TERMINAL_SCREEN=/org/gnome/Terminal/screen/7bc09103_9208_4754_a84d_bcb0ace30105
INVOCATION_ID=ebbdee88e0274f0596997f2c80aa83aa
MANAGERPID=1528
GJS_DEBUG_OUTPUT=stderr
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=seed
GNOME_TERMINAL_SERVICE=:1.202
DISPLAY=:0
SHLVL=1
QT_IM_MODULE=ibus
XDG_RUNTIME_DIR=/run/user/1000
JOURNAL_STREAM=9:61891
XDG_DATA_DIRS=/usr/share/ubuntu:/usr/local/share/:/usr/share/:/var/lib/snapd/desktop
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.
GDMSESSION=ubuntu
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
OLDPWD=/home/seed/Desktop/seed-labs-master/category-software/Environment_Variable_and_SetUID
_=./a.out
```

## Task 4

Correndo a função ```system()``` podemos verificar que as variáveis de sistema são passados para o programa.

```shell
[10/10/23]seed@VM:~/.../Labsetup$ gcc system.c
[10/10/23]seed@VM:~/.../Labsetup$ ./a.out
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
LESSOPEN=| /usr/bin/lesspipe %s
USER=seed
SSH_AGENT_PID=1723
XDG_SESSION_TYPE=x11
SHLVL=1
HOME=/home/seed
OLDPWD=/home/seed/Desktop/seed-labs-master/category-software/Environment_Variable_and_SetUID
DESKTOP_SESSION=ubuntu
GNOME_SHELL_SESSION_MODE=ubuntu
GTK_MODULES=gail:atk-bridge
MANAGERPID=1528
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
COLORTERM=truecolor
IM_CONFIG_PHASE=1
LOGNAME=seed
JOURNAL_STREAM=9:61891
_=./a.out
XDG_SESSION_CLASS=user
USERNAME=seed
TERM=xterm-256color
GNOME_DESKTOP_SESSION_ID=this-is-deprecated
WINDOWPATH=2
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.
SESSION_MANAGER=local/VM:@/tmp/.ICE-unix/1761,unix/VM:/tmp/.ICE-unix/1761
INVOCATION_ID=ebbdee88e0274f0596997f2c80aa83aa
XDG_MENU_PREFIX=gnome-
GNOME_TERMINAL_SCREEN=/org/gnome/Terminal/screen/7bc09103_9208_4754_a84d_bcb0ace30105
XDG_RUNTIME_DIR=/run/user/1000
DISPLAY=:0
LANG=en_US.UTF-8
XDG_CURRENT_DESKTOP=ubuntu:GNOME
XMODIFIERS=@im=ibus
XDG_SESSION_DESKTOP=ubuntu
XAUTHORITY=/run/user/1000/gdm/Xauthority
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:.tar=01;31:.tgz=01;31:.arc=01;31:.arj=01;31:.taz=01;31:.lha=01;31:.lz4=01;31:.lzh=01;31:.lzma=01;31:.tlz=01;31:.txz=01;31:.tzo=01;31:.t7z=01;31:.zip=01;31:.z=01;31:.dz=01;31:.gz=01;31:.lrz=01;31:.lz=01;31:.lzo=01;31:.xz=01;31:.zst=01;31:.tzst=01;31:.bz2=01;31:.bz=01;31:.tbz=01;31:.tbz2=01;31:.tz=01;31:.deb=01;31:.rpm=01;31:.jar=01;31:.war=01;31:.ear=01;31:.sar=01;31:.rar=01;31:.alz=01;31:.ace=01;31:.zoo=01;31:.cpio=01;31:.7z=01;31:.rz=01;31:.cab=01;31:.wim=01;31:.swm=01;31:.dwm=01;31:.esd=01;31:.jpg=01;35:.jpeg=01;35:.mjpg=01;35:.mjpeg=01;35:.gif=01;35:.bmp=01;35:.pbm=01;35:.pgm=01;35:.ppm=01;35:.tga=01;35:.xbm=01;35:.xpm=01;35:.tif=01;35:.tiff=01;35:.png=01;35:.svg=01;35:.svgz=01;35:.mng=01;35:.pcx=01;35:.mov=01;35:.mpg=01;35:.mpeg=01;35:.m2v=01;35:.mkv=01;35:.webm=01;35:.ogm=01;35:.mp4=01;35:.m4v=01;35:.mp4v=01;35:.vob=01;35:.qt=01;35:.nuv=01;35:.wmv=01;35:.asf=01;35:.rm=01;35:.rmvb=01;35:.flc=01;35:.avi=01;35:.fli=01;35:.flv=01;35:.gl=01;35:.dl=01;35:.xcf=01;35:.xwd=01;35:.yuv=01;35:.cgm=01;35:.emf=01;35:.ogv=01;35:.ogx=01;35:.aac=00;36:.au=00;36:.flac=00;36:.m4a=00;36:.mid=00;36:.midi=00;36:.mka=00;36:.mp3=00;36:.mpc=00;36:.ogg=00;36:.ra=00;36:.wav=00;36:.oga=00;36:.opus=00;36:.spx=00;36:*.xspf=00;36:
GNOME_TERMINAL_SERVICE=:1.202
SSH_AUTH_SOCK=/run/user/1000/keyring/ssh
SHELL=/bin/bash
QT_ACCESSIBILITY=1
GDMSESSION=ubuntu
LESSCLOSE=/usr/bin/lesspipe %s %s
GPG_AGENT_INFO=/run/user/1000/gnupg/S.gpg-agent:0:1
GJS_DEBUG_OUTPUT=stderr
QT_IM_MODULE=ibus
PWD=/home/seed/Desktop/seed-labs-master/category-software/Environment_Variable_and_SetUID/Labsetup
XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
XDG_DATA_DIRS=/usr/share/ubuntu:/usr/local/share/:/usr/share/:/var/lib/snapd/desktop
VTE_VERSION=6003
```

## Task 5

Compilamos o seguinte programa e mudamos o seu owner de **user** para **root** e definimos as seguintes permissões: -rwsr-xr-x. Assim, ao dar enable do bit Set-UID, permite que os utilizadores  executem o ficheiro com permissões de dono.

```c
#include <stdio.h>
#include <stdlib.h>
extern char **environ;
int main()
{
int i = 0;
while (environ[i] != NULL) {
printf("%s\n", environ[i]);
i++;
}
}
```

```shell
$ sudo chown root setuid
$ sudo chmod 4755 setuid
```

Primeiro guardamos o output inicial do programa e procedemos a alterar as variáveis de sistema **PATH**, **LD_LIBRARY_PATH** e uma variável criada por nós chamada **MYVAR**. Depois disto, voltamos a correr o programa e comparamos os dois outputs obtidos e verificamos que só as variáveis **PATH** e **MYVAR** foram herdadas pelo processo filho. Logo podemos concluir que quando um programa pertence ao **root** com Set-UID, este não herda todas as variáveis de sistema.

```shell
[10/12/23]seed@VM:~/.../Labsetup$ gcc setuid.c -o setuid
[10/12/23]seed@VM:~/.../Labsetup$ sudo chown root setuid
[10/12/23]seed@VM:~/.../Labsetup$ sudo chmod 4755 setuid
[10/12/23]seed@VM:~/.../Labsetup$ ./setuid > res1.txt
[10/12/23]seed@VM:~/.../Labsetup$ export PATH="/bin"
[10/12/23]seed@VM:~/.../Labsetup$ export LD_LIBRARY_PATH="/bin"
[10/12/23]seed@VM:~/.../Labsetup$ export MYVAR="Hello World!"
[10/12/23]seed@VM:~/.../Labsetup$ ./setuid > res2.txt
[10/12/23]seed@VM:~/.../Labsetup$ diff res1.txt res2.txt
7a8
> MYVAR=Hello World!
54c55
< PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.
---
> PATH=/bin
```

## Task 6

Criámos o ficheiro **ls.c** com o código fornecido e verificamos que de facto oferece o mesmo output do que o comando **ls** na shell. Depois disto alteramos o código para devolver o output **MALWARE DETECTED!** caso fosse detetada a vulnerabilidade na mudança do valor da variável de sistema PATH. Por último, alterámos as permissões de **ls.c** e ao recompilar e correr o código verficamos que de facto a vulnerabilidade foi explorada corretamente.

```shell
[10/12/23]seed@VM:~/.../Labsetup$ gcc ls.c
[10/12/23]seed@VM:~/.../Labsetup$ ./a.out
a.out	    catall.c  ls.c	 myenv.c       parentenv  res2.txt  setuid.c
cap_leak.c  childenv  mrrobot.c  myprintenv.c  res1.txt   setuid    system.c
[10/12/23]seed@VM:~/.../Labsetup$ nano ls.c
[10/12/23]seed@VM:~/.../Labsetup$ cat ls.c
#include <stdio.h>
#include <stdlib.h>

int main() {
	printf("MALWARE DETECTED!");
	return 0;
}
[10/12/23]seed@VM:~/.../Labsetup$ gcc ls.c
[10/12/23]seed@VM:~/.../Labsetup$ export PATH=/home/seed:$PATH
[10/12/23]seed@VM:~/.../Labsetup$ sudo chown root a.out
[10/12/23]seed@VM:~/.../Labsetup$ sudo chmod 4755 a.out
[10/12/23]seed@VM:~/.../Labsetup$ sudo ln -sf /bin/zsh /bin/sh
[10/12/23]seed@VM:~/.../Labsetup$ ./a.out
MALWARE DETECTED!
```

