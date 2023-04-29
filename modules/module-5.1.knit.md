---
title: "Module 5.1"
subtitle: "Your First Shiny App"
format: html
highlight-style: atom-one
execute:
  echo: true
  message: false
  warning: false
---


## Scatter plot app

<iframe src="https://emmanuelteitelbaum.shinyapps.io/scatterplot/" width="800" height="470" data-external="1"></iframe>

## Setup 


::: {.cell}

```{.r .cell-code}
# load packages
library(shiny)
library(readr)
library(ggplot2)

# read in data, create a vector of variable names
dem_data <- read_csv("dem_data.csv")

# create list of named values for the input selection
vars <- c("Democracy" = "polyarchy",
          "Clientelism" = "clientelism",
          "Corruption" = "corruption",
          "Women's Empowerment" = "womens_emp",
          "Wealth" = "gdp_pc",
          "Infant Mortality" = "inf_mort",
          "Life Expectancy" = "life_exp", 
          "Education" = "education")
```
:::



## ui 


::: {.cell}

```{.r .cell-code}
# Define UI for application that draws a histogram
ui <- fluidPage(

    # Application title
    titlePanel("Democracy and Development"),

    # Sidebar with a slider input for number of bins 
    sidebarLayout(
      sidebarPanel(
        selectInput('xcol', 'X Variable', vars),
        selectInput('ycol', 'Y Variable', vars, selected = vars[[6]])
      ),

        # Show a plot of the generated distribution
        mainPanel(
           plotOutput("scatterplot")
        )
    )
)
```
:::



## Server


::: {.cell}

```{.r .cell-code}
# Define server logic required to draw a scatter plot
server <- function(input, output, session) {
  
  # Combine the selected variables into a new data frame

  
  # Render the plot
  output$scatterplot <- renderPlot({
    
    selectedData <- dem_data[, c(input$xcol, input$ycol, "region")]
    
    ggplot(selectedData(), aes_string(x = input$xcol, y = input$ycol)) +
      geom_point(aes(color = region)) +
      geom_smooth(method = "loess") +
      scale_color_viridis_d(option = "plasma") +
      theme_minimal() +
      labs(
        x =  names(vars[which(vars == input$xcol)]),
        y =  names(vars[which(vars == input$ycol)]),
        caption = "Source: V-Dem Institute", # caption
        color = "Region" # legend title
      )
  })
}
```
:::


## Call to Shiny app


::: {.cell}

```{.r .cell-code}
# See above for the definitions of ui and server
ui <- ...

server <- ...

# Run the application 
shinyApp(ui = ui, server = server)
```
:::
