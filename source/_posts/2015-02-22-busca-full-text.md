---
layout: post
title: "Busca Full Text"
date: 2015-02-22 09:22:39
comments: true
description: "Busca Full-Text"
keywords: "asp.net, banco de dados, busca, full-text, lucene, performance, ruby on rails, solr"
categories: tecnicas
tags:
- asp.net
- banco de dados
- busca
- full-text
- lucene
- performance
- ruby on rails
- solr
---

Para permitir que usuários façam buscas no conteúdo de um site, muitas vezes optamos pelo caminho mais fácil, fazendo uma query com `WHERE LIKE` - uma simples busca textual no banco de dados. Funciona. É muito fácil de ser implementado e para **situações bem simples** tem um desempenho satisfatório. Mas alguns problemas são evidentes quando temos necessidades mais complexas:

- Não existe uma ordenação por **relevância** no resultado da busca. O fato do termo buscado aparecer no título, por exemplo, não dá ao conteúdo uma importância maior caso aparecesse no meio do texto, em termos de ordenação do resultado.
- Tende a ser lento, pois percorre todos os registros procurando o termo buscado, registro a registro. Em um sistema com muito conteúdo, a lentidão poderia impactar numa experiência ruim para os usuários e o uso excessivo dos recursos do servidor.

## Full-Text Search

É um mecanismo/técnica que permite a busca em documentos escritos em linguagem natural varrendo índices e permite, caso seja necessário, a ordenação dos resultados por relevância.

O processo de busca full-search divide-se em duas etapas fundamentais: **indexação** e **consulta**. Como o próprio nome sugere, os índices do conteúdo são inseridos numa primeira fase de indexação. Ao realizar uma consulta, não é necessário buscar nos documentos. A busca é realizada nos índices, e o documento correspondente é referenciado.

Um aspecto importante para retornar conteúdo de qualidade e diminuir a quantidade de índices é ignorar as palavras sem relevância semântica nos textos. As <a href="http://www.ranks.nl/stopwords" target="_blank">Stop Words</a> são palavras ignoradas na criação dos índices pois acrescentam pouco semanticamente e se repetem muito nos documentos. Normalmente são artigos, conjunções ou preposições (Ex,: o, um, pois, de, da, com).

<a href="http://en.wikipedia.org/wiki/Stemming" target="_blank">Steamming</a> é o processo de mapeamento de palavras derivadas para um mesmo “steam” (palavra raiz). Exemplo: pescar, pescando, pescador, pescaria são baseadas na palavra “pesca”. Esta técnica é de grande valia, pois ao realizar uma busca o usuário não se preocupa com o tempo verbal em que ela foi empregada.

Dadas as diferenças nos idiomas, o processo de steamming é  implementado de forma específica em cada um. Entretando, por problemas de ambiguidade na linguagem natural, temos problemas com falso-positivos: documentos não relevantes para a busca podem ser retornados no resultado. Em geral este problema é sanado pela ordenação dos resultados. O usuário encontra o que deseja (ou desiste) antes de chegar nestes documentos sem relevância.

## Buscadores Web

O Google, por exemplo, não é uma simples busca full-text (não estaria entre as marcas mais valiosas do mundo se fosse tão óbvio). Uma das grandes inovações foi o esquema de Page Rank, que aumenta a relevância do documento (no caso, uma página web) quando existem outros documentos referenciando a página.

Além do Page Rank, o algoritmo do Google implementa várias outras técnicas para dar relevância a um resultado de busca. Não se sabe exatamente todas as técnicas utilizadas pelo Google para rankear as páginas. Se o Google divulgasse todas as técnicas, várias páginas tentariam “enganar” o algoritmo para subir nas buscas, e isso é um dos motivos para o Google continuar evoluindo seus algoritmos.

## Bibliotecas/APIs

Em breve farei um post falando um pouco sobre duas das bibliotecas mais famosas:

### Lucene

Originalmente escrito em Java, e posteriormente adaptado para outras linguagens, a Lucene é uma das bibliotecas mais utilizadas para indexação e consulta de documentos utilizando busca full-search. Farei um post mostrando o uso básico no ASP.NET.

### Sunspot

Sunspot é uma biblioteca Ruby que tem como base a Solr (que por sua vez se baseia na Lucene). A utilização como gem no Ruby on Rails é muito simples, e traz excelentes recursos de busca.
