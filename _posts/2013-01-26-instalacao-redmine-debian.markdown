---
layout: post
title: "Instalação Redmine(Debian)"
date: 2013-01-26 11:10:25 -0200
categories: Ferramentas
---

Demorou, mas estou postando novamente.

Hoje gostaria de apresentar uma ferramente para gerenciamento de projetos de software, o [Redmine][redmine-site].
Dentre todos os gerenciadores que já utilizei este é o mais simples e o mais completo que encontrei.

Não irei falar sobre como utilizar esta ferramenta, mas sim, como realizar sua instalação em um servidor `Linux Debian`.

O [Redmine][redmine-site] é desenvolvido sobre a plataforma `Ruby on Rails`, isto significa que é necessário instalarmos
o framework em nosso servidor.

É possível realizar esta instalação através do `aptitude (apt-get)`, que torna este processo bem mais fácil,
o inconveniente é que estaremos utilizando uma versão 1.4 do [Redmine][redmine-site] enquanto a versão estável mais
recente é `2.2`.

Primeiramente devemos instalar o [RVM][rvm-site], que é um gerenciador de versões para o [Ruby][ruby-site], para isto,
primeiramente vamos instalar suas dependências executando o seguinte comando:

{% highlight sh %}
aptitude install autoconf automake autotools-dev build-essential bison bzip2 curl git libreadline5 libxml2-dev libmysqlclient-dev libreadline5-dev libruby openssl libssl-dev zlib1g zlib1g-dev zlibc git-core gcc make libxml2-dev libxslt-dev libopenssl-ruby libncurses5-dev libapr1-dev libaprutil1-dev build-essential libcurl4-openssl-dev libmagickwand-dev mysql-client apache2-prefork-dev libsqlite3-0 libsqlite3-dev sqlite3
{% endhighlight %}  

Agora iremos instalar o [RVM][rvm-site] e o `Rails`

{% highlight sh %}
bash -s stable << (curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)
source /usr/local/rvm/scripts/rvm
rvm install 1.9.3
rvm --default use 1.9.3
gem install rails
{% endhighlight %}

Com o `Ruby on Rails` instalado o próximo passo é instalar o [Redmine][redmine-site], para isso vamos baixar o projeto e
descompactar o arquivo da seguinte forma:

{% highlight sh %}
wget http://rubyforge.org/frs/download.php/76722/redmine-2.2.2.tar.gz
tar -C /opt/  -vzxf redmine-2.2.2.tar.gz
cd /opt
mv redmine-2.2.2 redmine
{% endhighlight %}

Note que estou instalando o Redmine na pasta `/opt` do servidor, porém pode ser instalado em qualquer pasta de sua preferencia.

Agora vamos iniciar as configurações

{% highlight sh %}
cd /opt/redmine
gem install bundler
bundle install --without development test postgresql sqlite
rake generate_secret_token
{% endhighlight %}

Note que na linha 3 estou instalando as dependências **SEM** o ambiente de desenvolvimento e teste, além dos pacotes
`postgresql` e `sqlite`, isto por que optei por utilizar o `mysql` como gerênciador de banco de dados.

O próximo passo é criar a base de dados. Para isso acesse o mysql e execute os seguintes comandos:

{% highlight sh %}
create database redmine character set utf8;
create user 'redmine'@'localhost' identified by 'sua_senha';
grant all privileges on redmine.* to 'redmine'@'localhost';
{% endhighlight %}

Criada a base de dados vamos editar o arquivo de configuração da conexão da seguinte forma:

{% highlight sh %}
cp /opt/redmine/config/database.yml.example  /opt/redmine/config/database.yml
vim /opt/redmine/config/database.yml
{% endhighlight %}

Altere o arquivo de acordo com o nome da base, usuário e senha que foram criados anteriormente, conforme o exemplo abaixo:

{% highlight yaml %}
production:
  adapter: mysql2
  database: redmine
  host: localhost
  username: redmine
  password: sua_senha
{% endhighlight %}

Vamos executar as migrações da base de dados e iniciar a carga dos dados, executando os seguintes comandos:

{% highlight sh %}
RAILS_ENV=production rake db:migrate
RAILS_ENV=production REDMINE_LANG=pt-BR rake redmine:load_default_data
{% endhighlight %}  

Também precisamos criar algumas pastas necessárias e alterar suas permissões:

{% highlight sh %}
mkdir tmp tmp/pdf public/plugin_assets
chown -R www-data:www-data files log tmp public/plugin_assets
chmod -R 755 files log tmp public/plugin_assets
{% endhighlight %}  

Para testar a instalação vamos iniciar o servidor rails:

{% highlight sh %}
ruby script/rails server webrick -e production
{% endhighlight %}  

Acesse `http://localhost:3000`, e pode começar a utiliza-lo fazendo login no sistema através da seguinte autenticação:

{% highlight sh %}
Usuário: admin
Senha: admin
{% endhighlight %}  

## Integração com o Apache

Para integrar o [Redmine][redmine-site] ao `Apache` vamos instalar o módulo e a **gem passenger**:

{% highlight sh %}
aptitude install libapache2-mod-passenger
gem install passenger
passenger-install-apache2-module
{% endhighlight %}  

Vamos criar um link do diretório `public` do redmine apontando a diretório `www` do apache

{% highlight sh %}
ln -s /opt/redmine/public /var/www/redmine
{% endhighlight %}  

O próximo passo é alterar as configurações do apache para que ele utilize o módulo passenger quando solicitado a url do
[Redmine][redmine-site]. Adicione a seguinte configuração no arquivo `/etc/apache2/sites-available/default`

{% highlight shell %}
<Directory /var/www/redmine>
  PassengerAppRoot /opt/redmine
  RailsEnv production
  RailsBaseURI /redmine
  PassengerResolveSymlinksInDocumentRoot on
</Directory>
{% endhighlight %}  

Como instalamos o `Ruby on Rails` a partir do [RVM][rvm-site] é necessário alterar as configurações do módulo passenger.
Para isso devemos alterar o arquivo `/etc/apache2/mods-available/passenger.conf`

{% highlight shell %}
<IfModule mod_passenger.c>
  PassengerRoot /usr/local/rvm/gems/ruby-1.9.3-p374/gems/passenger-3.0.19
  PassengerRuby /usr/local/rvm/wrappers/ruby-1.9.3-p374/ruby
  PassengerDefaultUser www-data           
</IfModule>
{% endhighlight %}  

Por fim vamos alterar o arquivo `/etc/apache2/mods-available/passenger.load`:

{% highlight shell %}
LoadModule passenger_module /usr/local/rvm/gems/ruby-1.9.3-p374/gems/passenger-3.0.19/ext/apache2/mod_passenger.so
{% endhighlight %}  

Agora é só habilitar o módulo passenger e reiniciar o `Apache`

{% highlight shell %}
a2enmod passenger
/etc/init.d/apache2 restart
{% endhighlight %}  

Pronto, podemos acessar o [Redmine][redmine-site] da seguinte forma:

`http://localhost/redmine`

[redmine-site]:http://www.redmine.org/
[ruby-site]:https://www.ruby-lang.org/
[rvm-site]:https://rvm.io/
