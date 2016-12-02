---
layout: post
title: "Paginação com scroll infinito em Rails utilizando will_paginate"
date: 2016-12-01 22:05:42
comments: true
description: "Paginação com scroll infinito em Rails utilizando will_paginate"
keywords: "paginação, scroll infinito, gem, rails"
categories: ruby-on-rails
tags:
- paginação
- scroll infinito
- gem
- ruby on rails
- will_paginate
---

Paginação no estilo _"infinite scroll"_ foi uma tendência ditada principalmente pelas timelines de redes sociais e uso cada vez mais comum de dispositivos móveis. Em telas estreitas, o conteúdo é disposto mais verticalmente, o que torna a rolagem algo essencial na navegação. Nada mais simples que antecipar o carregamento de conteúdo enquanto o usuário faz o scroll da página, melhorando sua experiência ao navegar.

Em feeds de redes sociais, a quantidade e diversidade de conteúdo tornam inviável uma paginação normal. Além disso, para as redes sociais, quanto mais tempo o usuário passar navegando, melhor. É como em um shopping center: não há janelas ou relógios nas paredes. A diferença é que nas redes sociais você é o produto.

Outra aplicação comum do scroll infinito é para paginação de logs. O usuário não quer se preocupar em qual página está - precisa apenas analisar uma quantidade grande de dados dispostos cronologicamente. Novos registros são inseridos no topo do log fazendo naturalmente com que conteúdos mais antigos desçam na página.

É fácil encontrar códigos prontos de paginação com scroll infinito no Stack Overflow ou blogs de programação. Ao mesmo tempo, perder tempo encaixando pedaços de código e modificando uma paginação comum já em funcionamento é uma perda de tempo desnecessária. Essa foi a motivação para criar a gem <a href="http://bit.ly/will_paginate_infinite" target="_blank">will_paginate_infinite</a>, que apenas modifica a paginação padrão da popular gem <a href="https://rubygems.org/gems/will_paginate" target="_blank">will_paginate</a>.

# Instalação
Inclua a gem no Gemfile:

{% highlight Ruby %}
gem 'will_paginate_infinite'
{% endhighlight %}

E execute `bundle install`.


Adicione a referência ao manifesto css:
`app/assets/stylesheets/application.css`:
{% highlight Ruby %}
*= require will_paginate_infinite
{% endhighlight %}

E ao Javascript:
`app/assets/javascripts/application.js`:
{% highlight Ruby %}
//= require will_paginate_infinite
{% endhighlight %}


# Configuração
Para facilitar demonstração utilizaremos um `scaffold` de notícias `rails g scaffold News title`. A view onde está a lista de conteúdo ficará da seguinte forma:

`app/views/news/index.html.erb`:
{% highlight ERB %}
<h1>News List</h1>

<div class="list-news">
  <ul>
    <% @news.each do |item| %>
      <%= render item %>
    <% end %>
  </ul>

  <%= will_paginate @news, renderer: WillPaginateInfinite::InfinitePagination %>
</div>
{% endhighlight %}
O principal ponto aqui é passar o renderer como argumento para o will_paginate: `WillPaginateInfinite::InfinitePagination`


Na `partial` da notícia colocaremos apenas o título:

`app/views/news/_news.html.erb`
{% highlight ERB %}
<li>
  <%= item.title %>
</li>
{% endhighlight %}

O controller com os itens a serem paginados fica da seguinte forma:

{% highlight Ruby %}
# app/controllers/news_controller.rb
class NewsController < ApplicationController
  def index
    @news = News.order(:created_at).paginate(:page => params[:page], :per_page => 30)
    # ...

    respond_to do |format|
      format.html
      format.js
    end
  end
end
{% endhighlight %}

E, finalmente, crie uma versão javascript da view de listagem de notícias:

{% highlight ERB %}
# app/views/news/index.js.erb
<%= infinite_append ".list-news ul", @news %>
{% endhighlight %}

ou

{% highlight ERB %}
# app/views/news/index.js.erb
<%= infinite_append ".list-news ul", { partial: "news/news", collection: @news }  %>
{% endhighlight %}

Note que `.list-news ul` é o seletor do elemento container onde as `partials` deverão ser inseridas no html.

## Exemplo
Código de exemplo no Github utilizando a gem `will_paginate_infinite` com Rails 4: <a href="https://github.com/Godoy/will_paginate_infinite_example" target="_blank">github.com/Godoy/will_paginate_infinite_example</a>
