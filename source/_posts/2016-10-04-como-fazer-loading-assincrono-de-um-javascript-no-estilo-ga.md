---
layout: post
title: "Como fazer loading assíncrono de um javascript no estilo GA"
date: 2016-10-04 21:52:38
comments: true
description: "Como fazer loading assíncrono de um javascript no estilo GA"
keywords: "javascript, assíncrono, google analytics"
categories: tecnicas
tags:
- javascript
- google analytics
- assíncrono
- técnicas
---

Suponha que seja necessário criar um plugin como o Google Analytics - seu arquivo javascript terá cerca de 20kb e será utilizado em milhares de site. Isto implica que o carregamento do script não pode impactar no carregamento normal dos sites, visto que não se tem domínio sobre o impacto que isso trará.

A forma mais óbvia de fazer o loading é utilizando o <a href="http://www.w3schools.com/tags/att_script_async.asp">atributo async na tag de script</a>:
{% highlight HTML %}
<script src="my_async_plugin.js" async></script>
{% endhighlight %}

Mas e a compatibilidade com browsers mais antigos?
![Compatibilidade do async com browsers]({{ site.url }}/assets/compatibilidade-async.png)

O problema é a necessidade de se utilizar os métodos do javascript no código sem ter garantia de que o script foi completamente carregado. Por exemplo, não seria prudente fazer a chamada logo em seguida, como:

{% highlight HTML %}
<script src="my_async_plugin.js" async></script>
<script>
  my_track('acessou página');
</script>
{% endhighlight %}


Uma opção seria declarar uma assinatura dessa função para que o browser a conheça.


{% highlight HTML %}
(function(w,d) {
  w['my_function'] = w['my_function'] || function() {
    (w['my_function'].q = w['my_function'].q || []).push(arguments)
  }
  var po = d.createElement('script'); po.type = 'text/javascript'; po.async = true;
  po.src = 'https://rawgit.com/Godoy/830e372b7aa73158d68633bd15acb781/raw/7aaf95e41b30ee72fc7504bdac3475bc66fbec31/my_function-sdk.js';
  var s = d.getElementsByTagName('script')[0]; s.parentNode.insertBefore(po, s);
})(window, document);
{% endhighlight %}
