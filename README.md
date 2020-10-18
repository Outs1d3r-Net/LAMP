# LAMP (Linux, Apache, Mysql e Php)
[![Banner](banner.png)]()  
##### Configure um servidor LAMP com ubuntu, neste tutorial eu abordo a configuração de vhosts no apache e muito mais !


# Criando ambiente para instalação com multipass:
> O objetivo desta seção é utilizar o snap para instalar o multipass para que possamos obter uma vm ubuntu rapida e facil para implantar o ```LAMP```.  

## Instalando o Snap e Multipass:  
```
$ sudo apt-get update
$ sudo apt-get install snapd
$ sudo snap install multipass
$ sudo ln /snap/bin/multipass /usr/bin/multipass
$ sudo multipass --help
```

## Criando VM ubuntu com Multipass:  
```
$ sudo multipass launch --name LAMP --disk 10G --mem 2G
$ sudo multipass list
$ sudo multipass info LAMP
$ sudo multipass shell LAMP
$ sudo apt update
```

## Configurando Vhosts no apache:  

#### Primeiro instale o apache e crie a estrutura de diretorios:
```
$ sudo apt install apache2 -y
$ sudo mkdir /var/www/MEUSITE.com.br
$ sudo mkdir /var/www/MEUSITE.com.br/public_html
$ sudo mkdir /var/www/MEUSITE.com.br/bkp
$ sudo mkdir /var/www/MEUSITE.com.br/logs/error.log
$ sudo chown -Rf ubuntu:ubuntu /var/www/MEUSITE.com.br
$ sudo chmod 755 -Rf /var/www/MEUSITE.com.br
```

#### Arquivo index.php para teste:
```
$ pico /var/www/MEUSITE.com.br/public_html/index.php
<?php phpinfo(); ?>
```

#### Realize o download do wordpress:
```
$ wget https://br.wordpress.org/latest-pt_BR.tar.gz -O /var/www/MEUSITE.com.br/public_html/wordpress.tar.gz
```

#### Configure o arquivo para o host virtual no apache:
```
$ sudo vi /etc/apache2/sites-available/MEUSITE.com.br.conf
```

```
<VirtualHost *:80>
    ServerAdmin support@MEUSITE.com.br
    ServerName MEUSITE.com.br
    ServerAlias www.MEUSITE.com.br
    DocumentRoot /var/www/MEUSITE.com.br/public_html
    ErrorLog /var/www/MEUSITE.com.br/logs/error.log
    CustomLog /var/www/MEUSITE.com.br/logs/access.log combined
    ErrorDocument 404 /error.htm
</VirtualHost>
```

#### Adicione o ServerName no final do arquivo apache2.conf:
```
$ sudo pico /etc/apache2/apache2.conf
```

```
ServerName localhost
```

#### Reconfigure e reinicie os modulos do apache:
```
$ sudo a2dissite 000-default.conf
$ sudo a2ensite MEUSITE.com.br.conf
$ sudo apache2ctl configtest
$ sudo systemctl restart apache2
$ sudo systemctl status apache2
$ sudo systemctl enable apache2
```

## Configurando mysql, php e phpmyadmin:
```
$ sudo apt install mysql-server mysql-client php libapache2-mod-php php-mysql phpmyadmin -y

```
##### phpmyadmin:
> Edite o arquivo apache2.conf e adicione a linha ```Include``` no final do arquivo para que voce consiga acessar o phpmyadmin em http://localhost/phpmyadmin
```
$ sudo vim /etc/apache2/apache2.conf
Include /etc/phpmyadmin/apache.conf
```

```
$ sudo systemctl restart apache2
```

## Configure usuario e senha no mysql com usuario root e de as devidas permissões:  
```
$ mysql -u root -p
```

```
mysql> CREATE USER 'admin'@'localhost' IDENTIFIED BY 'MyP4ssw0rd';
mysql> CREATE DATABASE wordpress;
mysql> GRANT ALL PRIVILEGES ON wordpress.* TO 'admin'@'localhost';
```

## Testando conexão e acessos com usuario admin:  
```
$ mysql -u admin -p
```

```
mysql> show databases;
mysql> use wordpress;
mysql> quit;
```

#### Descompactar Wordpress:
```
$ tar xzvf /var/www/MEUSITE.com.br/public_html/wordpress.tar.gz /var/www/MEUSITE.com.br/public_html/
$ mv /var/www/MEUSITE.com.br/public_html/wordpress/* /var/www/MEUSITE.com.br/public_html
$ mv /var/www/MEUSITE.com.br/public_html/wordpress.tar.gz /var/www/MEUSITE.com.br/bkp
$ rm -rf /var/www/MEUSITE.com.br/public_html/wordpress
```

#### De permissão para o usuario web www-data para que o wordpress seja instalado corretamente:
```
$ sudo chown -Rf www-data:www-data /var/www/MEUSITE.com.br/public_html
```

## Acesse https://localhost e configure o banco de dados no acesso inicial do wordpress.  

:brazil:

