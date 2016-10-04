---
layout: post
title: "Gist: Helper para adicionar classe 'active' a um link de menu"
date: 2016-01-14 21:50:50
comments: true
description: "Gist: Helper para adicionar classe 'active' a um link de menu"
keywords: "gist, helper, ruby on rails"
categories: ruby-on-rails
tags:
- gist
- ruby on rails
- menu active
- helper
---

Helper que adiciona a class `active` ao `li` quando a página atual é igual ao path passado como parâmetro ou quando o controller passado como parâmetro (opcional) é o controller corrente.

{% highlight Ruby %}
module ApplicationHelper
  def menu_li_link(content, path, controller="")

    options = current_page?(path) || controller_name == controller ? { class: "active" } : {}
    content_tag(:li, options) do
      link_to content, path
    end
  end
end
{% endhighlight %}

### Exemplos de uso

- Passando o path a ser comparado:
{% highlight ERB %}
<%= menu_li_link 'Texto com link', page_url_path %>
{% endhighlight %}

- Passando o controller:
{% highlight ERB %}
<%= menu_li_link 'Texto com link 2', page_url_path, "controller_name_all_actions" %> <!-- considers all actions in controller as current -->
{% endhighlight %}

## Gist
{% gist Godoy/fb6d23e3039c15fe0d37 %}
