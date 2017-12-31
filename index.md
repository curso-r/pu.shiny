---
title: 'Introdução'
date: '2017-12-31'
---






## Introdução

Até gora, pincelamos os principais elementos de transformação, visualização e modelagem de dados. Para completar nossa análise, precisamos de boas ferramentas de comunicação.

A maior parte dos trabalhos de análise estatística possui três *outputs* possíveis: 

- relatórios analíticos;
- *dashboards* de visualização; e
- APIs (*Application Programming Interfaces*).

O objetivo desta seção é a construção de *dashboards* utilizando o pacote `shiny`.

O Shiny é um sistema para desenvolvimento de aplicações web usando o R, um pacote do R (`shiny`) e um servidor web (`shiny server`). O Shiny é exatamente isso e nada mais, portanto Shiny **não** é uma página web, **não** é um substituto para sistemas mais gerais, como Ruby on Rails e Django, e também **não** é uma ferramenta gerencial, como o Tableau.

Para entender sobre Shiny, é necessário entender primeiro o que é [server side e user side](http://programmers.stackexchange.com/a/171210 "diferencas"). Quando surfamos na web, nos _comunicamos_ com servidores do mundo inteiro, geralmente por meio do protocolo HTTP.

No *server side*, processamos requisições e dados do cliente, estruturamos e enviamos páginas web, interagimos com banco de dados etc. Linguagens *server side* comuns são PHP, C#, Java, R etc (virtualmente qualquer linguagem de programação).

No *user side*, criamos interfaces gráficas a partir dos códigos recebidos pelo servidor. É onde enviamos e recebemos as informações do *server side*. As "linguagens" mais usuais nesse caso são HTML, CSS e JavaScript.

Mas onde está o Shiny nisso tudo? O código de uma aplicação Shiny nos permite estruturar tanto a interface com o usuário quanto o processamento de dados, geração de visualizações e modelagem, isto é, nós programamos tanto o *user side* quanto o *server side* numa tacada só. Assim, ao rodarmos o código, criamos um servidor que envia páginas web, recebe informações do usuário e processa os dados, utilizando apenas o R.

O pacote `shiny` do R possui internamente um servidor web básico, geralmente utilizado para aplicações locais, permitindo somente uma aplicação por vez. O `shiny server` é um programa que roda somente em Linux que permite o acesso a múltiplas aplicações simultaneamente. Falaremos mais sobre isso no item *Compartilhando*.

Antes de começarmos a explorar o Shiny, instale o pacote `shiny` no seu computador


```r
install.packages("shiny")
```

e, em seguida, carregue o pacote


```r
library(shiny)
```

## Estrutura básica

Um aplicativo em Shiny (ou Shiny app) é gerado por um único script chamado `app.R`. Esse script tem três componentes:

- um objeto com a interface do usuário (*user side*);

- uma função `server()` (*server side*); e

- uma chamada para a função `shinyApp()`.

Apresentamos abaixo um exemplo minimal de aplicação com essa estrutura.


```r
library(shiny)

# Define a interface do usuário para o app que gera um histograma.
ui <- fluidPage(

  # Título do app.
  titlePanel("Meu primeiro shiny app!"),

  # Barra lateral com as definições do input e do output.
  sidebarLayout(

    # Barra lateral para os inputs.
    sidebarPanel(

      # Input: número de classes do histograma.
      sliderInput(inputId = "classes",
                  label = "Número de classes:",
                  min = 1,
                  max = 30,
                  value = 10)

    ),

    # Painel principal para mostrar os outputs.
    mainPanel(

      # Output: Histograma
      plotOutput(outputId = "hist")

    )
  )
)


# Define o código necessário para a construção de um histograma.
server <- function(input, output) {

  # Função que gera o histograma e devolve para o user side.
  # Essa função é reativa. Isso significa que o histograma
  # vai mudar sempre que o valor do número de classes mudar.
  output$distPlot <- renderPlot({

    x    <- mtcars$mpg
    bins <- seq(min(x), max(x), length.out = input$classes + 1)

    hist(x, breaks = bins, col = "#75AADB", border = "white",
         xlab = "Milhas por galão",
         main = "Histograma do número de milhas rodadas por galão de combustível.")

  })

}

shinyApp(ui = ui, server = server)
```

Não se assuste com tanto vocabulário novo! Vamos passar por cada função ao decorrer desta seção. Nesse primeiro momento, queremos apenas que você se familiarize com a estrutura do código: primeiro a definição do objeto `ui`, em seguida a estruturação da função `server()` e por fim a chamada da função `shinyApp()`.

Existem duas maneiras de rodar o aplicativo. A primeira é rodar a função `runApp("caminho-para-o-arquivo-app.R")`. A segunda é clicar no botão "Run App" que vai aparecer no RStudio logo acima do seu script. Sempre que você estruturar um arquivo da maneira acima, o RStudio vai entender que se trata de um Shiny app e vai gerar essa opção (e outras).

Ao rodar o app, uma nova janela se abrirá e você terá acesso ao aplicativo (veja figura baixo). Ele estará rodando localmente, então você ainda não poderá acessá-lo pela internet.

<img src="figures/app_minimal.png" title="plot of chunk unnamed-chunk-11" alt="plot of chunk unnamed-chunk-11" width="60%" height="60%" />

Minimize a janela e veja no seu console que a sessão do R estará ocupada rodando o aplicativo. Assim, um Shiny app terá sempre uma sessão de R rodando por trás.

Você pode interagir com o app mudando o número de classes no *slider* gerado à esquerda. Sempre que você mudar o valor, o gráfico será atualizado automaticamente.

Para encerrar o aplicativo, basta fechar a janela. Observe no console que a sessão volta a ficar disponível.

Agora que você já conhece melhor a estrutura de um Shiny app, vamos entender melhor como construí-lo, começando com a interface do usuário.




## User side

Tudo o que será apresentado ao usuário está guardado no objeto `ui`, que nada mais é do que um código HTML. Experimente rodar uma função do *user side* no console. O que você receberá será sempre um código HTML.


```r
fluidPage()
```

<!--html_preserve--><div class="container-fluid"></div><!--/html_preserve-->

A função `fluidPage()` utilizada como exemplo acima é utilizada pelo Shiny para criar um display que automaticamente ajusta as dimensões da janela do navegador do usuário. Os elementos da interface do usuário são então colocados dentro dessa função. Veja um exemplo:


```r
ui <- fluidPage(
  titlePanel("Título"),

  sidebarLayout(
    sidebarPanel("Painel lateral"),
    mainPanel("Painel principal")
  )
)
```

O objeto `ui` acima gerará a seguinte interface:

<img src="figures/ui1.png" title="plot of chunk unnamed-chunk-14" alt="plot of chunk unnamed-chunk-14" width="60%" height="60%" />

As funções `titlePanel()` e `sidebarLayout()` são os argumentos da função `fluidPage()`. A primeira gera o título "Título", enquanto a segunda estrutura um layout com barra lateral para o app.

A função `sidebarLayout()` recebe dois argumentos: 

- `sidebarPanel()` - estrutura o painel na barra lateral; e
- `mainPanel()` - estrutura o painel principal.

Essa é uma das estruturas mais simples e populares para a interface de usuário de um Shiny app.

Note que nós criamos apenas títulos e painéis. Não há nada com o que o usuário possa interagir. Para isso, precisamos adicionar os *inputs*.

### inputs

Na prática, inputs são widgets que possibilitam a interação do usuário com o app. Eles recebem um valor escolhido pelo usuário e o envia para o *server side*. Segue uma lista dos principais inputs utilizados num Shiny app:

- `actionButton()` - botão para executar uma ação.
- `checkboxGroupInput()` - 	um grupo de *check boxes*.
- `checkboxInput()` -	um único *check box*.
- `dateInput()` -	um calendário para seleção de data.
- `dateRangeInput()` -	um par de calendários para escolher um intervalo de datas.
- `fileInput()` -	uma ferramenta para auxiliar o upload de arquivos.
- `numericInput()` -	Um campo para enviar números.
- `radioButtons()` -	Um conjunto de botões para seleção.
- `selectInput()` -	Um *select box* com um conjunto de opções.
- `sliderInput()` -	Um slider.
- `textInput()` -	Um campo para enviar texto.

No nosso primeiro exemplo, no item anterior, nós utilizamos um `sliderInput()` para interagir com o número de classes do histograma.


```r
ui <- fluidPage(

    titlePanel("Meu primeiro shiny app!"),

  sidebarLayout(
    sidebarPanel(
      sliderInput(inputId = "classes",
                  label = "Número de classes:",
                  min = 1,
                  max = 30,
                  value = 10)
    ),

    mainPanel(
      plotOutput(outputId = "hist")
    )
  )
)
```

Repare que a função `sliderInput()` recebe alguns argumentos. O mais importante é o `inputId=`. Esse argumento definirá o nome que usaremos para chamar esse input dentro do server. O argumento `label=` recebe o texto que aparecerá no widget, ajudando o usuário a entender o que o input controla. 

Cada input terá argumentos específicos da própria função. Assim, se você nunca usou um determinado input, procure no `help()` da função quais são os argumentos que ela recebe. No caso da `sliderInput()`, podemos controlar o valor mínimo do slider (`min = 1`), o valor máximo (`max = 30`) e o valor padrão (`value = 10`).

Agora que já sabemos como enviar objetos para o *server side*, vamos ver como receber as suas saídas.

### outputs

No exemplo do histograma, o input do nosso app era o número de classes e o output era o próprio histograma. Veja que no objeto `ui` temos o seguinte código:


```r
mainPanel(
      plotOutput(outputId = "distPlot")
    )
```

Isso quer dizer que vamos receber um output do tipo "plot" (gráfico) do servidor e colocá-lo dentro do `mainPanel()`. 

Da mesma forma que há uma função para cada tipo de input, há uma função para cada tipo de output:

- `dataTableOutput()` -	para data frames.
- `htmlOutput()` ou `uiOutput()` - para código HTML.
- `imageOutput()` - para imagens.
- `plotOutput()` - para gráficos.
- `tableOutput()` - para tabelas.
- `textOutput()` - para textos.
- `verbatimTextOutput()` - para textos não-formatados.

Assim como as funções de input, funções de output recebem um argumento de identificação, o `outputId=`. Esse argumento recebe uma string que representa o nome utilizado no *server side* para se referir a esse output. Consulte o `help()` de cada função para saber mais sobre os argumentos adicionais.

Criados os inputs e outputs do app, agora precisamos manipulá-los no *server side*. Vamos ver como fazer isso.



## Server side

Com a interface do usuário estruturada, precisamos agora implementar a função `server()`. Nela, colocaremos as instruções para gerar os outputs que nós vemos no *user side* a partir dos valores dos inputs que o usuário escolher.

A primeira coisa que precisamos fazer é defini-la. A função `server()` será sempre uma função que recebe dois argumentos: `input` e `output`. 


```r
server <- function(input, output) {
  
  # Código
  
}
```

A partir daí, precisamos seguir três regras:

1. Todos os outputs estão numa lista chamada `output`. Assim, como no exemplo do histograma nós chamamos o gráfico de `hist`, para nos referirmos a ele no *server side* utilizaremos `output$hist`.

2. Os outputs devem ser construídos com funções `render_()`. Existe uma função `render_()` para cada tipo de objeto. As principais são:

- `renderDataTable()` - data frames.
- `renderImage()` -	imagens.
- `renderPlot()` - gráficos.
- `renderPrint()` - qualquer printed output.
- `renderTable()` - data frames, matrizes, e outras estruturas em forma de tabela.
- `renderText()` - strings.
- `renderUI()` - um elemento do UI ou HTML.

O argumento dessas funções será sempre um bloco de código, usado para gerar o output desejado. Repare que para gerar o histograma, utilizamos a função `renderPlot()`.

3. Da mesma forma que os outputs, todos os inputs estão numa lista chamada `input`. Assim, para acessar o valor escolhido para o número de classes no exemplo do histograma, utilizaremos `input$classes`.

Agora já conseguimos entender o código do nosso exemplo.


```r
server <- function(input, output) {

  output$distPlot <- renderPlot({

    x    <- mtcars$mpg
    bins <- seq(min(x), max(x), length.out = input$classes + 1)

    hist(x, breaks = bins, col = "#75AADB", border = "white",
         xlab = "Milhas por galão",
         main = "Histograma do número de milhas rodadas por galão de combustível.")

  })

}
```

Repare nas `{}` dentro da função `renderPlot()`. Elas permitem que qualquer estrutura de código possa ser passada como argumento. Podemos então pular linhas e indentar nosso código normalmente dentro das funções `render_()`.

Sempre que você usar um input dentro de uma função `render_()`, o seu output se tornará reativo ao valor do input. Isso significa que, sempre que o usuário mudar o valor do input, o Shiny atualizará automaticamente o valor dentro da lista e também todas as funções `render_()` que dependam dele.

No nosso exemplo, sempre que o usuário mudar o número de classes no *slider*, o aplicativo atualizará o valor de `input$classes` e rodará novamente o código dentro da função `renderPlot()`. Assim, o objeto `output$hist` será atualizado.

A reatividade é um princípio essencial dentro do Shiny, e saber usá-la é primordial para que o aplicativo funcione corretamente e de forma eficiente. A seguir, exploraremos mais a fundo esse conceito e apresentaremos funções que auxiliam a manipular a reatividade.




## Reatividade

Para entender melhor como funciona a reatividade no Shiny, podemos pensar em uma fórmula do Excel. Se colocamos uma fórmula na célula A1 que utiliza as células B1 e C1, sempre que atualizarmos os valores de B1 e C1, o valor em A1 será automaticamente atualizado.

No Shiny, a ideia é exatamente a mesma. A diferença é que em vez de células, temos inputs e outputs. 

O fluxo de reatividade será sempre conduzido por valores e funções reativas. Os objetos dentro da lista `input` são os principais objetos reativos e as funções `render_()` são as principais funções reativas. 

Um fluxo básico seria o seguinte:

1. O usuário altera o valor do input `x`.
2. O valor reativo `input$x` é invalidado.
3. Toda função reativa que depender de `input$x` é notificada.
4. Essas funções verificam qual é o novo valor de `input$x` e atualizam suas saídas.

**Lembre-se**: valores reativos só trabalham com funções reativas. Tente, por exemplo, colocar um valor reativo dentro de uma função não-reativa.


```r
library(shiny)

ui <- fluidPage(
  sidebarLayout(
    sidebarPanel(
      numericInput(inputId = "num",
                   label = "Número  de observações",
                   value = 100)
    ),
    mainPanel(plotOutput(outputId = "hist"))
  )
)

server <- function(input, output) {
  
  hist(rnorm(input$num))
  
}

shinyApp(ui = ui, server = server)
```

Você receberá uma mensagem do tipo:

```
Error in .getReactiveEnvironment()\$currentContext() : 
  Operation not allowed without an active reactive context. (You tried to do something that can only be done from inside a reactive expression or observer.)
```
A forma correta seria usar a função `renderPlot()`.


```r
library(shiny)

ui <- fluidPage(
  sidebarLayout(
    sidebarPanel(
      numericInput(inputId = "num",
                   label = "Número  de observações",
                   value = 100)
    ),
    mainPanel(plotOutput(outputId = "hist"))
  )
)

server <- function(input, output) {
  
  output$hist <- renderPlot({hist(rnorm(input$num))})
  
}

shinyApp(ui = ui, server = server)
```

O Shiny disponibiliza funções para manipular a reatividade, alterando o fluxo básico apresentado acima.

- `reactive()` - Usada para criar objetos reativos. Elas funcionam tal como as funções `render_()`, mas não geram outputs para o *user side*.

- `isolate()` - Usada para inserir valores reativos dentro de funções reativas sem criar um fluxo de reatividade.

- `observeEvent()` - Usada como um gatilho para rodar código dentro do servidor. Esse código se refere a alguma ação, como imprimir uma mensagem no console ou salvar um arquivo. Geralmente usada com a função `actionButton()`, que criam botões de ação no *user side*. A função recebe um valor reativo e um bloco de código. Ela rodará o código sempre que o valor reativo especificado for atualizado. Valores reativos dentro do código que não o especificado funcionarão como se estivesse com `isolate()`.

- `observe()` - Também usada como um gatilho para rodar código no servidor, mas, diferentemente da `observeEvent()`, vai responder a qualquer valor reativo que estiver dentro do código. 

- `eventReactive()` - Funciona como a `oberserveEvent()`, mas em vez de executar uma ação, essa função é utilizada para criar valores reativos condicionados à atualização do valor reativo especificado como argumento. 
- `reactiveValues()` - Cria uma lista de valores reativos que podem ser manipulados no código.

A melhor dica para orientar a utilização dessas funções é fazer com que o seu código rode o menor número de vezes possível. Em um app complexo, saber o que deve ser ou não reativo pode não ser trivial. Por isso, o domínio desse conceito e dessas funções é importante para que o seu aplicativo seja eficiente, principalmente quando ele for ser utilizado por várias pessoas ao mesmo tempo.

Acesse o `help()` para mais informações sobre essas funções. Um tutorial muito mais completo sobre reatividade está disponível na [página do Shiny no site do Rstudio](https://shiny.rstudio.com/tutorial/written-tutorial/lesson6/).



## Costumizando aparência

Como já vimos na introdução desta seção, o objeto `ui` nada mais é do que um código em HTML estruturado pela função `fluidPage()`.

Quem conhece um pouco de HTML sabe que adicionamos conteúdo estático a uma página web a partir de tags. 

```
<div class = "container-fluid">
  <h1> Título </h1>
  <p style = "font-family: import">
    Veja exemplos de aplicativos na
    <a href = "https://shiny.rstudio.com/gallery/"> Shiny Gallery </a>
  </p>
</div>
```
Não se preocupe em entender o código acima. Ele é apenas um exemplo de como as tags funcionam no HTML. Veja, por exemplo, que a tag `<h1>` é utilizada para criar um título, a tag `<p>` cria parágrafos e a tag `<a>` cria hiperlinks. A tag `<div>` cria seções dentro da página com um mesmo estilo ou formato.

No Shiny, também podemos trabalhar com tags utilizando a função `tags()`. Essa função, na verdade, é uma lista de funções que mimetizam as tags do HTML. Veja as principais:

`tags$h1()` - Cria um título.
`tags$a()` - Cria um hiperlink.
`tags$p()` - Cria um parágrafo.
`tags$em()` - Formata o texto em itálico.
`tags$code()` - Formata o texto em código. 
`tags$strong()` - Formata o texto em negrito.
`tags$br()` - Pula uma linha.
`tags$hr()` - Cria uma linha horizontal.
`tags$img()` - Adiciona uma imagem (é preciso salvá-la em um subdiretório chamado `www`).


```r
library(shiny)

ui <- fluidPage(
  tags$h1("Utilizando tags no Shiny"),
  tags$hr(),
  h3("Uma visão geral"),
  br(),
  tags$p("O", strong("Shiny"), "permite a utilização de", tags$em("tags"),
         "assim como fazemons em", tags$code("HTML"), ".")
)

server <- function(input, output) {

}

shinyApp(ui, server)
```

<img src="figures/app-tags.png" title="plot of chunk unnamed-chunk-22" alt="plot of chunk unnamed-chunk-22" width="60%" height="60%" />


Algumas dessas funções vêm com um `wrapper` do tipo `nome-da-tag()`. Por exemplo, as funções `tags$h1()` e `h1()` são equivalentes.

Se você for bom em programação web, é possível escrever o objeto `ui` diretamente em HTML. Para passar código em HTML diretamente para a função `fluidPage()`, utilize a função `HTML()`.

Uma outra forma de customizar a aparência do seu app é alterar o layout da página. 

A página de um Shiny app tem sempre três dimensões: altura, largura e profundidade. Utilizando a função `fluidPage()`, a altura e a profundidade são variáveis, isto é, você pode controlá-las à vontade, mas a largura é fixa, formando um grid de 12 unidades. Essa unidades são adimensionais, o que significa que o app sempre vai se redimensionar para se ajustar a qualquer tela, sem criar uma barra de rolagem horizontal.

Para alterar o layout da página, você precisa usar as funções de layout:

- `fluidRow()` - Orienta a posição dos objetos dentro de uma mesma linha.
- `column()` - Orienta a posição dos objetos dentro de uma mesma coluna.
- `fixedPage()` - Usada no lugar da `fluidPage()`. Ela limita o seu comprimento em 940px em uma tela padrão e 724px ou 1170px em telas menores ou maiores respectivamente.
- `fixedRow()` - Usada no lugar de `fluidRow()` dentro da função `fixedPage()`.
- `navbarPage()` - Cria um layout de navegação por abas. Cada aba representa um nível de profundidade do aplicativo.
- `dashboardPage()` - Função do pacote `shinydashboad`. Usada para criar dashboards com o Shiny.


```r
library(shiny)

ui <- fluidPage(
  fluidRow(
    titlePanel("Modificando o layout do app"),
    column(3,
           h3("Coluna de tamanho 3"),
           sliderInput(inputId = "slider",
                       label = "Um slider",
                       min = 1,
                       max = 30,
                       value = 20)),
    column(6,
           h3("Coluna de tamanho 9"),
           radioButtons(inputId = "botao",
                        label = "Escolha",
                        choices = c("Sim", "Não"))),
    column(3,
           h3("Coluna de tamanho 3"),
           tags$p("A largura da página é um grid de tamanho 12."))
  ),
  fluidRow(
    tags$a("Interact. Analyze.
           Communicate. Take a fresh,
           interactive approach to telling
           your data story with Shiny. Let users interact with
           your data and your analysis. And do it all with R.
           Shiny is an R package that makes it easy to build
           interactive web apps straight from R.
           You can host standalone apps on a webpage or embed
           them in R Markdown documents or build dashboards.
           You can also extend your Shiny apps with CSS themes,
           htmlwidgets, and JavaScript actions.",
           href = "https://shiny.rstudio.com/")
  )
)

server <- function(input, output) {

}

shinyApp(ui, server)

```

<img src="figures/app-layout.png" title="plot of chunk unnamed-chunk-24" alt="plot of chunk unnamed-chunk-24" width="60%" height="60%" />

A profundidade em um Shiny app é acessada utilizando abas e painéis. As principais funções para trabalhar com essa feature são: `wellPanel()`, `tabPanel()`,  `tabsetPanel()` e `navlistPanel()`.

Deixamos como exercício entender a utilização de cada uma dessas funções. Lembre-se sempre de olhar o `help()` e visitar o [tutorial completo do Shiny](https://shiny.rstudio.com/tutorial/) no site do RStudio.

Também é possível trabalhar com CSS dentro do Shiny. Por padrão, o Shiny usa o framework [Bootstrap³](getbootstrap.com). Se você quiser usar um arquivo `.css` próprio, você precisa colocá-lo em um subdiretório chamado `www` e especificar o nome do arquivo no argumento `theme=` das funções `fluidPage()` ou `fixedPage()`.

Há outras duas formas de alterar o CSS. A primeira é utilizar a função `tags$style()` para alterar o CSS global. A segunda é modificar o argumento `style=` dentro das funções `tags$_()`. Assista a última parte [desse vídeo tutorial](https://shiny.rstudio.com/tutorial/) para mais informações.
   



## Compartilhando

Para compartilhar o seu app online, o Shiny oferece duas opções: o Shiny Server e o shinyapps.io. Veja a seguir mais detalhes sobre cada uma dessas opções.

**Nota**: independente da opção escolhida, para o compartilhamento online funcionar, o script com o código precisa ter o nome `app.R`.

### Shiny Server

O Shiny Server é um programa *backend* gratuito que monta um servidor web em linux feito para hospedar aplicativos em Shiny.

Para download, instruções de uso e mais informações, acesse o site: https://www.rstudio.com/products/shiny/shiny-server/.

O Shiny Server também tem uma versão paga, o [Shiny Server Pro](https://www.rstudio.com/products/shiny-server-pro/), que disponibiliza ferramentas de segurança, performance, gerencialmente e suporte.

### shinyapps.io

O shinyapps.io é um sistema do RStudio (que envolve tanto um pacote do R como uma página web) para a hospedagem de aplicativos em Shiny. As vantagens de se usar esse serviço são as seguintes:

- Você não vai precisar contratar um serviço de hospedagem nem configurar um servidor Linux.

- Ele é escalável, isto é, quando muita gente começa a usar o seu app, o servidor vai alocar mais processamento automaticamente para não perder performance.

- É seguro, fácil de usar e tem um versão gratuita.

Para começar a usar o shinyapps.io, você precisa:

**1.** Instalar o pacote `rsconnect`.


```r
devtools::install_github("rstudio/rsconnect")
```

**2.** Criar uma conta no [shinyapps.io](shinyapps.io).

**3.** No RStudio, rodar o app e clicar em *Publish*.

<img src="figures/app-publish.png" title="plot of chunk unnamed-chunk-26" alt="plot of chunk unnamed-chunk-26" width="60%" height="60%" />

Basta seguir as instruções e em alguns minutos você receberá um link para acessar o seu app online. Assim, tudo o que uma pessoa precisará para acessá-lo é um navegador web.

Para mais informações, acesse o [guia do usuário do shinyapps.io](http://docs.rstudio.com/shinyapps.io/).





