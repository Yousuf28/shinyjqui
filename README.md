
<!-- README.md is generated from README.Rmd. Please edit that file -->
shinyjqui
=========

[![Travis-CI Build Status](https://travis-ci.org/Yang-Tang/shinyjqui.svg?branch=master)](https://travis-ci.org/Yang-Tang/shinyjqui)

The shinyjqui package a wrapper of [jQuery UI](http://jqueryui.com/) javascript library. It allows user to easily add ineractions and animation effects to elements of a shiny app.

Installation
------------

You can install shinyjqui from github with:

``` r
# install.packages("devtools")
devtools::install_github("yang-tang/shinyjqui")
```

Example
-------

Here are some basic examples:

``` r
# load packages
library(shiny)
library(shinyjqui)
library(ggplot2)
library(highcharter)
```

-   **Draggable:** Allow elements to be moved using the mouse

``` r
server <- function(input, output) {}

ui <- fluidPage(
  includeJqueryUI(), # Load necessary web assets
  jqui_draggabled(fileInput('file', 'File'))
)

shinyApp(ui, server)
```

-   **Resizable:** Change the size of an element using the mouse.

``` r
server <- function(input, output) {
  output$gg <- renderPlot({
    ggplot(mtcars, aes(x = cyl, y = mpg)) + geom_point()
  })
}

ui <- fluidPage(
  includeJqueryUI(), # Load necessary web assets
  jqui_resizabled(plotOutput('gg', width = '200px', height = '200px'))
)

shinyApp(ui, server)
```

-   **Sortable:** Reorder elements in a list or grid using the mouse.

``` r
server <- function(input, output) {
  output$hc <- renderHighchart({
    hchart(mtcars, "scatter", hcaes(x = cyl, y = mpg, group = factor(vs))) %>% 
      hc_legend(enabled = FALSE)
  })
  output$gg <- renderPlot({
    ggplot(mtcars, aes(x = cyl, y = mpg, color = factor(vs))) + 
      geom_point() + 
      theme(legend.position= "none")
  })
}

ui <- fluidPage(
  includeJqueryUI(), # Load necessary web assets
  jqui_sortabled(div(id = 'plots',
                     highchartOutput('hc', width = '200px', height = '200px'),
                     plotOutput('gg', width = '200px', height = '200px')))
)

shinyApp(ui, server)
```

-   **Animation Effects:** Apply an animation effect to an element. Effects can also be used in hide or show.

``` r
server <- function(input, output) {
  observeEvent(input$show, {
    jqui_show('#gg', effect = input$effect)
  })
  
  observeEvent(input$hide, {
    jqui_hide('#gg', effect = input$effect)
  })
  
  output$gg <- renderPlot({
    ggplot(mtcars, aes(x = cyl, y = mpg, color = factor(gear))) +
      geom_point() +
      theme(plot.background = element_rect(fill = "transparent",colour = NA))
  }, bg = "transparent")
}

ui <- fluidPage(
  includeJqueryUI(), # Load necessary web assets
  div(style = 'width: 400px; height: 400px',
      plotOutput('gg', width = '100%', height = '100%')),
  selectInput('effect', NULL, choices = get_jqui_effects()),
  actionButton('show', 'Show'),
  actionButton('hide', 'Hide')
)

shinyApp(ui, server)
```

-   **Classes transfermation:** Add and remove class(es) to elements while animating all style changes.

``` r

server <- function(input, output) {

  current_class <- c()

  observe({
    input$class
    class_to_remove <- setdiff(current_class, input$class)
    class_to_add <- setdiff(input$class, current_class)
    current_class <<- input$class
    if(length(class_to_remove) > 0) {
      jqui_remove_class('#foo', paste(class_to_remove, collapse = ' '), duration = 1000)}
    if(length(class_to_add) > 0) {
      jqui_add_class('#foo', paste(class_to_add, collapse = ' '), duration = 1000)}
  })

}

ui <- fluidPage(

  includeJqueryUI(), # Load necessary web assets

  tags$head(
    tags$style(
      HTML('.class1 { width: 410px; height: 100px; }
            .class2 { text-indent: 40px; letter-spacing: .2em; }
            .class3 { padding: 30px; margin: 10px; }
            .class4 { font-size: 1.1em; }')
    )
  ),

  div(id = 'foo', 'Etiam libero neque, luctus a, eleifend nec, semper at, lorem. Sed pede.'),
  hr(),
  checkboxGroupInput('class', 'Class',
                     choices = list(`width: 410px; height: 100px;` = 'class1',
                                    `text-indent: 40px; letter-spacing: .2em;` = 'class2',
                                    `padding: 30px; margin: 10px;` = 'class3',
                                    `font-size: 1.1em;` = 'class4'))


)

shinyApp(ui, server)
```