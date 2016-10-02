---
layout: post
title: "Refatoração"
date: 2008-10-30 15:51:23
comments: true
description: "Refatoração"
keywords: "agile, refatoração"
category: agile
tags:
- agile
- refatoracao
---

Refatoração é a técnica ou um processo no qual melhora-se a parte não funcional de um sistema sem alterar seu comportamento funcional.
Quantas vezes temos que fazer sistemas, ou mesmo criar um módulo para um sistema, e você escuta a célebre expressão: “é pra ontem” ? Não digo que o sistema ficará uma droga, ou que não funcionará, mas o código ficará pobre: trechos duplicados, má indentação, métodos longos, de difícil entendimento. Enfim, o tempo que seria gasto para fazer o trabalho “bem feito”, será gasto quando o sistema necessitar de manutenção/alteração.
É aí que pode entrar a refatoração. Um sistema bem feito, de código limpo e com métodos que “generalizam” a funcionalidade pode ser reaproveitado posteriormente para outros sistemas que necessitem dessas mesmas funcionalidades. A idéia seria, após o sistema ser entregue dentro do prazo exigido, voltar com o projeto para a linha de produção. Os programadores revisam todo o código, retirando as “impurezas” e deixando um código limpo, buscando um melhor desempenho e generalizando ao máximo os métodos.

## Situações em que refatorar é interessante:

1- Quando você tem que adicionar uma funcionalidade a um programa e o código do programa não está estruturado de uma forma que torne a implementação desta funcionalidade conveniente, primeiro refatore de modo a facilitar a implementação da funcionalidade e, só depois, implemente-a.

2- Quando se implementa uma nova funcionalidade às pressas, refatore e transforme essa funcionalidade em um módulo, para reaproveitamento em outros sistemas.

3- O código foi tão “porcamente” escrito, que dois meses após o desenvolvimento, se o projeto retornar para manutenção, nem mesmo a equipe que desenvolveu consiguirá compreender a lógica de negócio. Então, antes que o projeto perca o foco dos desenvolvedores, refatore.

Alguns indícios que sugerem a refatoração (retirados da wikipedia):

– Código duplicado (duplicated code)

– Método longo (long method)

– Classe grande (large class)

– Lista de parâmetros longa (long parameter list)

– Má indentação(Bad Indentation)

## Links sobre o assunto:

<a href="https://pt.wikipedia.org/wiki/Refatora%C3%A7%C3%A3o" target="_blank">Wikipedia</a>

<a href="http://refactoring.com/" target="_blank">Martin Fowler – Refactoring</a>

<a href="" target="_blank">Métodos Ágeis – devagil.wordpress.com</a>
