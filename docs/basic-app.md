# Your first Shiny app {#basic-app}



## Introduction

在本章中，我们将创建一个简单的 Shiny app。
我将首先向您展示 Shiny app 所需的最小样板，然后您将学习如何启动和停止它。
接下来，您将学习每个 Shiny app 的两个关键组件：定义 app 外观的 **UI**（user interface 的缩写），和定义 app 工作方式的 **server function**。
Shiny 使用**响应式编程（reactive programming）**在输入变化时自动更新输出，因此我们将通过学习 Shiny app 的第三个重要组件来结束本章：**响应式表达式（reactive expressions）**。

如果您尚未安装 Shiny，请立即安装：


```r
install.packages("shiny")
```

如果您已经安装了 Shiny，请使用 `packageVersion("shiny")` 检查您是否拥有 1.5.0 或更高版本。

然后加载当前的 R 会话：


```r
library(shiny)
```

## Create app directory and file {#create-app}

有多种方法可以创建 Shiny app。
最简单的方法是为您的 app 创建一个新目录，并在其中放置一个名为 `app.R` 的文件。
这个 `app.R` 文件将用于告诉 Shiny 您的 app 应该如何显示以及它应该如何运行。

通过创建一个新目录并添加一个如下所示的 `app.R` 文件来尝试一下：


```r
library(shiny)
ui <- fluidPage(
  "Hello, world!"
)
server <- function(input, output, session) {
}
shinyApp(ui, server)
```

这是一个完整但微不足道的 Shiny app！
仔细观察上面的代码，我们的 `app.R` 做了四件事：

1.  它调用 `library(shiny)` 来加载 shiny 包。

2.  它定义了用户界面，即人类与之交互的 HTML 网页。
    在本例中，它是一个包含 "Hello, world!" 字样的页面。

3.  它通过定义 `server` 函数来指定我们 app 的行为。
    它目前是空的，所以我们的应用程序不执行任何操作，但我们很快就会回来重新讨论这个问题。

4.  它执行 `shinyApp(ui, server)` 来从 UI 和 server 构建并启动 Shiny app。

::: {.rmdnote}
**RStudio Tip**: 在 RStudio 中创建一个新的 app 有两种便捷的方法：

-   单击 **File \| New Project** 创建一个新目录和一个包含基本 app 的 `app.R` 文件，然后选择 **New Directory** 和 **Shiny Web Application**。

-   如果您已经创建了 `app.R` 文件，则可以通过键入 "shinyapp" 并按 Shift+Tab 快速添加 app 样板。
:::

## Running and stopping {#running}

您可以通过以下几种方式运行此 app：

-   单击文档工具栏中的 **Run App** (Figure \@ref(fig:run-app)) 按钮。

-   使用键盘快捷键：`Cmd/Ctrl` + `Shift` + `Enter`。

-   如果您不使用 RStudio，您可以 `(source())`[^basic-app-1] 整个文档，或者使用包含 `app.R` 的目录的路径调用 `shiny::runApp()`。

[^basic-app-1]: 外面额外的 `()` 很重要。
    `shinyApp()` 仅在打印时创建一个应用程序，而 `()` 强制打印源文件中的最后结果，否则将以不可见的方式返回。

<div class="figure">
<img src="images/basic-app/run-app.png" alt="Run App 按钮位于源窗格的右上角。" width="617" />
<p class="caption">(\#fig:run-app)Run App 按钮位于源窗格的右上角。</p>
</div>

选择这些选项之一，并检查您是否看到与 Figure \@ref(fig:hello-world) 中相同的 app。
恭喜！
您已经制作了第一个 Shiny app。

<div class="figure">
<img src="images/basic-app/hello-world.png" alt="当您运行上面的代码时，您将看到一个非常基本的 shiny app" width="440" />
<p class="caption">(\#fig:hello-world)当您运行上面的代码时，您将看到一个非常基本的 shiny app</p>
</div>

在关闭 app 之前，请返回 RStudio 并查看 R console。
你会注意到它说的是这样的：


```r
#> Listening on http://127.0.0.1:3827
```

这告诉您可以找到您的 app 的 URL：127.0.0.1 是一个标准地址，表示“这台计算机”，3827 是一个随机分配的端口号（port）。
您可以在任何兼容的[^basic-app-2]网络浏览器中输入该 URL 以打开你的 app 的另一个副本。

[^basic-app-2]: Shiny 致力于支持所有现代浏览器，您可以在 <https://www.rstudio.com/about/platform-support/> 上查看当前支持的浏览器集。
    请注意，直接从 R 会话运行 Shiny 时，IE11 之前的 Internet Explorer 版本不兼容。
    但是，部署在 Shiny Server 或 ShinyApps.io 上的 Shiny apps 可以与 IE10 一起使用（不再支持早期版本的 IE）。

另请注意 R 正忙：R 提示不可见，并且控制台工具栏显示停止标志图标。
当 Shiny app 运行时，它会“阻止” R console。
这意味着在 Shiny app 停止之前，您无法在 R console 上运行新命令。

您可以使用以下任一选项停止 app 并返回对 console 的访问权限：

-   单击 R console 工具栏上的停止标志图标。

-   单击 console，然后按 `Esc`（如果您不使用 RStudio，则按 `Ctrl` + `C`）。

-   关闭 Shiny app 窗口。

Shiny app 开发的基本工作流程是编写一些代码，启动 app，使用 app，编写更多代码，然后重复。
如果您使用的是 RStudio，您甚至不需要停止并重新启动应用程序即可查看更改 --- 您可以按工具箱中的 **Reload app** 按钮或使用 `Cmd/Ctrl` + `Shift` + `Enter` 键盘快捷键。
我将在 Chapter \@ref(action-workflow) 中介绍其他工作流程模式。

## Adding UI controls {#adding-ui}

接下来，我们将向 UI 添加一些输入和输出，这样它就不会那么小了。
我们将制作一个非常简单的 app，向您显示 datasets 包中包含的所有内置 data frames。

将您的 `ui` 替换为以下代码：


```r
ui <- fluidPage(
  selectInput("dataset", label = "Dataset", choices = ls("package:datasets")),
  verbatimTextOutput("summary"),
  tableOutput("table")
)
```

此示例使用四个新函数：

-   `fluidPage()` 是一个 **layout function**，用于设置页面的基本视觉结构。
    您将在 Section \@ref(layout) 中了解有关它们的更多信息。

-   `selectInput()` 是一个 **input control**，允许用户通过提供值（value）与 app 交互。
    在本例中，它是一个带有 "Dataset" 标签的选择框，可让您选择 R 附带的内置数据集之一。
    您将在 Section \@ref(inputs) 中了解有关输入的更多信息。

-   `verbatimTextOutput()` 和 `tableOutput()` 是 **output controls**，它们告诉 Shiny 将渲染的输出放在哪里（我们稍后会介绍如何放置）。 
    `verbatimTextOutput()` 显示代码，`tableOutput()` 显示表格。
    您将在 Section \@ref(outputs) 中了解有关输出的更多信息。

布局函数、输入和输出有不同的用途，但它们本质上是相同的：它们都是生成 HTML 的奇特方法，如果您在 Shiny app 之外调用其中任何一个，您将看到 HTML 在控制台打印出来。
不要害怕四处探索，看看这些不同的布局和控件在幕后是如何工作的。

继续并再次运行该 app。
现在您将看到 Figure \@ref(fig:basic-ui)，这是一个包含选择框的页面。
我们只看到输入，看不到两个输出，因为我们还没有告诉 Shiny 输入和输出是如何关联的。

<div class="figure">
<img src="demos/basic-app/ui.png" alt="The datasets app with UI" width="600" />
<p class="caption">(\#fig:basic-ui)The datasets app with UI</p>
</div>

## Adding behaviour {#server-function}

接下来，我们将通过在 server 函数中定义输出来使输出变得生动。

Shiny 使用响应式编程使 apps 具有交互性。
您将在 Chapter \@ref(basic-reactivity) 中了解有关响应式编程的更多信息，但现在请注意，它涉及告诉 Shiny 如何执行计算，而不是命令 Shiny 实际执行计算。
这就像给某人一个菜谱和要求他们给你做一个三明治之间的区别。

我们将通过提供这些输出的“配方”来告诉 Shiny 如何在示例 app 中填写 `summary` 和 `table` 输出。
将空的 `server` 函数替换为：


```r
server <- function(input, output, session) {
  output$summary <- renderPrint({
    dataset <- get(input$dataset, "package:datasets")
    summary(dataset)
  })
  
  output$table <- renderTable({
    dataset <- get(input$dataset, "package:datasets")
    dataset
  })
}
```

赋值运算符 (`<-`) 的左侧，`output$ID`，表示您正在为具有该 ID 的 Shiny 输出提供配方。
赋值的右侧使用特定的**渲染函数（render function）**来包装您提供的一些代码。
每个 `render{Type}` 函数都旨在生成特定类型的输出（例如文本、表格和绘图），并且通常与 `{type}Output` 函数配对。
例如，在此 app 中，`renderPrint()` 与 `verbatimTextOutput()` 配合使用，以显示固定宽度（逐字）文本的统计摘要，而 `renderTable()` 与 `tableOutput()` 配合使用，以在表格中显示输入数据。

再次运行 app 并进行测试，观察更改输入时输出会发生什么情况。
Figure \@ref(fig:basic-server) 显示了打开 app 时应该看到的内容。

<div class="figure">
<img src="demos/basic-app/server.png" alt="现在我们已经提供了连接输出和输入的 server function，我们有了一个功能齐全的 app" width="75%" />
<p class="caption">(\#fig:basic-server)现在我们已经提供了连接输出和输入的 server function，我们有了一个功能齐全的 app</p>
</div>

请注意，每当您更改输入数据集时，摘要和表格都会更新。
这种依赖关系是隐式创建的，因为我们在输出函数中引用了 `input$dataset`。
`input$dataset` 填充了带有 id `dataset` 的 UI 组件的当前值，并且每当该值发生变化时都会导致输出自动更新。
这是**响应式（reactivity）**的本质：当输入发生变化时，输出会自动做出响应（重新计算）。

## Reducing duplication with reactive expressions {#reactive-expr}

即使在这个简单的示例中，我们也有一些重复的代码：两个输出中都存在以下行。


```r
dataset <- get(input$dataset, "package:datasets")
```

在每种编程中，重复代码都是不好的做法；它可能会造成计算浪费，更重要的是，它增加了维护或调试代码的难度。
这在这里并不重要，但我想在一个非常简单的上下文中说明基本思想。

不幸的是，这些方法在这里都不起作用，原因您将在 Section \@ref(motivation) 中了解，并且我们需要一种新机制：**响应式表达式（reactive expressions）**。

您可以通过将代码块包装在 `reactive({...})` 中并将其分配给变量来创建响应式表达式，然后通过像函数一样调用它来使用响应式表达式。
但是，虽然看起来像是在调用函数，但响应式表达式有一个重要的区别：它仅在第一次调用时运行，然后缓存其结果，直到需要更新为止。

我们可以更新我们的 `server()` 以使用响应式表达式，如下所示。
该 app 的行为相同，但工作效率更高一些，因为它只需要检索数据集一次，而不是两次。


```r
server <- function(input, output, session) {
  # Create a reactive expression
  dataset <- reactive({
    get(input$dataset, "package:datasets")
  })

  output$summary <- renderPrint({
    # Use a reactive expression by calling it like a function
    summary(dataset())
  })
  
  output$table <- renderTable({
    dataset()
  })
}
```

我们将多次回到响应式编程，但即使具备输入、输出和响应式表达式的粗略知识，也可以构建非常有用的 Shiny apps！


## Summary

在本章中，您创建了一个简单的 app --- 它不是很令人兴奋或有用，但您看到了使用现有的 R 知识构建一个 web app 是多么容易。
在接下来的两章中，您将了解有关用户界面和响应式编程的更多信息，这是 Shiny 的两个基本构建块。
现在是获取 [Shiny cheatsheet](https://rstudio.github.io/cheatsheets/shiny.pdf) 的好时机。
这是一个很好的资源，可以帮助您回忆 Shiny app 的主要组件。

<div class="figure">
<img src="images/basic-app/cheatsheet.png" alt="Shiny cheatsheet, available from https://www.rstudio.com/resources/cheatsheets/" width="352" />
<p class="caption">(\#fig:cheatsheet)Shiny cheatsheet, available from https://www.rstudio.com/resources/cheatsheets/</p>
</div>

## Exercises

1.  创建一个通过名字向用户打招呼的 app。
    您还不知道执行此操作所需的所有函数，因此我在下面添加了一些代码行。
    考虑一下您将使用哪些行，然后将它们复制并粘贴到 Shiny app 中的正确位置。

    
    ```r
    tableOutput("mortgage")
    output$greeting <- renderText({
      paste0("Hello ", input$name)
    })
    numericInput("age", "How old are you?", value = NA)
    textInput("name", "What's your name?")
    textOutput("greeting")
    output$histogram <- renderPlot({
      hist(rnorm(1000))
    }, res = 96)
    ```

2.  假设您的朋友想要设计一个 app，允许用户设置 1 到 50 之间的数字 (`x`)，并显示该数字乘以 5 的结果。
    这是他们的第一次尝试：

    
    ```r
    library(shiny)
    
    ui <- fluidPage(
      sliderInput("x", label = "If x is", min = 1, max = 50, value = 30),
      "then x times 5 is",
      textOutput("product")
    )
    
    server <- function(input, output, session) {
      output$product <- renderText({ 
        x * 5
      })
    }
    
    shinyApp(ui, server)
    ```

    但不幸的是它有一个错误：

    <img src="demos/basic-app/ex-x-times-5.png" width="600" />

    你能帮助他们找到并纠正错误吗？

3.  扩展上一个练习中的 app，以允许用户设置乘数 `y` 的值，以便应用程序生成 `x * y` 的值。
    最终结果应该是这样的：

    <img src="demos/basic-app/ex-x-times-y.png" width="600" />

4.  使用以下 app，它为上一个练习中描述的最后一个 app 添加了一些附加功能。
    什么是新的？
    如何通过使用响应式表达式来减少 app 中重复代码的数量。

    
    ```r
    library(shiny)
    
    ui <- fluidPage(
      sliderInput("x", "If x is", min = 1, max = 50, value = 30),
      sliderInput("y", "and y is", min = 1, max = 50, value = 5),
      "then, (x * y) is", textOutput("product"),
      "and, (x * y) + 5 is", textOutput("product_plus5"),
      "and (x * y) + 10 is", textOutput("product_plus10")
    )
    
    server <- function(input, output, session) {
      output$product <- renderText({ 
        product <- input$x * input$y
        product
      })
      output$product_plus5 <- renderText({ 
        product <- input$x * input$y
        product + 5
      })
      output$product_plus10 <- renderText({ 
        product <- input$x * input$y
        product + 10
      })
    }
    
    shinyApp(ui, server)
    ```

5.  以下 app 与您在本章前面看到的 app 非常相似：您从包中选择一个数据集（这次我们使用 **ggplot2** 包），然后该 app 打印出数据的摘要和绘图。
    它还遵循良好实践并利用响应式表达式来避免代码冗余。
    然而，下面提供的代码中存在三个错误。
    你能找到并修复它们吗？

    
    ```r
    library(shiny)
    library(ggplot2)
    #> Warning: package 'ggplot2' was built under R version 4.2.3
    
    datasets <- c("economics", "faithfuld", "seals")
    ui <- fluidPage(
      selectInput("dataset", "Dataset", choices = datasets),
      verbatimTextOutput("summary"),
      tableOutput("plot")
    )
    
    server <- function(input, output, session) {
      dataset <- reactive({
        get(input$dataset, "package:ggplot2")
      })
      output$summmry <- renderPrint({
        summary(dataset())
      })
      output$plot <- renderPlot({
        plot(dataset)
      }, res = 96)
    }
    
    shinyApp(ui, server)
    ```
