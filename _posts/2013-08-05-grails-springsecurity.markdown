---
layout: post
title: "Segurança Com SpringSecurity"
date: 2013-08-05 21:23:37 -0200
categories: Grails
---

Quando desenvolvemos aplicações web, é quase impossível não nos preocuparmos com segurança, autenticação do usuário,
e restrições de acesso.

Desenvolvendo uma aplicação simples, em que o nível de controle se restringe a controlar seu o usuário está autenticado ou não,
o [Grails][grails-site] nos permite utilizar a variável session e em conjunto com os filters resolver esta questão
(este [link][grails-filters] demonstra como implementar esta situação).

Porém quando nos deparamos, com sistemas maiores, onde necessitamos de um controle mais rigoroso, a implementação acima
citada pode não ser suficiente, e apresentar falhas o que pode causar um enorme transtorno para o desenvolvedor.

Uma opção existente para a segurança de sistemas mais complexos, é o plugin [SpringSecurity][grails-security], com ele
é possível criar uma aplicação segura em poucos minutos, facilitando e muito a vida do desenvolvedor.

Este plugin tem muitos recursos, neste post vamos implementar um simples controle de autenticação.

Para isso vamos criar a aplicacao de exemplo:

{% highlight shell %}
grails create-app autenticacao
{% endhighlight %}  

Agora vamos adicionar a dependencia do plugin na sessão plugins do arquivo `grails-app/conf/BuildConfig.groovy` da
seguinte forma:

{% highlight groovy %}
plugins {
  ...

  compile ":spring-security-core:1.2.7.3"

  ...
}
{% endhighlight %}  

Agora vamos executar o comando abaixo para que o grails efetue o download do plugin e o instale corretamente na aplicação:

{% highlight shell %}
grails refresh-dependencies
{% endhighlight %}

O plugin possuí um script para facilitar a sua configuração, e este deve ser executado da seguinte forma:

{% highlight shell %}
grails s2-quickstart br.com.teste Usuario Role
{% endhighlight %}

Conforme é possível observar para executar o script é necessário passar 3 parâmetros. O primeiro diz respeito ao pacote
onde serão gerada as duas classes de domínios necessárias, onde a primeira representa o usuário da apliação e a
segunda é os papéis(roles) que o usuário pode assumir.

Este script também vai alterar o arquivo `grails-app/conf/Config.groovy` adicionando as seguintes linhas:   

{% highlight groovy %}
plugins {
    // Added by the Spring Security Core plugin:
    grails.plugins.springsecurity.userLookup.userDomainClassName = 'br.com.teste.Usuario'
    grails.plugins.springsecurity.userLookup.authorityJoinClassName = 'br.com.teste.UsuarioRole'
    grails.plugins.springsecurity.authority.className = 'br.com.teste.Role'
}
{% endhighlight %}  

O plugin também irá criar as views e os controllers para que o usuário efetue o login no aplição.

Agora vamos editar o arquivo `grails-app/conf/BootStrap.groovy` para que ao iniciar a aplição seja inserido o usuario
teste e adcionando a role `ROLE_ADMIN` a este usuário.

{% highlight groovy %}
import br.com.teste.Role
import br.com.teste.Usuario
import br.com.teste.UsuarioRole

class BootStrap {

  def init = { servletContext ->
      def role = new Role(authority:  "ROLE_ADMIN")
      role.save(flush: true)

      def usuario = new Usuario(username: "teste", password: "teste", accountExpired: false, accountLocked: false, passwordExpired: false, enabled: true)
      usuario.save(flush: true)

      UsuarioRole.create(usuario, role)
    }

    def destroy = {
    }
}
{% endhighlight %}  

Agora é só executar o comando `grails run-app` a aplicação e testar a autenticação do usuário.


[grails-site]:https://grails.org/
[grails-filters]:http://www.itexto.net/devkico/?p=29
[grails-security]:http://grails.org/plugin/spring-security-core
