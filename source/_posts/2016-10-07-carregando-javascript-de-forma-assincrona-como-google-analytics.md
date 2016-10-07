---
layout: post
title: "Carregando Javascript de forma assíncrona como o Google Analytics"
date: 2016-10-07 7:52:38
comments: true
description: "Carregando Javascript de forma assíncrona como o Google Analytics"
keywords: "javascript, assíncrono, google analytics"
categories: tecnicas
tags:
- javascript
- google analytics
- assíncrono
- técnicas
---

Suponha que você precise criar um plugin como o Google Analytics: uma função de *track* em Javascript que consumirá uma API via Ajax. Seu arquivo Javascript terá cerca de 20kb e será utilizado em milhares de sites. Tem-se então um requisito importante: o carregamento do script não pode impactar no tempo de resposta normal dos sites, já que não se tem domínio sobre o impacto que isso trará em cada caso.

A forma mais óbvia de fazer o carregamento é utilizando o <a href="http://www.w3schools.com/tags/att_script_async.asp" target="_blank">atributo async na tag de script</a>:
{% highlight HTML %}
<script src="//cdn.com/my_async_plugin.js" async></script>
{% endhighlight %}


#### Problema: Compatibilidade com browsers antigos
Infelizmente o atributo async não é compatível com browsers mais antigos.
![Compatibilidade do async com browsers]({{ site.url }}/assets/compatibilidade-async.png)

Podemos partir para uma solução mais interessante: manipular o DOM para criar dinamicamente uma tag `<script>`:

{% highlight JS %}
(function() {
  var tag_script = document.createElement('script');
  tag_script.type = 'text/javascript';
  tag_script.async = true;
  tag_script.src = '//cdn.com/my_async_plugin.js';
  var s = document.getElementsByTagName('script')[0]; 		     
  s.parentNode.insertBefore(tag_script, s);
})();
{% endhighlight %}

<a href="http://www.w3schools.com/jsref/dom_obj_script.asp" target="_blank">Veja aqui mais detalhes sobre HTML DOM Script Object</a> e quais propriedades podem ser manipuladas.

Independente do atributo async `tag_script.async = true` utilizado, só o fato desse script ser executado após a renderização inicial da página já torna o carregamento do script remoto `//cdn.com/my_async_plugin.js` assíncrono.

#### Problema: Utilizar métodos que ainda não foram carregados
Outro problema é a necessidade de se utilizar métodos contidos no Javascript que ainda não foi carregado (já que seu loading é assíncrono).
Por exemplo, a chamada da função `my_track('acessou página')` a seguir provavelmente falhará, já que o browser ainda desconhece a função `my_track`:

{% highlight HTML %}
<script>
  (function() {
    var tag_script = document.createElement('script');
    tag_script.type = 'text/javascript';
    tag_script.async = true;
    tag_script.src = '//cdn.com/my_async_plugin.js';
    var s = document.getElementsByTagName('script')[0]; 		     
    s.parentNode.insertBefore(tag_script, s);
  })();
</script>

<script>
  my_track('acessou página');
</script>
{% endhighlight %}


Uma solução é declarar uma assinatura dessa função para que o browser reconheça a chamada como válida. Quando o script for completamente carregado, substituimos a função *fake* de declaração pela função real.

É importante observar que quando o script for completamente carregado, as chamadas que já foram feitas à função *fake* seriam simplesmente ignoradas, já que o browser já as executou. Podemos então criar uma função global (no window) que armazene as chamadas feitas à função *fake*, para que sejam recuperadas quando a função *real* for carregada. Vamos armazenar as chamadas feitas à função *fake* em uma fila:

{% highlight JS %}
(function() {
  window['my_track'] = window['my_track'] || function() {
    (window['my_track'].q = window['my_track'].q || []).push(arguments)
  }

  var tag_script = document.createElement('script');
  tag_script.type = 'text/javascript';
  tag_script.async = true;
  tag_script.src = '//cdn.com/my_async_plugin.js';
  var s = document.getElementsByTagName('script')[0]; 		     
  s.parentNode.insertBefore(tag_script, s);
})();
{% endhighlight %}


Parece complicado, mas basicamente utilizamos o objeto `window['my_track']` caso já exista ou criamos um novo com `function() { ... }`. Dentro da função criamos um atributo `q` que armazenará nossa queue/fila, e damos um push para enfileirar os argumentos passados. <a href="https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Functions/arguments" target="_blank">Veja aqui para saber mais sobre o objeto `arguments`</a>.

Por exemplo, debugando o valor armazenado em `q` para duas chamadas ao método `my_function`:
{% highlight JS %}
// ao carregar a página
// window['my_track'].q -> []

my_track('teste 1');
// window['my_track'].q -> ['teste 1']

my_track('teste 2');
// window['my_track'].q -> ['teste 1', 'teste 2']
{% endhighlight %}


Agora o script remoto `my_async_plugin.js`, quando carregado, precisa desenfileirar as chamadas armazenadas em `q`. O arquivo fica da seguinte forma:

{% highlight JS %}
// FILENAME: my_async_plugin.js
(function () {
  q = window['my_track'].q;
  while(a = q.shift()){
    my_real_track(a);
  }

  my_track = my_real_track;
})();

// função real
function my_real_track(params) {
  console.log("executando funcao real vinda do arquivo remoto");
  console.log(params);
}
{% endhighlight %}

Pegamos as chamadas à função *fake* armazenadas em `q` e fazemos as chamadas à função *real*:
{% highlight JS %}
q = window['my_function'].q;
while(a = q.shift()){
  my_real_function(a);
}
{% endhighlight %}

Em seguida, precisamos "substituir" a função *fake* pela *real*, para que chamadas subsequentes já sejam feitas seguindo o que está definido na função *real*:
my_track = my_real_track;

### Pronto!

Para deixar o código bacana podemos tirar as quebras de linha e criar algumas variáveis mais compactas:

{% highlight JS %}
(function(t,r,a,c,k) { t[k]=t[k]||function(){ (t[k].q = t[k].q||[]).push(arguments) }
var ts=r.createElement(a);ts.type='text/javascript';ts.async=true;
ts.src=c; var s=r.getElementsByTagName(a)[0];s.parentNode.insertBefore(ts, s); })(window, document,
'script','//rawgit.com/Godoy/776586faf5389d2276aebc179801c0e7/raw/441601291b272a1b5bea191b54e13cce159d7dbf/my_async_plugin.js', 'my_track')
{% endhighlight %}

Quase um Google Analytics.. 😎
{% highlight JS %}
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','https://www.google-analytics.com/analytics.js','ga');
{% endhighlight %}


### Código completo e Demo
Gist: <a href="https://gist.github.com/Godoy/776586faf5389d2276aebc179801c0e7" target="_blank">
https://gist.github.com/Godoy/776586faf5389d2276aebc179801c0e7</a>
JSFiddle: <a href="https://jsfiddle.net/adrianogodoy/2o80q2w4/1/" target="_blank">
https://jsfiddle.net/adrianogodoy/2o80q2w4/1/</a>
