---
layout: post
title: "URLs Amigáveis   .htaccess"
date: 2008-11-09 18:48:48
comments: true
description: "URLs Amigáveis - .htaccess"
keywords: "php, regex, .htaccess, url amigável"
categories: php
tags:
- php
- regex
- .htaccess
---

Urls amigáveis são as urls “semanticamente corretas”: são compreensíveis e passam uma informação relevante (e importante) aos usuários. Além da vantagem estética, as urls amigáveis ajudam as máquinas de busca na indexação, e são interessantes no sentido de ocultar a tecnologia que está sendo utilizada.

Por exemplo:
www.meusite.com.br/exibeFotos.php?idAlbum=2&idFoto=35

Nesta url, vê-se que seu site utiliza o php, além de serem exibidas explicitamente duas variáveis GET.

Com a url amigável, você poderia deixar:
`www.meusite.com.br/exibe-fotos/2/35`

ou ainda melhor:

`www.meusite.com.br/exibe-fotos/nome-da-foto-de-teste`

Vou explicar aqui um modo de fazer, utilizando o arquivo .htaccess e php.

O .htaccess é um arquivo do apache, utilizado, dentre outras coisas, para dar permissão de acesso à pastas do servidor. No nosso caso, ele será o “tradutor” das urls.

O .htaccess não transforma a url comum em url amigável. Ao contrário, traduz a url amigável em url “normal”. O que ele faz basicamente é ler a url do browser do usuário (a url amigável), enviada na forma de requisição para o servidor web, e tenta “casar” a url com uma séria de regras (regex). Ao encontrar uma regra compatível com a url, ele traduz para o termo correspondente, ou seja, a url normal. E é esta url que é enxergada pelo php no servidor.

Para começar, crie (ou altere caso já exista) o .htaccess da pasta raiz de seu site (sugiro que leiam um pouco sobre o .htaccess antes de prosseguir). No seu .htaccess ficarão as regras de reescrita, ou rewrite rules.

OBS: lembre-se de ativar o mod rewrite no seu servidor, para que as regras funcionem.

Vamos ao nosso .htaccess. Para que nosso exemplo funcione, coloque o seguinte:

{% highlight Apache %}
RewriteEngine on
RewriteRule ^exibe-fotos/([0-9]+)\/([0-9]+)\/?$ exibeFotos.php?idAlbum=$1&idFoto=$2
{% endhighlight %}
Pronto! Não vou explicar as expressões regulares, mas vamos tentar entender o que foi feito:

`RewriteRule ^` inicia uma regra de reescrita. Tudo o que aparece do `^` até o `$` é uma expressão regular. É essa expressão que deverá casar com a url amigável que você utilizará em seu site.

Comparem a url com a expressão regular. No nosso caso, nossa url é a seguinte:
`www.meusite.com.br/exibe-fotos/2/35`

Expressão que casa com o padrão:
`exibe-fotos/([0-9]+)\/([0-9]+)\/`

Nossa url possui a string `exibeFotos`, seguidos de dois números. Como a url casa corretamente com a regra, o endereço será convertido na segunda parte:
`exibe-fotos.php?idAlbum=$1&idFoto=$2`

Basicamente é isso. Não vou explorar mais como fazer as regras de reescrita. Agora vamos às implicações sobre o site.

Se seu site já estava pronto, você deverá substituir TODAS os links de seu site pelas novas urls. Por exemplo, se você tiver um link na sua pagina da seguinte forma:

{% highlight Html %}
<a href="xxxx.php?id=23&x=5">Ir</a>
{% endhighlight %}

Você deverá trocar pela correspondente url amigável. Isso parece idiota, mas muita gente tem dificuldade de entender que o `.htaccess` não vai transformar as url do seu site. É você que deve fazer isso manualmente. Ele apenas pegará essa nova url e traduzirá para a url normal.

Outro ponto que dará bastante trabalho: suponha que sua url ficou da seguinte forma:
`www.meusite.com.br/exibe-fotos/2/35`
e que seu .htaccess está traduzindo corretamente essa url.

Então para a parte da aplicação que roda no servidor está tudo bem. O php receberá todas as referências do arquivo corretamente. Mas o html é interpretado no browser do usuário. Isso quer dizer que o browser não saberá interpretar corretamente as referências de arquivos, pois não sabe que suas regras de reescrita existem.

Resumindo, isso acontecerá com todos as suas referências no html à imagens, arquivos css e arquivos de mídia.

Por exemplo, se você chama a imagem: `<img src=”img/teste.jpg” />` no arquivo `exibeFotos.php`, o browser interpretará sua url amigável como vários níveis de diretórios. Então ele irá procurar a imagem que você colocou na seguinte pasta no servidor:
`exibeFotos/2/35/img/teste.jpg`, e não encontrará o arquivo.

Uma forma de resolver isso é colocando as referências absolutas ou a url completa. No nosso caso:
{% highlight Html %}
<img src=”http://www.meusite.com.br/img/teste.jpg” />
{% endhighlight %}

Para fazer isso de forma a não comprometer seu site, fazendo-o dependente do domínio, sugiro um código simples em php para pegar a url correta:

{% highlight Php %}
<?php
$server = $_SERVER['SERVER_NAME'];
$urlPrincipal = "http://".$server."/";
?>
{% endhighlight %}

Nas suas referências, coloque:

{% highlight Html %}
<img src="<?=$urlPrincipal?>img/teste.jpg" />
{% endhighlight %}

Pronto. Algumas referências:

<a href="http://forum.imasters.uol.com.br/index.php?showtopic=203965
" target="_blank">http://forum.imasters.uol.com.br/index.php?showtopic=203965</a>

<a href="http://www.vivaolinux.com.br/dica/Utilizando-URL-Amigavel-no-Apache" target="_blank">http://www.vivaolinux.com.br/dica/Utilizando-URL-Amigavel-no-Apache</a>
