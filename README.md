![Imagem](https://seahawkmedia.com/wp-content/uploads/2023/02/wix-to-wordpress-01-scaled.jpg)

# Otimização de processo: Automação de Migração de Conteúdo Wix-WordPress com Python
Projeto de migração de postagens do blog da OAB Paraná para novo site com script em Python para automatizar o processo de extração, transformação e carga de 301 posts, garantindo 100% de eficiência e integridade dos dados em JSON entre diferentes plataformas (Wix → WordPress) através do Python e APIs.

## Contexto
Este projeto visa automatizar a migração de 301 posts do antigo blog da OAB Maringá, hospedado no Wix, para o novo site em WordPress. Através da extração de dados via API JSON do Wix e da utilização da API do WordPress, migrei o conteúdo de forma eficiente e precisa, eliminando a necessidade de trabalho manual e reduzindo o risco de erros, economizando tempo e reduzindo o risco humano durante o processo de migração do blog, garantindo a integridade dos dados.

## Desafio / Problema de negócio
* A estrutura front-end possuí incompatibilidades de javascript e não poderá ser feito web scraping por HTML.
* Você é livre para escolher a linguagem e as ferramentas que julgar relevante para execução do projeto.
* Caso encontre outra forma de fazer web scraping sem ser por parse.html, poderá apresentar uma solução.
* Não poderá acessar o painel administrativo do Wix para exportar dados.
* Não poderá ser instalado nenhum plugin terceiro para importação de dados no WordPress, por questões de custo e performance (em caso de falha na importação e sujeira no banco de dados MySQL do site novo).
* Por uma questão de otimização nos mecanismos de busca, todos os links atuais devem ser preservados.
* Você deverá, por conta própria, descobrir qual o volume de dados e problemas de conteúdo nas postagens, apresentando um relatório com uma solução de ajustes e correções.
* Todas as soluções devem ser apresentadas para tomadores de decisão e também por desenvolvedores, com os dados do que será migrado para aprovação.
* A única informação sobre o site será retirada de https://www.oabmaringa.org.br/blog.

# Solução

# Introdução
Para solucionar o problema, inspecionei o código fonte para identificar as referências da plataforma e as requisições feitas ao servidor, analisando os dados retornados pela API. Identifiquei o componente do sistema que fornece uma interface programática para acessar os dados do blog de forma padronizada e eficiente através de uma API pública de segunda versão. A documentação da Wix sobre API REST pode ser encontrada no site Wix Developers [clicando aqui](https://dev.wix.com/docs/build-apps/develop-your-app/api-integrations/rest). Dessa forma, posso interagir com os dados brutos e estruturados, realizando um processo ETL no formato JSON. Ao codificar os parâmetros da URL, obtenho um método muito mais eficiente e preciso do que realizar web scraping na página HTML.

## Tecnologias utilizadas
* **Python:** Linguagem de programação utilizada para desenvolver o script.
* **Requests:** Biblioteca para fazer requisições HTTP à API do Wix.
* **json:** Biblioteca padrão do Python para manipular dados JSON.
* **WordPress REST API:** Utilizada para inserir os dados extraídos no WordPress.
* **Streamlit:** Biblioteca para criar dashboards e visualizar os dados da migração.

## Estrutura de dados

### Parâmetros de consulta
Primeiro, encontrei a URL que indica uma solicitação para a API do blog da OAB Maringá [clique aqui](https://www.oabmaringa.org.br/blog-frontend-adapter-public/v2/post-feed-page?includeContent=false&page=1&pageSize=20&type=ALL_POSTS).
Com uma forma mais moderna de compartilhamento de dados com outras aplicações, configurei a personalização da consulta pelos parâmetros da URL, filtrando de forma mais específica.
Com base nos dados estruturados, organizei o JSON e tabela conforme abaixo, por tipo do dado, nome do acesso e funcionalidade.
Considerei apenas os dados que julguei relevante para o caso de uso.

Parâmetro  |  Valor  |  Descrição do parâmetro  |
|:---------|  :---:  |  -------------------:    |
?includeContent= | True | Conteúdo cocmpleto dos posts na resposta.
&page | 1 | Página de resultado.
&pageSize | 20 | Quantidade de posts em cada página.
&type | ALL_POSTS | Todos os tipos de posts.
?fields | title,content | Recursos a serem inclusos na resposta.
*

#### Dados de consumo geral: 
Campo            |        Tipo     |     Acesso      |      Descrição
|      :---      |      :---:      |      :---:      |      ---:      |
postFeedPage | Object |	Root	| Raiz do objeto contendo dados da paginação de posts do blog.
id	| String |	postFeedPage.id |	Identificador da página de feed de posts.
id |	String |	postFeedPage.currentUser.appStore.<app_id>.id |	Identificador do app store (ex: 14bcded7-0066-7c35-14d7-466cb3f09103).
posts |	Object |	postFeedPage.posts |	Objeto contendo a lista de posts.
posts	| Array |	postFeedPage.posts.posts |	Array contendo dados de cada post.

#### Parâmetros sobre : 
Campo	| Tipo | Acesso | Descrição
id |	String	| postFeedPage.posts.posts.<post_id>.id |	Identificador único do post (ex: a9ec0b65-3e8c-4b76-b6eb-8f81816b1c80).
title |	String	| postFeedPage.posts.posts.<post_id>.title |	Título do post.
excerpt	| String |	postFeedPage.posts.posts.<post_id>.excerpt	| Trecho inicial do post.
firstPublishedDate |	String |	postFeedPage.posts.posts.<post_id>.firstPublishedDate	| Data e hora da primeira publicação (formato ISO 8601).
url |	Object |	postFeedPage.posts.posts.<post_id>.url |	Objeto contendo informações da URL do post.
slug |	String	| postFeedPage.posts.posts.<post_id>.url.slug |	Slug do post (usado na URL amigável).
coverMedia	Object	postFeedPage.posts.posts.<post_id>.coverMedia	Informações sobre a mídia de capa do post.
enabled	Boolean	postFeedPage.posts.posts.<post_id>.coverMedia.enabled	Indica se a mídia de capa está habilitada (true).

image	Object	postFeedPage.posts.posts.<post_id>.coverMedia.image	Informações sobre a imagem da capa.
id	String	postFeedPage.posts.posts.<post_id>.coverMedia.image.id	Identificador da imagem.
url	String	postFeedPage.posts.posts.<post_id>.coverMedia.image.url	URL da imagem.
height	Number	postFeedPage.posts.posts.<post_id>.coverMedia.image.height	Altura da imagem em pixels.
width	Number	postFeedPage.posts.posts.<post_id>.coverMedia.image.width	Largura da imagem em pixels.
displayed	Boolean	postFeedPage.posts.posts.<post_id>.coverMedia.displayed	Indica se a imagem está sendo exibida (true).
custom	Boolean	postFeedPage.posts.posts.<post_id>.coverMedia.custom	Indica se a imagem é personalizada (false).

#### Dados de consumo sobre SEO: 
minutesToRead	Number	postFeedPage.posts.posts.<post_id>.minutesToRead	Estimativa do tempo de leitura do post em minutos.
seoData	Object	postFeedPage.posts.posts.<post_id>.seoData	Dados de SEO do post.
tags	Array	postFeedPage.posts.posts.<post_id>.seoData.tags	Lista de tags de SEO associadas ao post.

media	Object	postFeedPage.posts.posts.<post_id>.media	Informações sobre a mídia do post.
- wixMedia	Object	postFeedPage.posts.posts.<post_id>.media.wixMedia	Informações sobre a mídia Wix.
- image	Object	postFeedPage.posts.posts.<post_id>.media.wixMedia.image	Informações sobre a imagem da mídia Wix.
- id	String	postFeedPage.posts.posts.<post_id>.media.wixMedia.image.id	Identificador da imagem.
- url	String	postFeedPage.posts.posts.<post_id>.media.wixMedia.image.url	URL da imagem.
- height	Number	postFeedPage.posts.posts.<post_id>.media.wixMedia.image.height	Altura da imagem em pixels.
- width	Number	postFeedPage.posts.posts.<post_id>.media.wixMedia.image.width	Largura da imagem em pixels.
- displayed	Boolean	postFeedPage.posts.posts.<post_id>.media.displayed	Indica se a mídia está sendo exibida (true).
- custom	Boolean	postFeedPage.posts.posts.<post_id>.media.custom	Indica se a mídia é personalizada (false).
customExcerpt	Boolean	postFeedPage.posts.posts.<post_id>.customExcerpt	Indica se o trecho inicial do post é personalizado (false).
internalId	String	postFeedPage.posts.posts.<post_id>.internalId	Identificador interno do post.
isLiked	Boolean	postFeedPage.posts.posts.<post_id>.isLiked	Indica se o post foi curtido pelo usuário atual (false).
isSubscribed	Boolean	postFeedPage.posts.posts.<post_id>.isSubscribed	Indica se o usuário atual está inscrito para receber notificações sobre o post (false).
preview	Boolean	postFeedPage.posts.posts.<post_id>.preview	Indica se o post está em modo de pré-visualização (false).
link	String	postFeedPage.posts.posts.<post_id>.link	URL completa do post.

#### Dados multimídia para download:
Campo	| Tipo | Acesso | Descrição
postFeedPage | Object |	Root	| Raiz do objeto contendo dados da paginação de posts do blog.
id	| String |	postFeedPage.id |	Identificador da página de feed de posts.
id |	String |	postFeedPage.currentUser.appStore.<app_id>.id |	Identificador do app store (ex: 14bcded7-0066-7c35-14d7-466cb3f09103).
posts |	Object |	postFeedPage.posts |	Objeto contendo a lista de posts.
posts	| Array |	postFeedPage.posts.posts |	Array contendo dados de cada post.
id |	String	| postFeedPage.posts.posts.<post_id>.id |	Identificador único do post (ex: a9ec0b65-3e8c-4b76-b6eb-8f81816b1c80).
title |	String	| postFeedPage.posts.posts.<post_id>.title |	Título do post.
excerpt	| String |	postFeedPage.posts.posts.<post_id>.excerpt	| Trecho inicial do post.
firstPublishedDate |	String |	postFeedPage.posts.posts.<post_id>.firstPublishedDate	| Data e hora da primeira publicação (formato ISO 8601).
lastPublishedDate	| String |	postFeedPage.posts.posts.<post_id>.lastPublishedDate	| Data e hora da última publicação (formato ISO 8601).
url |	Object |	postFeedPage.posts.posts.<post_id>.url |	Objeto contendo informações da URL do post.
base	String	postFeedPage.posts.posts.<post_id>.url.base	URL base do site (ex: https://www.oabmaringa.org.br).
path	String	postFeedPage.posts.posts.<post_id>.url.path	Caminho relativo do post dentro do site (ex: /post/eleicoes-diretas-para-o-cfoab-serao-debatidas-no-conselho-seccional).
slug	String	postFeedPage.posts.posts.<post_id>.url.slug	Slug do post (usado na URL amigável).
featured	Boolean	postFeedPage.posts.posts.<post_id>.featured	Indica se o post é destaque (false).
pinned	Boolean	postFeedPage.posts.posts.<post_id>.pinned	Indica se o post está fixado (false).
categoryIds	Array	postFeedPage.posts.posts.<post_id>.categoryIds	Array contendo IDs das categorias associadas ao post (pode estar vazio).








## Resultados

### Lógica do ETL
```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

### Lógica de programação
```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

### Dados de migração
![Apresentação de dados de migração para aprovação](https://blog.streamlit.io/content/images/2022/04/Live-Data-Science-Dashboard-GIF-1.gif)

Imagem
1. Qualidade de código: Criação de script para revisão por outro desenvolvedor, com facilidade de leitura/compreensão do código, considerando melhores práticas de código em Python (aqui), considerando, ainda, aspectos de herança, polimorfismo, encapsulamento e lógica de programação para resolução do problema.
2. Documentação técnica: Facilidade para que o código seja entendido, executado, excluído e reproduzido para outros contextos no Wix-WordPress, com markdown detalhado.
3. Apresentação final: Dashboard explicitando quais dados serão migrados, assim como insights claros sobre pontos de melhoria nos posts anteriores, como falta de imagens e conteúdos.
4. Visualização: Facilitar a comunicação sobre os principais insights da migração de dados para a equipe de negócios, considerando também aspectos de design analítico e funcional.
