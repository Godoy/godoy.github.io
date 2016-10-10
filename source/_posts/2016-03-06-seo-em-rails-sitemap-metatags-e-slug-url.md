---
layout: post
title: "SEO em Rails: Sitemap Metatags e Slug url"
date: 2016-03-06 10:08:12
comments: true
description: "SEO em Rails: Sitemap Metatags e Slug url"
keywords: "friendly_id, ruby on rails, seo, sitemap_generator, truncate_html"
categories: ruby-on-rails
tags:
- friendly_id
- ruby on rails
- seo
- sitemap_generator
- truncate_html
---

Neste post vou apresentar algumas dicas básicas de SEO e aplicá-las em um projeto Ruby on Rails explicando cada passo. Já existem vários blogs com dicas de SEO (<a href="http://www.seomaster.com.br/blog" target="_blank">SEO Master</a>, <a href="http://www.agenciamestre.com/blog/" target="_blank">Agência Mestre</a>, <a href="https://moz.com/blog" target="_blank">MOZ</a>, etc), por isso focaremos em como aplicar as técnicas e mostrar quais gems podem nos auxiliar.

O resultado dos passos aqui descritos resultaram neste projeto no Github: <a href="https://github.com/adrianogodoy/rails-seo" target="_blank">https://github.com/adrianogodoy/rails-seo</a>. Após ler este post, se ainda restarem dúvidas, veja o log dos commits no github ou comente 😉


## Criando a aplicação base para aplicar as técnicas

Para aplicar as técnicas, vamos criar um site com uma administração de conteúdo básica. Criaremos os tipos Event (eventos), News (notícias) e relacionaremos esses itens com o model Category (categorias).

Não nos preocuparemos com autenticação de usuários e validações pois não são relevantes para o conteúdo abordado neste post.

Comece criando o projeto no `Terminal`:

{% highlight Ruby %}
rails new rails-seo
{% endhighlight %}

Adicionaremos as gems `RailsAdmin` e `Paperclip`  apenas para facilitar a inserção de conteúdo no site. Vamos também adicionar a gem `Bootstrap-Sass`.

{% highlight Ruby %}
gem "paperclip", "~> 4.2"
gem "rails_admin"
gem "bootstrap-sass", "~> 3.3.3"
{% endhighlight %}

No terminal, instale o `rails_admin`:

{% highlight Ruby %}
rails g rails_admin:install
{% endhighlight %}

Criando os models:

{% highlight Ruby %}
rails g model Category name
rails g model News title category:references content:text image:attachment
rails g model Event title category:references content:text image:attachment start_date:datetime end_date:datetime place

rake db:migrate
{% endhighlight %}

Os models ficam da seguinte forma:

Arquivo `app/models/category.rb`:

{% highlight Ruby %}
class Category < ActiveRecord::Base
  has_many :events
  has_many :news
end
{% endhighlight %}


`app/models/event.rb`:
{% highlight Ruby %}
class Event < ActiveRecord::Base
  belongs_to :category

  has_attached_file :image, :styles => { :medium => "300x300>", :thumb => "100x100>" }, :default_url => "/images/:style/missing.png"
  validates_attachment_content_type :image, :content_type => /\Aimage\/.*\Z/
end
{% endhighlight %}

`app/models/news.rb`:

{% highlight Ruby %}
class News < ActiveRecord::Base
  belongs_to :category

  has_attached_file :image, :styles => { :medium => "300x300>", :thumb => "100x100>" }, :default_url => "/images/:style/missing.png"
  validates_attachment_content_type :image, :content_type => /\Aimage\/.*\Z/
end
{% endhighlight %}

Neste ponto já podemos cadastrar conteúdo no admin. Sugiro carregar conteúdo via seed – <a href="http://godoy.net.br/ruby-on-rails/2015/importacao-de-conteudo-e-seed-no-rails/">veja neste post como criar o seed com lorem ipsum</a>.

Agora criaremos as rotas, views e controllers para a página home, lista e interna de noticias, lista e interna de eventos.

{% highlight Ruby %}
rails g controller pages home
rails g controller news index show
rails g controller events index show
{% endhighlight %}

No arquivo de rotas, aponte o root para a home e deixe as rotas da seguinte forma:

`config/routes.rb`:
{% highlight Ruby %}
Rails.application.routes.draw do
  root 'pages#home'

  get 'events' => 'events#index', as: :events
  get 'events/:id' => 'events#show', as: :event

  get 'news' => 'news#index', as: :news_index
  get 'news/:id' => 'news#show', as: :news

  mount RailsAdmin::Engine => '/admin', as: 'rails_admin'
end
{% endhighlight %}

Após configurar as rotas, inclua os scripts e css do Bootstrap no projeto. Siga os passos descritos na gem ou veja as modificações realizadas <a href="https://github.com/Godoy/rails-seo/commit/18cbd0f457dea06c755b50af549d56b7fb9af389" target="_blank">neste commit</a>.

Neste ponto já é possível acessar as páginas de eventos e notícias. Estamos prontos para começar a aplicar as melhorias.

## Títulos e Headers

O título da página é um dos elementos mais importantes para compor o SEO. Ele diz aos usuários (e aos buscadores) qual conteúdo será apresentado na página. Para o title é indicado um tamanho máximo de 70 caracteres (cerca de 10 palavras). Além disso, é recomendado que cada página tenha um título único que a distingua das outras páginas.

Um erro comum é a repetição ou o uso indiscriminado da tag h1. Ela deve ocorrer uma única vez por página. Portanto não é aconselhável colocar o nome do site, no header, dentro de uma tag h1. Deixe a tag para o título do conteúdo que está sendo apresentado na página. Subtítulos, sub-seções e afins podem ser expressados com as tags h2, h3, etc. Estas podem se repetir na página.

Para facilitar a definição do título e header, utilizaremos a gem meta-tags. Adicione a gem ao seu Gemfile. Em seguida, no layout, substitua a linha da tag <title> pela seguinte:

`app/views/layouts/application.html.erb`:
{% highlight ERB %}
<%= display_meta_tags :site => 'SEO no Rails', :reverse => true %>
{% endhighlight %}

O valor de “site” corresponde ao nome do seu site. Utilizei a opção “reverse” true para que o nome do site seja repetido depois do título da página. Ficaria algo como:

*Título de exemplo da página \| SEO no Rails*

Além disso, em cada view, coloque a tag h1:

{% highlight ERB %}
<h1><%= title %></h1>
{% endhighlight %}

Nos controllers defina o valor do title. As actions de eventos, por exemplo, ficam da seguinte forma:

`app/controllers/events_controller.rb`:
{% highlight Ruby %}
  def index
    @events = Event.all
    @page_title = 'Eventos'
  end

  def show
    @page_title = @event.title
  end
{% endhighlight %}

Simples! Veja <a href="https://github.com/Godoy/rails-seo/commit/51b180b5b32d5a9e18858c3c5a34f77edebb36e7" target="_blank">neste commit</a> as alterações feitas em nosso projeto neste passo.

## Metatags

Precisamos também definir nossas metatags: description, keywords, além das metatags do facebook. A description não é tão relevante para rankeamento quanto o título, mas é extremamente importante para ganhar o clique do usuário no resultado de busca. O indicado é que a description possua entre 150 e 160 caracteres.

Da mesma forma, usaremos a gem meta-tags:

{% highlight Ruby %}
class NewsController < ApplicationController
  include ActionView::Helpers::TextHelper

...

  def show
    @page_title = @news.title
    @page_description = truncate(@news.content, length: 150, omission: '...')
    @page_keywords    = @news.title.gsub ' ', ', '

    set_meta_tags :og => {
      :title    => @page_title,
      :image    => request.base_url+@news.image.url,
      :description => @page_description
    }
  end
{% endhighlight %}

Para utilizar o helper truncate no texto, inclua-o no controller (linha 2). Apenas para exemplificar, no campo Keywords quebrei o título separando as palavras com vírgulas. Para um projeto real, torne isso gerenciável. Crie um novo campo “keywords” onde o administrador possa cadastrar keywords específicas para cada conteúdo. Faça o mesmo com a description. Ao invés de utilizar os 150 primeiros caracteres (que podem não ter nada relevante sendo dito), crie um campo administrável, assim o usuário pode inserir um texto mais interessante para chamar os usuários para visitar a página. <a href="https://github.com/Godoy/rails-seo/commit/5f34929a7b50b6d9d4c40fc2f9730336ee88b0c5" target="_blank">Commit com as alterações</a>

## URL Amigável

Além da url ser um fator muito relevante para o rankeamento da página nos resultados de busca, ela é outro importante mecanismo para transmitir ao usuário qual será o conteúdo da página. (veja <a href="http://godoy.net.br/php/2008/urls-amigaveis---htaccess/" target="_blank">neste post como fazer em PHP utilizando .htaccess</a> – old but gold 😀 )

Para desenvolvermos esta feature no rails, mais uma vez faremos uso de uma gem: FriendlyId. No gemfile, adicione:

{% highlight Ruby %}
Gemfile

gem 'friendly_id', '~> 5.1.0'
{% endhighlight %}

Execute o bundle install e em seguida o comando:

{% highlight Ruby %}
rails generate friendly_id
{% endhighlight %}

Este comando irá gerar, além do initializer, uma migration para criação de uma tabela onde serão armazenados os slugs e o histórico de alterações com correspondências de slugs. Precisamos também criar o campo slug nos nossos tipos (notícias, eventos e categorias). Execute as migrations:

{% highlight Ruby %}
rails generate migration add_slug_to_news slug:string:uniq
rails generate migration add_slug_to_events slug:string:uniq
rails generate migration add_slug_to_categories slug:string:uniq

rake db:migrate
{% endhighlight %}

Agora podemos decorar os models para sinalizar qual campo devem utilizar para a criação do slug, como por exemplo no model News, que utilizaremos o campo title:

`app/models/news.rb`:
{% highlight Ruby %}
class News < ActiveRecord::Base
...

  extend FriendlyId
  friendly_id :title, use: :slugged
end
{% endhighlight %}

No caso de eventos, é possível fazer um combo bacana na geração dos slugs, concatenando a data com o título do evento:

`app/models/event.rb`:
{% highlight Ruby %}
class Event < ActiveRecord::Base
...

  extend FriendlyId
  friendly_id :my_method_slug, use: :slugged

  def my_method_slug
    start_date.strftime("%m/%d/%Y")+" - #{title}"
  end
end
{% endhighlight %}

Como já havíamos cadastrado nosso conteúdo de teste, precisamos salvar novamente todos os registros para que seus slugs sejam gerados. O comando para ser executado no rails console é simples. Veja <a href="https://gist.github.com/Godoy/6564097" target="_blank">neste gist</a>.

Na listagem de notícias (http://localhost:3000/news) já é possível ver a nova URL sendo automaticamente gerada passando o slug ao invés do id. Agora só precisamos alterar a forma como os registros são buscados no controller:

`app/controllers/events_controller.rb`:

{% highlight Ruby %}
class EventsController < ApplicationController
...

  private
    def set_event
      @event = Event.friendly.find(params[:id])
    end
end
{% endhighlight %}

Done!

OBS: Para impedir que o slug gerado automaticamente seja sobrescrito ou apagado pelo usuário no rails_admin, exclua o campo slug dos formulários no rails_admin inserindo o seguinte no seu model:

`app/models/event.rb`:
{% highlight Ruby %}
class Event < ActiveRecord::Base
...

  rails_admin do
    create do
      exclude_fields :slug
    end
    edit do
      exclude_fields :slug
    end
  end
end
{% endhighlight %}

<a href="https://github.com/Godoy/rails-seo/commit/c3df3da6377ee6ebc094de8f09f83c8a154d48cd" target="_blank">Clique aqui</a> para ver o commit com as alterações.

## Sitemap

O <a href="https://support.google.com/webmasters/answer/183668?hl=en" target="_blank">sitemap</a> é uma forma na qual o webmaster pode auxiliar (e agilizar) a indexação do site. Permite sugerir aos robôs de busca quais páginas devem ser indexadas, qual frequência de rastreamento, data da última modificação da página e a prioridade em relação às outras páginas do arquivo.

Utilizaremos a gem `Sitemap Generator`. Adicione a gem ao seu Gemfile e execute o comando:

{% highlight Ruby %}
rake sitemap:install
{% endhighlight %}

No arquivo de configuração gerado, vamos inserir nossas páginas e rotas para os conteúdos:

`config/sitemap.rb`:
{% highlight Ruby %}
# Set the host name for URL creation
SitemapGenerator::Sitemap.default_host = "http://localhost:3000"

SitemapGenerator::Sitemap.create do

  add news_path, :priority => 0.7, :changefreq => 'daily'
  News.find_each do |item|
    add news_path(item), :lastmod => item.updated_at
  end

  add events_path, :priority => 0.7, :changefreq => 'daily'
  Event.find_each do |item|
    add event_path(item), :lastmod => item.updated_at
  end
end
{% endhighlight %}

A página inicial já é incluida por default. Execute o seguinte para gerar o sitemap baseado nas configurações:

{% highlight Ruby %}
rake sitemap:refresh
{% endhighlight %}

E confira o seu sitemap gerado em `/public/sitemap.xml.gz`:

{% highlight XML %}
...
  <url>
      <loc>http://localhost:3000</loc>
    <lastmod>2015-03-06T21:49:01-03:00</lastmod>
    <changefreq>always</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>http://localhost:3000/noticias</loc>
    <lastmod>2015-03-06T21:49:01-03:00</lastmod>
    <changefreq>daily</changefreq>
    <priority>0.7</priority>
  </url>
  <url>
    <loc>http://localhost:3000/noticias/optio-minima-exercitationem-dolor</loc>
    <lastmod>2015-03-07T00:19:29+00:00</lastmod>
    <changefreq>weekly</changefreq>
    <priority>0.5</priority>
  </url>
...
{% endhighlight %}

Veja o <a href="https://github.com/Godoy/rails-seo/commit/56cad016d28fee5fc83711408ebf60a1d5309a53" target="_blank">commit</a> com as alterações para Sitemap.

## Crie conteúdo e monitore!

Com as dicas básicas dadas neste artigo sua página estará em boas condições de aparecer nas buscas dos usuários pelo conteúdo do seu site. Lembre-se: antes de qualquer técnica, seu site precisa ter conteúdo!

As técnicas de SEO existem para tornar claro qual o conteúdo de seu site e permitir que os buscadores exponham seu conteúdo quando for relevante para a busca feita.

Faça o monitoramento de seu site. Acompanhe no Google Analytics o perfil de seu visitante, quais páginas são mais acessadas e as origens de tráfego.

Cadastre seu site no Google Webmasters (http://www.google.com.br/webmasters/) e receba feedbacks de melhorias e erros no seu site.

Em breve farei um post sobre microdados e utilizarei este mesmo projeto como base!

Dúvidas e sugestões são bem vindas 😉
