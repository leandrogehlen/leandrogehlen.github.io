---
layout: post
title: "Migrações do Banco de Dados com Grails"
date: 2013-02-18 13:31:19 -0200
categories: Grails
---

Geralmente, vejo em tutoriais sobre Grails, demonstrarem primeiramente a geração de `Scaffold`, por ser umas das
funcionalidades que chama muito atenção pela facilidade e rapidez de se gerar um `CRUD`. Porém acredito que para
começarmos a programar o primeiro passo seria as migrações da base de dados.

Apesar do [Grails][grails-site] criar automaticamente o esquema do banco de dados através do [Hibernate][hibernate-site],
e isso contribuir para se iniciar rapidamente o desenvolvimento de uma aplicação Web, o que parece ser um facilitador
pode se tornar em um grande transtorno quando esta aplicação se mover para a produção.

Quando o [Hibernate][hibernate-site] realiza as operações no seu esquema de banco de dados, não leva em consideração se
sua aplicação pode perder dados. Além da perca dos dados a aplicação poderá ficar com sua estrutura de banco de dados
diferente das classes de domínio, dependendo do forma de atualização do esquema que escolhermos, o [Hibernate][hibernate-site]
não removerá nada, apenas irá criar o que esta faltando.

Tendo isto em vista o [Grails][grails-site] possuí o plugin [Grails Database Migration Plugin][grails-migration],
com ele podemos criar um script de atualização deste esquema, o que nos proporciona a criação ou remoção de estruturas
de forma segura, mantendo nosso esquema de base de dados sincronizado com as classes de domínio da aplicação.

Para demonstrar a utilização deste plugin vamos criar uma aplicação nova. Para isso abra o terminal de seu computador
e digite o seguinte comando:

{% highlight shell %}
grails create-app migracao
{% endhighlight %}  

Este comando irá criar toda a estrutura de sua aplicação e já estará instalado a versão 1.2.1 database-migration,
aconselho atualizar este plugin para a versão 1.3.2 pois algumas correções foram realizadas e é importante que o plugin
funcione correamente.

Para isso altere o arquivo `BuildConfig.groovy` que está em `grails-app/conf` do seu projeto, substituindo a versão
`1.2.1` para `1.3.2`. Com isso a sessão plugins deste arquivos deverá ficar da seguinte forma

{% highlight groovy %}
plugins {
  ...
  runtime ":database-migration:1.3.2"    
  ...
}
{% endhighlight %}  

após a alteração salve o arquivo e execute o seguinte comando:

{% highlight shell %}
grails dbm-generate-changelog changelog-1.0.groovy
{% endhighlight %}  

Este comando irá gerar o arquivo `changelog-1.0.groovy` dentro do diretório `grails-app/migration`. Além deste arquivo vamos
criar outro arquivo dentro deste mesmo diretório denominado changelog.groovy. Este será um arquivo mestre, que irá
informar ao plugin quais os scripts devem ser executados, isto por que a cada nova versão de sua aplicação será gerado
um novo arquivo de script.

Inicialemente arquivo `changelog.groovy` terá o seguinte conteúdo:

{% highlight groovy %}
databaseChangeLog = {
  include file: "changelog-1.0.groovy"   
}
{% endhighlight %}  

O último passo é configurar como será executado estas migrações, para isso vamos alterar o arquivo `Config.groovy` que
se encontra em `grails-app/conf`, adicionando as seguintes configurações:

{% highlight groovy %}
grails.plugin.databasemigration.updateOnStart = true
grails.plugin.databasemigration.updateOnStartFileNames = ['changelog.groovy']
{% endhighlight %}

A primeira configuração informa ao plugin que as alterações do esquema serão executadas ao iniciar a aplicação.
A segunda informa quais os arquivos de script devem ser executados

Caso queira executar manualmente os scripts deve-se remover a primeira configuração e executar o seguinte comando

{% highlight groovy %}
grails dbm-update
{% endhighlight %}

Antes de iniciar a aplicação vamos configurar o arquivo DataSource.groovy para que ele não utilize a atualização pelo
[Hibernate][hibernate-site], para isso vamos comentar a linha `dbCreate = “create-drop”` deixando o arquivo da
seguinte forma:

{% highlight groovy %}
...
environments {
    development {
        dataSource {
            //dbCreate = "create-drop" // one of 'create', 'create-drop', 'update', 'validate', ''
            url = "jdbc:h2:mem:devDb;MVCC=TRUE;LOCK_TIMEOUT=10000"
        }
    }
...
}
{% endhighlight %}

Vamor executar a aplicação através do comando:

{% highlight shell %}
grails run-app
{% endhighlight %}  

O plugin fornece uma ferramenta de documentação de suas migrações para ver essa documentação acesse
`http://localhost:8080/aula1-migracao/dbdoc` pelo seu browser.

Agora vamos incluir o atributo `sexo` na classe `Pessoa`, deixando ela no seguinte formato:

{% highlight groovy %}
class Pessoa {
  String nome
  String sobreNome
  Date dataNasc
  String sexo

  static belongsTo = [cidade: Cidade]

  static constraints = {
      nome blank: false
      sobreNome blank: false
      dataNasc blank: false
      sexo inList: ["M", "F"]
    }
}
{% endhighlight %}

Neste momento vamos alterar manualmente o arquivo changelog-1.0.groovy para adicionar uma coluna na tabela pessoa
adicionando o seguinte conteúdo ao arquivo:

{% highlight groovy %}
changeSet(author: "leandro (generated)", id: "1364128026256-4") {
  comment { 'Adicionado coluna sexo' }
  addColumn(tableName: "pessoa") {
      column(name: "sexo", type: "varchar(1)") {
          constraints(nullable: "false")
      }
  }
}
{% endhighlight %}

A geração do script pelo comando dbm-generate-changelog pode ser utilizada até o momento em que a versão da aplicação
não está em produção, pois este comando não compara o que tem no script atual para gerar as diferenças, ele simplesmente
gera um novo script criando toda a estrutura a partir das classes de domínio.

Eu particularmente não utilizo a geração, acho mais confiável ir criando os scripts.

[grails-site]:https://grails.org/
[grails-migration]:http://grails.org/plugin/database-migration
[hibernate-site]:http://hibernate.org/
