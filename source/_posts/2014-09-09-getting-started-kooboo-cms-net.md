---
layout: post
title: "Getting Started Kooboo CMS .NET"
date: 2014-09-09 22:10:06
comments: true
description: "Getting Started Kooboo CMS .NET"
keywords: "asp.net, cms, getting started, kooboo"
categories: asp-net
tags:
- asp.net
- cms
- getting started
- kooboo
---

O Kooboo é um CMS desenvolvido em .NET por uma empresa sediada na China. O desenvolvimento é muito simples e rápido. Possui código aberto, pode ser encontrado no <a href="https://github.com/Kooboo/CMS" target="_blank">Github</a>.

Por default, um projeto inicial no Kooboo já realiza persistência do conteúdo em XML. Isso é uma simples comodidade, para deixar mais didático o início do desenvolvimento. Em poucos minutos é possível ter um site funcionando. Para prosseguir no desenvolvimento do projeto, é necessário trocar o provider de conteúdo. Na página <a href="http://wiki.kooboo.com/?wiki=Setup_database_provider#SQLServer" target="_blank">http://wiki.kooboo.com/?wiki=Setup_database_provider</a> é possível ver a lista completa dos providers, dentre os quais se encontram Sql Server, Mysql e MongoDB.

## Recursos disponíveis

O que mais chama a atenção, além da facilidade de criar um novo site, é a quantidade de recursos já embarcados no CMS, bastando apenas a configuração no painel administrativo. É possível desenvolver praticamente todo o site sem a necessidade de abrir o Visual Studio.

- Criar tipos customizados (notícias, eventos, destaques..)
- Biblioteca de arquivos (semelhante à biblioteca de mídia do WordPress)
- Busca com Lucene (Full-Text Search)
- Edição de páginas e conteúdos inline, sem necessidade entrar no painel administrativo
- Permite “multi-sites” com hierarquização e customização de diretórios virtuais
- Excelente performance (além de já possuir, na interface administrativa, suporte a cache tanto em memória quanto disco)
- Possibilidade de criar plugins e módulos
- Criação de temas e gerenciamento dos assets


## Passos iniciais para criar um projeto:

1. Clone o repositório: https://github.com/Kooboo/CMS.git
2. Execute o arquivo CMS\Kooboo.CMS\Publish\publish.bat para que ele gere o projeto do website para o visual studio.
3. O projeto estará na pasta CMS\Kooboo.CMS\Publish\Web. Copie todo o conteúdo desta pasta. Este será o seu projeto a partir de agora. Neste ponto já é possível rodar, customizar o site, criar conteúdo. O CMS está pronto para ser utilizado, mas todo o conteúdo está sendo armazenado em XML. Precisamos trocar o provider.
4. Para trocar o provider, descompacte o arquivo que se encontra em: CMS\Kooboo.CMS\Publish\Released\Content_Providers.zip. Para utilizar SQL Server, copie os arquivos da pasta SQLServer\Kooboo.CMS.Content.Persistence.SQLServer.dll e SQLServer\SqlServer.config para a pasta Wev\bin do seu projeto, gerado nos passos 1 e 2.
5. Configure a connection string do arquivo SqlServer.config (veja mais em http://wiki.kooboo.com/?wiki=Setup_database_provider#SQLServer)

## Veja Mais

Passo a passo no Gist: https://gist.github.com/adrianogodoy/dc64bd8cab08929b292c

Dúvidas? Acesse o fórum do kooboo ou a seção de Issues no Github.

Veja a documentação completa em: http://kooboo.com/docs/kooboo-cms/all
