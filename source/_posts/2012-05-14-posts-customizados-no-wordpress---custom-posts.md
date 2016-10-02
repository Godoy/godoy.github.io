---
layout: post
title: "Posts Customizados no Wordpress   Custom Posts"
date: 2012-05-14 21:51:09
comments: true
description: "Posts Customizados no Wordpress - Custom Posts"
keywords: "cms, custom posts, php, wordpress"
categories: php
tags:
- cms
- wordpress
- php
- custom posts
---

O “Custom Post” permite utilizar o wordpress como um “CMS”. Esse recurso possibilita que os posts sejam tratados como “tipos” distintos (Ex.: notícias, eventos, cursos, etc.). Dentre outras vantagens, o sistema administrativo fica muito mais organizado e o gerenciamento do conteúdo muito mais simples e intuitivo.

Vamos supor que um site qualquer em WordPress precise exibir disciplinas de um curso. Para registrar o tipo disciplina como um custom post, basta ir no arquivo `functions.php` do tema e adicionar o seguinte código:

{% highlight PHP %}
<?php
add_action('init', 'criar_tipos_personalizados');

function criar_tipos_personalizados() {
  register_post_type( 'exemplo_disciplina',
  array(
    'labels' =>
    array(
      'name' => __('Disciplinas'),
      'singular_name' => __('Disciplina')
    ),
    'public' => true,
    'rewrite' => array('slug' => 'disciplina'),
    'capability_type' => 'post',
    'hierarchical' => false,
    'supports' => array('title','editor','thumbnail'),
    'has_archive' => true
      )
  );
}
{% endhighlight %}

Utilizamos um “gancho” (ou Hook) para registrar a função que irá criar os custom posts:
`add_action(‘init’, ‘criar_tipos_personalizados’);`
(lembrando que o nome da função – criar_tipos_personalizados – é arbitrária)

Com a função do WordPress `register_post_type` criamos nosso tipo “exemplo_disciplina”. É possível definir vários labels para o novo tipo, veja na documentação da função register_post_type todas as possibilidades. Aqui criamos apenas o básico para a fácil identificação no admin.

Podemos nomear os tipos da forma que for mais conveniente. No caso, nosso tipo foi registrado no WordPress como exemplo_disciplina, mas a fim de melhorar a legibilidade nas URLs utilizamos o trecho
`‘rewrite’ => array(‘slug’ => ‘disciplina’),`
para que o WordPress exiba os posts do tipo exemplo_disciplina como disciplina.

Para adicionar mais Custom Posts ao projeto, basta utilizar novamente a função `register_post_type` dentro da nossa função definida. Vamos adicionar o tipo “exemplo_aluno”:

{% highlight PHP %}
<?php
add_action('init', 'criar_tipos_personalizados');

function criar_tipos_personalizados() {
    register_post_type( 'exemplo_disciplina',
    	array(
	        'labels' =>
    			array(
			        'name' => __('Disciplinas'),
			        'singular_name' => __('Disciplina')
			    ),		
	        'public' => true,
	        'rewrite' => array('slug' => 'disciplina'),
	        'capability_type' => 'post',
	        'hierarchical' => false,	     
	        'supports' => array('title','editor','thumbnail'),
		'has_archive' => true
	      )
	);

    register_post_type( 'exemplo_aluno',
    	array(
	        'labels' =>
    			array(
			        'name' => __('Alunos'),
			        'singular_name' => __('Aluno')
			    ),		
	        'public' => true,
	        'rewrite' => array('slug' => 'aluno'),
	        'capability_type' => 'post',
	        'hierarchical' => false,	     
	        'supports' => array('title'),
		'has_archive' => true
	      )
	);
}
{% endhighlight %}

Vá ao sistema administrativo do wordpress, e você verá seus 2 novos tipos no menu. E para exibir estes itens? Crie um template de páginas com o nome `template-disciplina.php` em seu tema e insira o código que se segue: (lembre-se de seguir a estrutura do seu tema. Para facilitar, copie algum dos arquivos do tema que exiba posts)

{% highlight PHP %}
<?php
/**
 * Template Name: Disciplinas
 *
 */
get_header();


	$args = array(
		"post_type" => "exemplo_disciplina"												
	);
	$posts = query_posts($args);

	while ( have_posts() ) : the_post();
	?>

		<h1><?php echo get_the_title(); ?></h1>

	<?php
	endwhile;

get_footer();
{% endhighlight %}

Feito isto, vá a administração de páginas e crie um nova página em seu WordPress com o título Disciplinas. Além do título, em Atributos de Página -> Modelo, selecione o template Disciplinas, criado no passo anterior. Feito! Acesse sua página e será possível visualizar a listagem das disciplinas já cadastradas.

Veremos, em um próximo post, como inserir categorias customizadas (ou Taxonomies). Por exemplo, podemos criar a categoria “Semestre”, e relacioná-la com disciplinas cadastradas em nosso sistema.
