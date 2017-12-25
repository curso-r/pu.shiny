---
title: 'Introdução'
date: '2017-12-25'
---






## Introdução

- O que é/o que não é
- User/server side

## Estrutura básica

- Template minimal
- Rodar/modificar/parar


## Introdução

Nas outras aulas pincelamos os elementos de transformação, visualização e modelagem de dados. Para completar nossos trabalhos, precisamos de boas ferramentas de comunicação.

A maior parte dos trabalhos de análise estatística possui três *outputs* possíveis: i) relatórios analíticos, ii) *dashboards* de visualização e iii) APIs (*Application Programming Interfaces*). Neste Power-Up, vamos aprender a construir *dashboards* utilizando o pacote `shiny`.

O Shiny é um sistema para desenvolvimento de aplicações web usando o R, um pacote do R (`shiny`) e um servidor web (`shiny server`). O Shiny é exatamente isso e nada mais, portanto Shiny não é uma página web, não é um substituto para sistemas mais gerais, como Ruby on Rails e Django, e também não é uma ferramenta gerencial, como o Tableau.

Para entender sobre Shiny, é necessário entender primeiro o que é [server side e user side](http://programmers.stackexchange.com/a/171210 "diferencas"). Quando surfamos na web, nos _comunicamos_ com servidores do mundo inteiro, geralmente através do protocolo HTTP.

No server side, processamos requisições e dados do cliente, estrutura e envia páginas web, interage com banco de dados, etc. Linguagens server side comuns são PHP, C#, Java, R etc (virtualmente qualquer linguagem de programação).

No user side, criamos interfaces gráficas a partir dos códigos recebidos pelo servidor, envia e recebe informações do servidor etc. As "linguagens" mais usuais nesse caso são HTML, CSS e JavaScript.

Mas onde está o Shiny nisso tudo? O código de uma aplicação shiny fica no _server side_. O shiny permite que um computador (servidor) envie páginas web, receba informações do usuário e processe dados, utilizando apenas o R. Para rodar aplicativos shiny, geralmente estruturamos a parte relacionada ao HTML, JavaScript e CSS no arquivo `ui.R`, e a parte relacionada com processamento de dados e geração de gráficos e análises no arquivo `server.R`. Os arquivos `ui.R` e `server.R` ficam no servidor! Atualmente é possível construir [aplicativos em um arquivo só](http://shiny.rstudio.com/articles/single-file.html), mas vamos manter a estrutura de `ui.R` e `server.R`.

O pacote `shiny` do R possui internamente um servidor web básico, geralmente utilizado para aplicações locais, permitindo somente uma aplicação por vez. O `shiny server` é um programa que roda somente em Linux que permite o acesso a múltiplas aplicações simultaneamente.






## Começando com um exemplo

Um aplicativo em `shiny` (ou `shiny app`) é composto por duas partes:

- Um script `ui.R`, que constrói a interface que o usuário enxerga e interage;
- Um script `server.R`, que descreve o código em R que roda por trás da `user-interface`. 

Um exemplo de app com essa estrutura pode ser visualizado rodando o comando abaixo. (Para voltar ao R feche a janela ou pressine Esc no console):


```r
shiny::runExample('01_hello')
```

Nesse primeiro exemplo, o arquivo `ui.R` é bastante simples, mas ilustra a estrutura básica de um arquivo desse tipo.


```r
library(shiny)

# Define a User-Interface da aplicação
shinyUI(fluidPage(
  # Título da aplicação
  titlePanel("Hello Shiny!"),

  # Sidebar com um slider para o número de colunas
  sidebarLayout(
    sidebarPanel(
      sliderInput("bins",
                  "Number of bins:",
                  min = 1,
                  max = 50,
                  value = 30)
    ),

    # Imprime o plot
    mainPanel(
      plotOutput("distPlot")
    )
  )
))
```

Todo arquivo `ui` tem uma composição parecida com a identificada acima:

- Todo o código, com exceção do `library(shiny)`, está envolto em uma aplicação da função `shinyUI`.
    - Dentro do `shinyUI`, todo código está envolto em uma função que define o layout da aplicação. No exemplo anterior, `fluidPage` faz esse papel. Existem outras opções que serão detalhadas mais adiante.
      - Dentro da definição do layout vem o conteúdo da página.


```r

library(shiny)

# Define a lógica necessária pra criar o histograma
shinyServer(function(input, output) {

  # Expressão que gera o histograma. A expressão é 
  # escrita dentro de um "renderPlot" para garantir
  # duas coisas:
  #
  #  1) A expressão é "reactive" e por isso será atualizada
  # automaticamente após a mudança de um input.
  #  2) Seu tipo de output é um plot.

  output$distPlot <- renderPlot({
    x    <- faithful[, 2]  # Old Faithful Geyser data
    bins <- seq(min(x), max(x), length.out = input$bins + 1)

    # Desenha o histograma com um determinado número de bins.
    hist(x, breaks = bins, col = 'darkgray', border = 'white')
  })
})

```

É interessante notar que o código não fornece nenhum parâmetro gráfico para o navegador, tal como o conteúdo de um arquivo `css`. O Shiny utiliza como padrão o estilo [bootstrap css](http://getbootstrap.com/css/) do [Twitter](https://twitter.com), que é bonito e responsivo (lida bem com várias plataformas, como notebook e mobile). Não é necessário descrever com detalhes o site que será construído, apenas os `inputs` e `outputs`.

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



## UI

- Funções do ui geram código HTML
- Inputs
   - dar exemplos
   - explicar argumentos
- Outputs
   - tabela com exemplo de funções
   - discutir argumentos de uma função


<script src="https://cdn.datacamp.com/datacamp-light-latest.min.js"></script>




<script src="https://cdn.datacamp.com/datacamp-light-latest.min.js"></script>



1. Calcule o número de ouro no R.

$$
\frac{1 + \sqrt{5}}{2}
$$

<div data-datacamp-exercise data-height="300" data-encoded="true">eyJsYW5ndWFnZSI6InIiLCJzYW1wbGUiOiIjIERpZ2l0ZSBhIGV4cHJlc3NcdTAwZTNvIHF1ZSBjYWxjdWxhIG8gblx1MDBmYW1lcm8gZGUgb3Vyby4iLCJzb2x1dGlvbiI6IigxICsgc3FydCg1KSkvMiIsInNjdCI6InRlc3Rfb3V0cHV0X2NvbnRhaW5zKFwiMS42MTgwMzRcIiwgaW5jb3JyZWN0X21zZyA9IFwiVGVtIGNlcnRlemEgZGUgcXVlIGluZGljb3UgYSBleHByZXNzXHUwMGUzbyBjb3JyZXRhbWVudGU/XCIpXG5zdWNjZXNzX21zZyhcIkNvcnJldG8hXCIpIn0=</div>







<script src="https://cdn.datacamp.com/datacamp-light-latest.min.js"></script>


## Server

- Arquitetura
   - atualização dos parâmetros (inputs)
   - outputs
   - funções Render
   - dar exemplos


<script src="https://cdn.datacamp.com/datacamp-light-latest.min.js"></script>


## Reatividade

- Exemplo Excel
- reactive values
- reactive functions
   - render_()
   - reactive()
   - isolate()
   - observeEvent()
   - observe()
   - eventReactive()
   - reactiveValues()


<script src="https://cdn.datacamp.com/datacamp-light-latest.min.js"></script>


## Costumizando aparência

- fluidPage()
- tags()
   - exemplos
- HTML()
- layout
   - fluidRow()
   - fixedPage()
   - fixedRow()
- Paineis
- shinydashboard
- CSS


<script src="https://cdn.datacamp.com/datacamp-light-latest.min.js"></script>


## Compartilhando

- app.R (precisa ter esse nome)
- shinyapps.io
   - servidor do Rstudio
   - escalável
   - fácil
   - gratuito*
   - explicar como publicar
- Servidor próprio
   - Shiny server

