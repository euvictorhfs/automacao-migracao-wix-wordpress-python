![Imagem de logotipos Wix e WordPress](https://github.com/euvictorhfs/automacao-migracao-wix-wordpress-python/blob/main/Wix-WordPress.jpg)

# Otimização de processo: Automação de Migração de Conteúdo Wix-WordPress com Python e API REST
Migração de postagens de blog com garantia de eficiência, qualidade e integridade e dados estruturados entre diferentes plataformas (Wix → WordPress) através das tecnologias Python e API REST.

## Contexto
Neste projeto, desenvolvi uma solução completa para a migração de cerca de 301 posts do blog antigo para o novo site da OAB Maringá. Utilizando Python e ETL, automatizei o processo de extração de dados via API REST, transformação para o novo formato e carregamento no sistema. Ao eliminar a necessidade de migração manual, garanti a precisão dos dados e economizei tempo, além de criar uma base sólida para futuras migrações. A documentação detalhada deste projeto demonstra minha capacidade de analisar dados, projetar soluções eficientes e trabalhar com diferentes tecnologias.

## Desafio / Problema de negócio
1. Engenharia Reversa de Aplicações Web: A necessidade de contornar as limitações da plataforma Wix, como a extração de dados de componentes JavaScript renderizados dinamicamente, exigiu o uso de técnicas avançadas de engenharia reversa para identificar e explorar pontos de acesso não documentados.
2. Otimização de Performance para Web Scraping: A implementação de estratégias para otimizar a performance do web scraping, como o uso de proxies, caching, e a identificação de padrões nos requests, foi crucial para lidar com o grande volume de dados e evitar sobrecarregar os servidores do site original.
3. Transformação de Dados e Mapeamento de Dados: A complexidade da transformação dos dados extraídos para o formato compatível com o WordPress, incluindo a criação de mapeamentos personalizados entre os campos, exigiu conhecimento de estruturas de dados e algoritmos.
4. Desenvolvimento de Ferramentas Customizadas: A criação de ferramentas personalizadas para automatizar tarefas repetitivas, como a extração de dados, a limpeza e a transformação, demonstrou a capacidade de desenvolver soluções sob medida para problemas específicos.
5. Gerenciamento de Erros e Exceções: Evidencia a importância de criar soluções robustas e resilientes, capazes de lidar com diversas situações inesperadas, como falhas em conexões de dados ou erros de processamento.
6. Integração com Ferramentas de CI/CD: A integração da solução com ferramentas de CI/CD, como o GitHub Actions ou o Jenkins, permitiu automatizar o processo de build, teste e deploy, garantindo a qualidade e a consistência do código.
7. Monitoramento e Análise de Logs: A implementação de um sistema de monitoramento e análise de logs para identificar e resolver problemas de performance e erros foi essencial para garantir a estabilidade da solução a longo prazo.
8. Documentação Técnica Detalhada: A criação de uma documentação técnica completa, incluindo diagramas de fluxo, exemplos de código e explicações detalhadas das decisões de design, facilitou a compreensão da solução e a colaboração com outros desenvolvedores.
9. Adesão a Boas Práticas de Desenvolvimento: Evidencia o conhecimento e a aplicação de boas práticas de desenvolvimento, como padrões de projeto, testes unitários, revisão de código e compartilhamento de código, para garantir a facilidade de entendimento e a manutenibilidade do código.

## Materiais
* Domínio do blog no Wix para inspeção de código [site](https://www.oabmaringa.org.br/blog).
* Caminho que conecta o front-end do blog com a parte interna do sistema Wix [site](https://www.oabmaringa.org.br/blog-frontend-adapter-public/v2/post-feed-page?includeContent=true&page=1&pageSize=50&type=ALL_POSTS).

### Tecnologias utilizadas
* **Python:** Linguagem de programação utilizada para desenvolver o script.
* **Requests:** Biblioteca para fazer requisições HTTP à API do Wix.
* **json:** Biblioteca padrão do Python para manipular dados JSON.
* **WordPress REST API:** Utilizada para inserir os dados extraídos no WordPress.
* **Streamlit:** Biblioteca para criar dashboards e visualizar os dados da migração.

### Dados de consumo
#### Parâmetros de URL
Parâmetro  |  Valor  |  Descrição do parâmetro  |
|:---------|  :---:  |  -------------------:    |
?includeContent | True | Conteúdo completo dos posts na resposta.
&page | 1 | Página de resultado.
&pageSize | 20 | Quantidade de posts em cada página.
&type | ALL_POSTS | Todos os tipos de posts.
?fields | title[^1] | Recursos a serem inclusos na resposta.
[^1]: Os recursos devem ser incluídos com base nos dados estruturados do campo da tabela abaixo.

#### Dados estruturados para consumo
Campo            |        Tipo     |     Acesso      |      Descrição
|      :---      |      :---:      |      :---:      |      ---:      |
postFeedPage | Object |	Root	| Raiz do objeto contendo dados da paginação de posts do blog.
id	| String |	postFeedPage.id |	Identificador da página de feed de posts.
id |	String |	postFeedPage.currentUser.appStore.<app_id>.id |	Identificador do app store (ex: 14bcded7-0066-7c35-14d7-466cb3f09103).
posts |	Object |	postFeedPage.posts |	Objeto contendo a lista de posts.
posts	| Array |	postFeedPage.posts.posts |	Array contendo dados de cada post.
id |	String	| postFeedPage.posts.posts.<post_id>.id |	Identificador único do post (ex: a9ec0b65-3e8c-4b76-b6eb-8f81816b1c80).
title |	String	| postFeedPage.posts.posts.<post_id>.title |	Título do post.
excerpt	| String |	postFeedPage.posts.posts.<post_id>.excerpt	| Trecho inicial do post.
firstPublishedDate |	String |	postFeedPage.posts.posts.<post_id>.firstPublishedDate	| Data e hora da primeira publicação (formato ISO 8601).
url |	Object |	postFeedPage.posts.posts.<post_id>.url |	Objeto contendo informações da URL do post.
slug |	String	| postFeedPage.posts.posts.<post_id>.url.slug |	Slug do post (usado na URL amigável).
coverMedia |	Object |	postFeedPage.posts.posts.<post_id>.coverMedia |	Informações sobre a mídia de capa do post.
enabled	| Boolean |	postFeedPage.posts.posts.<post_id>.coverMedia.enabled |	Indica se a mídia de capa está habilitada (true).
image	| Object |	postFeedPage.posts.posts.<post_id>.coverMedia.image	| Informações sobre a imagem da capa.
video[^2]	| Object	| postFeedPage.posts.posts.<post_id>.coverMedia.video	| Informações sobre o vídeo da capa.
id	| String	| postFeedPage.posts.posts.<post_id>.coverMedia.image.id	| Identificador da imagem.
url	String	| postFeedPage.posts.posts.<post_id>.coverMedia.image.url	| URL da imagem.
height	| Number |	postFeedPage.posts.posts.<post_id>.coverMedia.image.height	| Altura da imagem em pixels.
width	| Number |	postFeedPage.posts.posts.<post_id>.coverMedia.image.width	| Largura da imagem em pixels.
displayed |	Boolean |	postFeedPage.posts.posts.<post_id>.coverMedia.displayed |	Indica se a imagem está sendo exibida (true).
custom	| Boolean |	postFeedPage.posts.posts.<post_id>.coverMedia.custom |	Indica se a imagem é personalizada (false).
minutesToRead |	Number	postFeedPage.posts.posts.<post_id>.minutesToRead |	Estimativa do tempo de leitura do post em minutos.
seoData	| Object |	postFeedPage.posts.posts.<post_id>.seoData	| Dados de SEO do post.
tags	| Array	| postFeedPage.posts.posts.<post_id>.seoData.tags |	Lista de tags de SEO associadas ao post.
link	| String |	postFeedPage.posts.posts.<post_id>.link	| |URL completa do post.
url |	Object |	postFeedPage.posts.posts.<post_id>.url |	Objeto contendo informações da URL do post.
slug	| String |	postFeedPage.posts.posts.<post_id>.url.slug	| Slug do post (usado na URL amigável).

[^2]: O campo de vídeo só aparece quando o post contém vídeo, do contrário, esse dado não existe. Foi realizado um tratamento de erros aqui. E, caso o redator tenha esquecido de postar imagem ou vídeo, eu criei um layout padrão com a identidade visual da OAB Maringá para utilizar como imagem (default.jpg).

## Pensamento computacional
### ETL
```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```
### Python
```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```
### Cliente
```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

## Resultados
![Acompanhamento do Pipeline de Dados](https://global.discourse-cdn.com/business7/uploads/streamlit/original/2X/4/42208bbc19b918fca7ed2ba1b9112c7b8c4f71ed.gif)
Acompanhamento do Pipeline de Dados[^3]
[^3]: Acompanhamento do Pipeline de Dados com feedback contínuo e qualidade de código na criação de script para revisão por outro desenvolvedor, com facilidade de leitura/compreensão do código, considerando melhores práticas de código em Python (aqui), considerando, ainda, aspectos de herança, polimorfismo, encapsulamento e lógica de programação para resolução do problema.

![Visualização de Dados para Aprovação](https://blog.streamlit.io/content/images/2022/04/Live-Data-Science-Dashboard-GIF-1.gif)
Visualização de Dados para Aprovação[^4]
[^4]: A visualização de dados para aprovação facilita a comunicação sobre os principais insights da migração de dados para a equipe de negócios, considerando também aspectos de design analítico e funcional.

## Referências 
* Wix Developers [site](https://dev.wix.com/docs/build-apps/develop-your-app/api-integrations/rest).

