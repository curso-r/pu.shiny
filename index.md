---
title: Introdução
date: '2017-01-26'
---





## Introdução

Nas outras aulas pincelamos os elementos de transformação, visualização e modelagem de dados. Para completar nossos trabalhos, precisamos de boas ferramentas de comunicação.

A maior parte dos trabalhos de análise estatística possuem três *outputs* possíveis: i) relatórios analíticos, ii) *dashboards* de visualização e iii) APIs (*Application Programming Interfaces*). Neste Power-Up, vamos aprender a construir *dashboards* utilizando o pacote `shiny`.

O Shiny é um sistema para desenvolvimento de aplicações web usando o R, um pacote do R (`shiny`) e um servidor web (`shiny server`). Shiny não é uma página web, não é um substituto para sistemas mais gerais, como Ruby on Rails e Django, e também não é uma ferramenta gerencial, como o Tableau.

Para entender sobre Shiny, é necessário entender primeiro o que é [server side e user side](http://programmers.stackexchange.com/a/171210 "diferencas"). Quando surfamos na web, nos _comunicamos_ com servidores do mundo inteiro, geralmente através do protocolo HTTP.

No server side, processamos requisições e dados do cliente, estrutura e envia páginas web, interage com banco de dados, etc. Linguagens server side comuns são PHP, C#, Java, R etc (virtualmente qualquer linguagem de programação).

No user side, criamos interfaces gráficas a partir dos códigos recebidos pelo servidor, envia e recebe informações do servidor etc. As "linguagens" mais usuais nesse caso são HTML, CSS e JavaScript.

Mas onde está o Shiny nisso tudo? O código de uma aplicação shiny fica no _server side_. O shiny permite que um computador (servidor) envie páginas web, receba informações do usuário e processe dados, utilizando apenas o R. Para rodar aplicativos shiny, geralmente estruturamos a parte relacionada ao HTML, JavaScript e CSS no arquivo `ui.R`, e a parte relacionada com processamento de dados e geração de gráficos e análises no arquivo `server.R`. Os arquivos `ui.R` e `server.R` ficam no servidor! Atualmente é possível construir [aplicativos em um arquivo só](http://shiny.rstudio.com/articles/single-file.html), mas vamos manter a estrutura de `ui.R` e `server.R`.

O pacote `shiny` do R possui internamente um servidor web básico, geralmente utilizado para aplicações locais, permitindo somente uma aplicação por vez. O `shiny server` é um programa que roda somente em Linux que permite o acesso a múltiplas aplicações simultaneamente.






## Começando com um exemplo


```r
shiny::runGitHub('abjur/vistemplate', subdir='exemplo_01_helloworld',
                  display.mode = 'showcase')
```

O Shiny utiliza como padrão o [bootstrap css](http://getbootstrap.com/css/) do [Twitter](https://twitter.com), que é bonito e responsivo (lida bem com várias plataformas, como notebook e mobile). Note que criamos páginas básicas com `pageWithSidebar`. Páginas mais trabalhadas são criadas com `fluidPage`, `fluidRow`, `column`. Pesquise outros tipos de layouts no shiny. É possível criar páginas web customizadas direto no HTML.

Para estudar os *widgets* (entradas de dados para o usuário), acesse [este link](http://shiny.rstudio.com/gallery/widget-gallery.html 'widgets') ou rode


```r
shiny::runGitHub('garrettgman/shinyWidgets')
```

## Criando outputs

Imagine que para cada função `xxOutput('foo', ...)` do `ui.R` você pode colocar um código do tipo `output$foo <- renderXX(...)` no `server.R`. A função no arquivo `ui.R` determina a localização e identificação do elemento. Crie gráficos com `plotOutput` e `renderPlot` e exiba dados com `dataTableOutput` e `renderDataTable`.

## Fazendo mais com o shiny

### Shiny Server Pro

- Licença comercial do Shiny-server
- Possui algumas características a mais, como autenticação e suporte.

### shinyapps.io

- Para compartilhar um aplicativo shiny, geralmente precisamos ter um servidor Linux (geralmente utilizando algum serviço na cloud como AWS ou DigitalOcean) com o shiny server instalado.
- Isso pode ser doloroso.
- O shinyapps.io é um sistema (que envolve tanto pacote do R como uma página web) que permite que o usuário coloque sua aplicação shiny na web sem muito esforço.
- O serviço foi desenvolvido pela RStudio Inc. e possui contas grátis e pagas.

### Flexdashboards

(na outra página)


<script src="https://cdn.datacamp.com/datacamp-light-latest.min.js"></script>




<script src="https://cdn.datacamp.com/datacamp-light-latest.min.js"></script>



1. Calcule o número de ouro no R.

$$
\frac{1 + \sqrt{5}}{2}
$$

<div data-datacamp-exercise data-height="300" data-encoded="true">eyJsYW5ndWFnZSI6InIiLCJzYW1wbGUiOiIjIERpZ2l0ZSBhIGV4cHJlc3NcdTAwZTNvIHF1ZSBjYWxjdWxhIG8gblx1MDBmYW1lcm8gZGUgb3Vyby4iLCJzb2x1dGlvbiI6IigxICsgc3FydCg1KSkvMiIsInNjdCI6InRlc3Rfb3V0cHV0X2NvbnRhaW5zKFwiMS42MTgwMzRcIiwgaW5jb3JyZWN0X21zZyA9IFwiVGVtIGNlcnRlemEgZGUgcXVlIGluZGljb3UgYSBleHByZXNzXHUwMGUzbyBjb3JyZXRhbWVudGU/XCIpXG5zdWNjZXNzX21zZyhcIkNvcnJldG8hXCIpIn0=</div>






