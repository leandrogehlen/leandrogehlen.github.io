---
layout: post
title: "Internacionalização Com Grails"
date: 2013-05-30 17:00:19 -0200
categories: Grails
---

Apesar de muitos desenvolvedores, não tomar o cuidado de desenvolver suas aplicações com suporte a múltiplos idiomas,
acredito ser bem relevante levar isto em consideração, afinal se estas aplicações ganharem proporções maiores e este
requisito não foi analisado inicialmente, você terá uma enorme dor de cabeça para mudar isto.

Outro fator importante, é a que com a internacionalização ajuda a resolver o problema de codificação de caracteres nos
arquivos.

Com certeza quem já desenvolveu aplicações web já deve ter passado pelo problema e aparecerem carácteres `coringas` no
lugar de caracteres acentuados ou o carácter `ç`. Uma das soluções é utilizar a meta-tag:

{% highlight html %}
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
{% endhighlight %}  

Porém ao fazer isso teremos uma aplicação que irá funcionar apenas com caracteres latinos. Caso sua aplicação tenha que
possuir um idioma como Japonês, por exemplo, este recurso não irá solucionar o problema.

Um dica importante para resolver os problemas de codificação, é trabalhar com sistema operacional, banco de dados e
arquivos com o mesmo charset, preferencialmente UTF-8.

Com o [Grails][grails-site], o uso de internacionalização, é praticamente intrínseco e para fazer uso deste recurso
devemos entender como ele é organizado.

O [Grails][grails-site], possuí o diretório `grails-app/i18n`, onde o nome de cada arquivo contido no diretório deve
iniciar por `messages_` e terminar com a localidade, conforme os exemplos abaixo:

{% highlight shell %}
messages.properties
messages_en.properties
messages_fr.properties
messages_pt_BR.properties
{% endhighlight %}  

Por padrão Grails olha para o arquivo messages.properties a menos que o usuário tenha especificado a localidade.
É possível criar seu próprio pacote de mensagens, para isso, basta criar um novo arquivo de propriedades que termina
com o localidade que você está interessado. Por exemplo `messages_pt_BR.properties` refere-se ao português brasileiro.

## Usando as mensagens

O local mais comum para utilizar as mensagens é nos arquivos `.gsp` contidos no diretório `grails-app/view`, para isso
é possível utilizar a seguinte tag:

{% highlight html %}
<g:message code= "chave.da.mensagem" />
{% endhighlight %}  

Quando este recuso for utilizado o grails irá procurar no arquivo correspondente a localidade que o usuário está utilizando.
Nele deve estar contido o conteúdo da mensagem conforme é demonstrado no exemplo abaixo:

{% highlight shell %}
chave.da.mensagem = Olá mundo
{% endhighlight %}  

É possível utilizar mensagem com argumentos, por exemplo:

{% highlight html %}
<g:message code="chave.da.mensagem.com.arg" args= "${['Leandro','Internacionalização']}" />
{% endhighlight %}  

A declaração mensagem especifica parâmetros posicionais que são dinamicamente especificados:

{% highlight shell %}
chave.da.mensagem.com.arg = Olá, {0}. Você está aprendendo {1} em Grails.
{% endhighlight %}  

Além disso podemos querer utilizar este recurso no classe de `controller`, para isso podemos utilizar este recurso da
seguinte forma:

{% highlight groovy %}
def show () {
  def msg = message(code: "chave.da.mensagem.com.arg", args: ['Leandro', 'Internacionalização'])
}
{% endhighlight %}  

## Conclusão

Utilizando estes cuidados ao iniciar uma aplicação, com certeza será evitado futuras dores de cabeça, além de tornar
sua aplicação flexível a qualquer idioma.






[grails-site]:https://grails.org/
