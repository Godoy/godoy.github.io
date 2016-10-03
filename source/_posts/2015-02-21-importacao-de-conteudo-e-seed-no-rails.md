---
layout: post
title: "Importação de Conteúdo e Seed no Rails"
permalink: importacao-de-conteudo-e-seed-no-rails
date: 2015-02-21 09:34:46
comments: true
description: "Importação de Conteúdo e Seed no Rails"
keywords: "banco de dados, faker, gem, ruby on rails, seed, task"
categories: ruby-on-rails
tags:
- banco de dados
- faker
- gem
- ruby on rails
- seed
- task
---

Durante o desenvolvimento de um projeto é sempre interessante existir conteúdo para que se tenha uma boa visualização de como ficará o site em produção, detectar falhas de usabilidade e navegação. Por várias vezes perdi tempo cadastrando conteúdo “lorem ipsum” em meus projetos. Bem, isso não é necessário - no Ruby on Rails temos a gem **Faker**.

A Faker gera vários tipos de Lorem Ipsum, dentre os quais:

- Nome de pessoas, e-mails, imagens (avatares);
- Nome de cidade, país, latitude/longitude;
- Cor, preço, nome de departamento, número de telefone, entre outros.

Dessa forma, no arquivo `db/seeds.rb`, basta fazer algo bem simples como:

{% highlight Ruby %}
for i in 1..10
  Article.create(title: Faker::Lorem.sentence, content: Faker::Lorem.paragraph(5), image: Faker::Company.logo)
end
{% endhighlight %}

A gem possui suporte a `I18N` (locale). Se o site utilizar `config.i18n.default_locale` com `pt-BR`, os nomes de pessoas, cidades e formatos estarão compatíveis com o Brasil: https://github.com/stympy/faker/blob/master/lib/locales/pt-BR.yml

## Conteúdo Herdado

Outra necessidade recorrente é a importação do conteúdo de um sistema legado, de um banco já existente. Isso pode ser feito com uma simples task. O exemplo abaixo faz importação de notícias de um banco mysql:

No arquivo `lib/tasks/import-news.rake`:

{% highlight Ruby %}
namespace :import do
  desc 'importa noticias de um banco de dados legado'
  task :news => :environment do
    puts "IMPORTANDO NOTICIAS......"

    # classe do antigo DB
    class OldNews < ActiveRecord::Base
      establish_connection :old_database_connection
      set_table_name 'Noticia' #nome da tabela
      set_primary_key 'idNoticia' #campo chave primaria
    end

    OldNews.order("idNoticia ASC").each do |oldItem|
      News.find_or_create_by_title(
        :title  => oldItem.titulo,
        :date => oldItem.data,
        :body => oldItem.conteudo
      )
    end
  end
end
{% endhighlight %}

Sendo a configuração de acesso ao banco antigo inserida no arquivo `config/database.yml`:

{% highlight Ruby %}
old_database_connection:
  adapter: mysql2
  encoding: utf8
  database: nome_banco_antigo
  pool: 5
  host: host_db
  username: username_db
  password: pass_db
  socket: /var/run/mysqld/mysqld.sock
{% endhighlight %}

Para executar a task, no `Terminal`:

{% highlight Ruby %}
rake import:news
{% endhighlight %}
