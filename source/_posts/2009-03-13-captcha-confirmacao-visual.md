---
layout: post
title: "Captcha - Confirmação Visual"
date: 2009-03-13 20:27:11
comments: true
description: "Captcha - Confirmação Visual"
keywords: "captcha, gd, php"
categories: php
tags:
- gd
- php
- captcha
---

Depois de um bom tempo com o blog parado, vou mostrar um modo de desenvolver um <a href="https://pt.wikipedia.org/wiki/CAPTCHA" target="_blank">captcha</a> utilizando a biblioteca GD do php. O captcha é um <a href="https://pt.wikipedia.org/wiki/Teste_de_Turing" target="_blank">teste de turing</a> reverso, pois é a máquina que testa se o usuário é humano. Este teste evita que sistemas automatizados enviem spans pelos formulários de seu site.

A idéia é bem simples. Vamos ter um arquivo php que gera uma imagem, podendo ser exibido como se de fato fosse uma imagem:

{% highlight Html %}
<img src="geraImagemConfirmacao.php" alt="Captcha" />
{% endhighlight %}

Essa imagem será gerada, e o conteúdo gravado em um cookie. Este cookie será utilizado na ação de seu formulário, quando deverá ser comparado com o texto digitado pelo usuário. Vou procurar fazer o mais simples possível:

{% highlight Html %}
<form action="acaoConfirmaCodigo.php" method="post">
<img src="geraImagemConfirmacao.php" alt="Captcha" />
<input name="palavraUsuario" type="text" />

<input type="submit" />

</form>
{% endhighlight %}

Salve este arquivo com um nome qualquer.
No arquivo `acaoConfirmaCodigo.php`, coloque da seguinte forma:

{% highlight Php %}
<?php

$palavraUsuario = strtolower($_POST['palavraUsuario']);
$palavraGerada = strtolower($_COOKIE['palavraGerada']);

if($palavraUsuario == $palavraGerada)
echo "Confirmação visual correta.";
else
echo "Por favor, tente novamente.";
?>
{% endhighlight %}

O que este arquivo faz nada mais é do que pegar a palavra digitada pelo usuário (enviada pelo POST) e a palavra armazenada no cookie, passando ambas para a forma minúscula, e então comparando-as.

Agora vamos à parte realmente interessante, o arquivo `geraImagemConfirmacao.php`:

{% highlight PHP %}
<?php

$larguraImg = 100;
$alturaImg = 30;

$espacoMinLetrasX = 15;
$espacoMaxLetrasX = 20;

$espacoMinLetrasY = 5;
$espacoMaxLetrasY = 10;

$numLetras = 4;

$letras = array('2','3','4','5','6','7','8','9', 'A', 'B', 'C', 'D', 'E', 'Z', 'K', 'M', 'X');

// cria uma imagem
$imagem = imagecreate($larguraImg, $alturaImg);

/* Cores para utilizar na imagem imagem */
$cinza = imagecolorallocate($imagem,0xF8,0xF8,0xF8);
$cinza_escuro = imagecolorallocate($imagem,0xAC,0xAC,0xAC);
$vermelho = imagecolorallocate($imagem,0xFF,0×00,0×00);
$azul = imagecolorallocate($imagem,0x0F,0×93,0xFF);
$verde = imagecolorallocate($imagem,0×00,0×66,0×00);
$preto = imagecolorallocate($imagem,0×00,0×00,0×00);
$laranja = imagecolorallocate($imagem,0xFF,0x8C,0×24);

$cores = array($vermelho, $azul, $verde, $preto);

$tamanho_letras = count($letras)-1;
$tamanho_cores = count($cores)-1;

/* Escrevendo as letras… */
$palavraGerada = "";
$x = 0;

for($i=0;$i<$numLetras;$i++){

$x += rand($espacoMinLetrasX, $espacoMaxLetrasX); //localizacao horizontal
$y = rand($espacoMinLetrasY, $espacoMaxLetrasY);

$j = rand(0,$tamanho_cores);

$k = rand(0,$tamanho_letras);
$palavraGerada .= $letras[$k];

imagestring($imagem, 5, $x, $y, $letras[$k], $cores[$j]); //escreve a letra na imagem
}

setcookie('palavraGerada', $palavraGerada, time()+7200);

header("Content-type: image/jpeg");
imagejpeg($imagem);
imagedestroy($imagem);
{% endhighlight %}

A primeira parte é apenas para configurar a imagem que será gerada:

{% highlight Php %}
$larguraImg = 100;
$alturaImg = 30;
{% endhighlight %}

Note que essas dimensões têm que ser compatíveis com a quantidade de letras e o espaçamento das letras que você deseja gerar, caso contrário algumas letras ficarão fora da imagem simplesmente por falta de espaço.

{% highlight Php %}
$espacoMinLetrasX = 15;
$espacoMaxLetrasX = 20;
$espacoMinLetrasY = 5;
$espacoMaxLetrasY = 10;
$numLetras = 4;
{% endhighlight %}

Aqui são determinados os intervalos das posições das letras na imagem. Por exemplo, a posição horizontal (espaço entre as letras) irá variar de 15 a 20px. Para realizar essa variação, utilizamos a função rand(min, max) do php.
E também aqui é determinada a quantidade de letras que serão geradas na imagem.

{% highlight Php %}
$letras = array('2','3','4','5','6','7','8','9', 'A', 'B', 'C', 'D', 'E', 'Z', 'K', 'M', 'X');
{% endhighlight %}

Esse array contém as letras que poderão ser sorteadas e aparecer na imagem. Procure evitar letras como O, I, L… e os números 0 e 1, pois podem ser confundidos com facilidade.

{% highlight Php %}
// cria uma imagem
$imagem = imagecreate($larguraImg, $alturaImg);

/* Cores para utilizar na imagem imagem */
$cinza = imagecolorallocate($imagem,0xF8,0xF8,0xF8);
$cinza_escuro = imagecolorallocate($imagem,0xAC,0xAC,0xAC);
$vermelho = imagecolorallocate($imagem,0xFF,0×00,0×00);
$azul = imagecolorallocate($imagem,0x0F,0×93,0xFF);
$verde = imagecolorallocate($imagem,0×00,0×66,0×00);
$preto = imagecolorallocate($imagem,0×00,0×00,0×00);
$laranja = imagecolorallocate($imagem,0xFF,0x8C,0×24);

$cores = array($vermelho, $azul, $verde, $preto);
{% endhighlight %}

Neste ponto, criamos a imagem, e associamos as cores que utilizaremos na imagem. Além disso, coloco no array $cores as cores que serão sorteadas para cada letra. (Note mais uma vez que você deve facilitar a vida do usuário, então utilize cores fortes nas letras).

{% highlight PHP %}
$palavraGerada = "";
$x = 0;
for($i=0;$i<$numLetras;$i++){

$x += rand($espacoMinLetrasX, $espacoMaxLetrasX); //localizacao horizontal
$y = rand($espacoMinLetrasY, $espacoMaxLetrasY);

$j = rand(0,$tamanho_cores);

$k = rand(0,$tamanho_letras);
$palavraGerada .= $letras[$k];

imagestring($imagem, 5, $x, $y, $letras[$k], $cores[$j]); //escreve a letra na imagem
}
{% endhighlight %}

Na iteração, para cada letra sortea-se a posição x e y, dentro do intervalo configurado. Note que o intervalo x deve ser incrementado a cada iteração, caso contrário todas as letras ficariam dentro de um mesmo intervalo na horizontal (experimente trocar o += por =, para ver como fica).

Também são sorteadas a letra e sua cor, dentro do array pré-determinado. Só então a letra é gravada na imagem, com a função `imagestring()` do php.

{% highlight PHP %}
setcookie('palavraGerada', $palavraGerada, time()+7200);

header("Content-type: image/jpeg");
imagejpeg($imagem);
{% endhighlight %}

Finalmente a imagem é gravada no cookie e criada na tela, utilizando o cabeçalho `Content-type: image/jpeg`. Pronto!

Se achar conveniente, você pode utilizar outras funções da GD como imagearc, imagelin.. para “dificultar” a visualização da imagem (cuidado para não deixar a imagem imcompreensível).
