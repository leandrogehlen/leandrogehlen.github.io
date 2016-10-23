---
layout: post
title:  "Por Que Grails?"
date:   2012-12-10 10:30:20 -0200
categories: Grails
---

Vou começar falando sobre por que escolhi [Grails][grails-site] e quais os pontos são necessários analisar  no nomento
de escolher uma ferrementa de desenvolvimento.

## Acesso a base de dados

Acredito que atualmente, é necessário escolher algo que possua suporte a múltiplas bases de dados. Apesar de existir
inúmeras tecnologias fornecerem ferramentas para criar uma aplicação que suporte conexão com múltiplos bancos de dados,
ainda tem muito desenvolvedor utilzando PL/SQL tornando sistemas ingessados e colocando fim na questão escalabilidade.

O [Grails][grails-site] possuí o [GORM][grails-gorm] um framework de persistência baseado em `ActiveRecords` do [RubyOnRails][rubyonrails-site] e
tem como base o consagrado [Hibernate][hibernate-site] no mundo Java.

## Migrações

O [Grails][grails-site] possuí o [Database Migration Plugin][grails-migration], que torna possível criar scripts para migrar a
base de dados. Este script é totalmente `DSL`, tornando fácil a criação, alteração ou exclusão de objetos ou registros.

## Segurança

Bom, é imprescindível que um sistemas tenha segurança no acesso aos dados. O [Grails][grails-site] possuí filters que são
interceptadores de requisições, onde é possível definir as regras para permissão ou proibição do acesso.
Caso seja necessário um controle mais rígido é possível utilizar o plugin [Spring-Security][grails-security].

## Escalabilidade

Vou considerar este item, como sendo a capacidade de um sistema estar preparado para crescer em todos seus aspectos.
Fazer com que isto seja possível depende muito da modelagem deste sistema e isto pouco depende da tecnlogia.
Porém é necessário que a tecnologia escolhida forneça as ferramentas para isto. O [Grails][grails-site] poussuí uma
central de plugins, que torna fácil adaptar seu sistema a inúmeras funcionalidas como por exemplo `Multi-tenancy`,
segurança, migrações entre outros.

## Facilidade no desenvolvimento

Neste ponto, quero relatar sobre a linguagem [Groovy][groovy-site], que por ser uma linguagem funcional nos dá uma
facilidade enorme na hora de produzir código. Além disso, para mim que estou vindo do mundo Java e optei por manter um
software sobre a JVM, o [Groovy][groovy-site] integra com todas as classes Java existentes, além de poder produzir
código `Groovy` ou `Java`.
Outro item interessante são os [Scaffolding][grails-scaffolding]. Com eles é possível gerar toda a parte de `CRUD` da
sua aplicação.

## Testes

Apesar de muitos desenvolvedores ainda não utilizarem testes automatizados, acredito este ser o requisisto é o mais
importante.
O [Grails][grails-site] poussuí tanto testes unitários quanto testes de integração.

## Conclusão

A minha escolha pelo [Grails][grails-site]  levou em consideração além dos pontos citados, a curva de aprendizado ser
menor por já conhecer a plataforma `Java`, a boa documentação existente, por ser desenvolvido sobre frameworks
consolidados no mercado como: [Hibernate][hibernate-site], [Spring][spring-site], e por ter exemplos de sucesso como o
[Linkedin][linkedin-site] e a [Sky][sky-site].

[grails-site]:https://grails.org/
[grails-migration]:http://grails.org/plugin/database-migration
[grails-security]:http://grails.org/plugin/spring-security-core
[grails-gorm]:http://docs.grails.org/latest/guide/GORM.html
[grails-scaffolding]:http://docs.grails.org/latest/guide/scaffolding.html
[groovy-site]:http://www.groovy-lang.org/
[spring-site]:https://projects.spring.io/spring-framework/
[hibernate-site]:http://hibernate.org/
[rubyonrails-site]:https://laravel.com/
[linkedin-site]:http://www.linkedin.com/
[sky-site]:http://www.sky.com/
