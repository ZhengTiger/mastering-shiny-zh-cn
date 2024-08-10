# Workflow {#action-workflow}



如果您要编写大量 Shiny apps（既然您正在阅读这本书，我希望您会这么做！），那么在基本工作流程上投入一些时间是值得的。
改进工作流程是投入时间的好地方，因为从长远来看，它往往会带来巨大的回报。
它不仅增加了您编写 R 代码所花费的时间比例，而且因为您更快地看到结果，它使编写 Shiny apps 的过程变得更加愉快，并帮助您更快地提高技能。

本章的目标是帮助您改进三个重要的 Shiny 工作流程：

-   创建 apps、进行更改和试验结果的基本开发周期。

-   调试，在这个工作流程中，您找出代码出了什么问题，然后集体讨论解决方案来修复它。

-   编写 reprexes，即说明问题的独立代码块。
    Reprexes 是一种强大的调试技术，如果您想从其他人那里获得帮助，它们是必不可少的。

## Development workflow

优化开发工作流程的目标是缩短做出更改和看到结果之间的时间。
你迭代的速度越快，你的实验速度就越快，你就能越快地成为一名更好的 Shiny 开发人员。
这里有两个主要的工作流程需要优化：首次创建 app，以及加快调整代码和尝试结果的迭代周期。

### Creating the app

您将使用相同的六行 R 代码启动每个 app：


```r
library(shiny)
ui <- fluidPage(
)
server <- function(input, output, session) {
}
shinyApp(ui, server)
```

您可能很快就会厌倦输入这些代码，因此 RStudio 提供了一些快捷方式：

-   如果您已经打开了未来的 `app.R`，请输入 `shinyapp`，然后按 `Shift` + `Tab` 插入 Shiny app 片段。[^action-workflow-1]

-   如果您想启动一个新项目[^action-workflow-2]，请进入 File 菜单，选择 "New Project"，然后选择 "Shiny Web Application"，如 Figure \@ref(fig:new-project) 所示。

[^action-workflow-1]: Snippets 是文本宏，可用于插入常见代码片段。
    有关更多详细信息，请参阅 <https://support.rstudio.com/hc/en-us/articles/204463668-Code-Snippets>。
    如果您喜欢使用 snippets，请务必查看 ThinkR 整理的 Shiny 特定 snippets 集合：<https://github.com/ThinkR-open/shinysnippets>。

[^action-workflow-2]: 项目是一个独立的目录，与您正在处理的其他项目隔离。
    如果您使用 RStudio，但目前不使用项目，我强烈建议您阅读有关 [project oriented lifestyle](https://whattheyforgot.org/project-oriented-workflow.html) 的文章。

<div class="figure">
<img src="images/action-workflow/new-project.png" alt="要在 RStudio 中创建新的 Shiny app，请选择 'Shiny Web Application' 作为项目类型" width="364" />
<p class="caption">(\#fig:new-project)要在 RStudio 中创建新的 Shiny app，请选择 'Shiny Web Application' 作为项目类型</p>
</div>

您可能认为学习这些快捷方式不值得，因为您每天只会创建一两个 app，但是创建简单的 app 是在开始更大的项目之前检查您是否掌握了基本概念的好方法，并且它们是一个很棒的调试工具。

### Seeing your changes

最多，您一天会创建几个 apps，但您会运行 apps 数百次，因此掌握开发工作流程尤为重要。
减少迭代时间的第一个方法是避免单击 "Run App" 按钮，而是学习键盘快捷键 `Cmd/Ctrl` + `Shift` + `Enter`。
这将为您提供以下开发工作流程：

1.  写一些代码。
2.  使用 `Cmd/Ctrl` + `Shift` + `Enter` 启动 app。
3.  使用该 app 进行交互式实验。
4.  关闭 app。
5.  回到 1。

进一步提高迭代速度的另一种方法是打开自动重新加载并在后台作业中运行 app，如 <https://github.com/sol-eng/background-jobs/tree/master/shiny-job> 中所述。
通过此工作流程，只要您保存文件，您的 app 就会重新启动：无需关闭并重新启动。
这会带来更快的工作流程：

1.  编写一些代码并按 `Cmd/Ctrl` + `S` 保存文件。
2.  交互式实验。
3.  回到 1。

这种技术的主要缺点是调试起来相当困难，因为 app 在单独的进程中运行。

随着您的 app 变得越来越大，您会发现“交互式实验”步骤开始变得繁重。
记住重新检查 app 中可能受到更改影响的每个组件太困难了。
稍后，在 Chapter \@ref(scaling-testing) 中，您将学习自动化测试工具，它允许您将正在运行的交互式实验转换为自动化代码。
这可以让您更快地运行测试（因为它们是自动化的），并且意味着您不会忘记运行重要的测试。
开发测试需要一些初始投资，但对于大型 apps 来说，投资回报丰厚。

### Controlling the view

默认情况下，当您运行 app 时，它将显示在弹出窗口中。
您可以从 Run App 下拉列表中选择其他两个选项，如 Figure \@ref(fig:run-app) 所示：

-   **Run in Viewer Pane** 会在 viewer 窗格（通常位于 IDE 的右侧）中打开 app。
    它对于较小的 apps 很有用，因为您可以在运行 app 代码的同时看到它。

-   **Run External** 在您常用的网络浏览器中打开 app。
    它对于较大的 apps 以及当您想查看 app 在大多数用户将体验的环境的外观时非常有用。

<div class="figure">
<img src="images/action-workflow/run-app.png" alt="Run App 按钮允许您选择如何显示正在运行的 app。" width="125" />
<p class="caption">(\#fig:run-app)Run App 按钮允许您选择如何显示正在运行的 app。</p>
</div>

## Debugging

当你开始编写 apps 时，几乎肯定会出现问题。
大多数错误的原因是您对 Shiny 的心理模型与 Shiny 实际所做的事情不匹配。
当你阅读这本书时，你的心理模型将会得到改善，从而减少你犯的错误，当你犯错误时，你会更容易发现问题。
然而，在您能够可靠地编写首次运行的代码之前，您需要拥有多年使用任何语言的经验。
这意味着您需要开发一个强大的工作流程来识别和修复错误。
在这里，我们将重点关注 Shiny apps 特有的挑战；如果您不熟悉 R 调试，请从 Jenny Bryan 的 rstudio::conf(2020) 主题演讲 "[Object of type 'closure' is not subsettable](https://resources.rstudio.com/rstudio-conf-2020/object-of-type-closure-is-not-subsettable-jenny-bryan)" 开始。

我们将在下面讨论三种主要的问题情况：

-   您收到意外报错。
    这是最简单的情况，因为您将获得回溯，使您能够准确地找出错误发生的位置。
    一旦发现问题，您就需要系统地测试您的假设，直到发现您的期望与现实之间的差异。
    交互式调试器是这个过程的有力助手。

-   您没有收到任何报错，但某些值不正确。
    在这里，您需要使用交互式调试器以及您的调查技能来追踪根本原因。

-   所有值都是正确的，但它们没有按您的预期更新。
    这是最具挑战性的问题，因为它是 Shiny 所独有的，因此您无法利用现有的 R 调试技能。

出现这些情况时会令人沮丧，但您可以将其转化为练习调试技能的机会。

在下一节中，我们将回到另一个重要的技术，制作一个最小的可重复示例。
如果您遇到困难并需要从其他人那里获得帮助，那么创建一个最小的示例至关重要。
但是，在调试自己的代码时，创建一个最小的示例也是一项非常重要的技能。
通常情况下，您有很多代码可以正常工作，而只有极少量的代码会导致问题。
如果您可以通过删除有效的代码来缩小有问题的代码范围，那么您将能够更快地迭代解决方案。
这是我每天都使用的技巧。

### Reading tracebacks

在 R 中，每个错误都伴随着回溯或调用堆栈，它实际上可以追溯到导致错误的调用序列。
例如，采用以下简单的调用序列：`f()` 调用 `g()` 调用 `h()`，后者调用乘法运算符：


```r
f <- function(x) g(x)
g <- function(x) h(x)
h <- function(x) x * 2
```

如果这段代码出错，如下：


```r
f("a")
#> Error in x * 2: non-numeric argument to binary operator
```

您可以调用 `traceback()` 来查找问题的调用顺序：


```r
traceback()
#> 3: h(x)
#> 2: g(x)
#> 1: f("a")
```

我认为将回溯颠倒过来是最容易理解的：

    1: f("a")
    2: g(x)
    3: h(x)

现在，这会告诉您导致错误的调用顺序 --- `f()` 调用 `g()` 调用 `h()` （哪些错误）。

### Tracebacks in Shiny

不幸的是，您无法在 Shiny 中使用 `traceback()`，因为您无法在 app 运行时运行代码。
相反，Shiny 会自动为您打印回溯。
例如，使用我上面定义的 `f()` 函数来获取这个简单的 app：


```r
library(shiny)

f <- function(x) g(x)
g <- function(x) h(x)
h <- function(x) x * 2

ui <- fluidPage(
  selectInput("n", "N", 1:10),
  plotOutput("plot")
)
server <- function(input, output, session) {
  output$plot <- renderPlot({
    n <- f(input$n)
    plot(head(cars, n))
  }, res = 96)
}
shinyApp(ui, server)
```

如果您运行此 app，您将在 app 中看到一条错误消息，并在控制台中看到一条回溯：

    Error in *: non-numeric argument to binary operator
      169: g [app.R#4]
      168: f [app.R#3]
      167: renderPlot [app.R#13]
      165: func
      125: drawPlot
      111: <reactive:plotObj>
       95: drawReactive
       82: renderFunc
       81: output$plot
        1: runApp

为了了解发生了什么，我们再次将其颠倒过来，这样您就可以按照调用的出现顺序查看调用顺序：

    Error in *: non-numeric argument to binary operator
       1: runApp
      81: output$plot
      82: renderFunc
      95: drawReactive
     111: <reactive:plotObj>
     125: drawPlot
     165: func
     167: renderPlot [app.R#13]
     168: f [app.R#3]
     169: g [app.R#4]

调用堆栈由三个基本部分组成：

-   前几次调用会启动 app。在这种情况下，您只会看到 `runApp()`，但根据您启动 app 的方式，您可能会看到更复杂的内容。
    例如，如果您调用 `source()` 来运行 app，您可能会看到以下内容：

        1: source
        3: print.shiny.appobj
        5: runApp

    一般来说，您可以忽略第一个 `runApp()`; 之前的任何内容；这只是让 app 运行的设置代码。

-   接下来，您将看到一些负责调用响应式表达式的内部 Shiny 代码：

         81: output$plot
         82: renderFunc
         95: drawReactive
        111: <reactive:plotObj>
        125: drawPlot
        165: func

    在这里，发现 `output$plot` 非常重要 --- 它可以告诉您哪个响应式（`plot`）导致了错误。
    接下来的几个函数是内部函数，您可以忽略它们。

-   最后，在最底部，您将看到您编写的代码：

        167: renderPlot [app.R#13]
        168: f [app.R#3]
        169: g [app.R#4]

    这是 `renderPlot()` 内部调用的代码。
    由于文件路径和行号，您可以告诉您应该注意这里；这让您知道这是您的代码。

如果您的 app 出现错误，但没有看到回溯，请确保您正在使用 `Cmd/Ctrl` + `Shift` + `Enter` 运行 app（或者如果不在 RStudio 中，则调用 `runApp()`），并且您已经保存了您运行它的文件。
运行 app 的其他方式并不总能捕获进行回溯所需的信息。

### The interactive debugger {#browser}

一旦找到错误的根源并想找出导致错误的原因，您可以使用的最强大的工具就是交互式调试器。
调试器会暂停执行，并为您提供一个交互式 R 控制台，您可以在其中运行任何代码来找出问题所在。
有两种启动调试器的方法：

-   在源代码中添加对 `browser()` 的调用。
    这是启动交互式调试器的标准 R 方式，无论您如何运行 Shiny，它都可以工作。

    `browser()` 的另一个优点是，因为它是 R 代码，所以您可以通过将其与 `if` 语句组合来使其成为条件。
    这允许您仅针对有问题的输入启动调试器。

    
    ```r
    if (input$value == "a") {
      browser()
    }
    # Or maybe
    if (my_reactive() < 0) {
      browser()
    }
    ```

-   单击行号左侧添加 RStudio 断点。
    您可以通过单击红色圆圈来删除断点。

    <img src="images/action-workflow/breakpoint.png" width="317" />

    断点的优点是它们不是代码，因此您永远不必担心意外地将它们签入版本控制系统。

如果您使用 RStudio，当您处于调试器中时，Figure \@ref(fig:debug-toolbar) 中的工具栏将出现在控制台顶部。
工具栏是记住现在可用的调试命令的简单方法。
它们也可以在 RStudio 之外使用；您只需要记住一个字母命令即可激活它们。
三个最有用的命令是：

-   下一步（按 `n`）：执行函数中的下一步。
    请注意，如果您有一个名为 `n` 的变量，则需要使用 `print(n)` 来显示其值。

-   继续（按 `c`）：离开交互式调试并继续函数的常规执行。
    如果您已经修复了错误状态并想要检查函数是否正常运行，这非常有用。

-   停止（按 `Q`）：停止调试，终止函数，并返回全局工作区。
    一旦找出问题所在，并准备好修复它并重新加载代码，就可以使用它。

<div class="figure">
<img src="images/action-workflow/debug-toolbar.png" alt="RStudio's debugging toolbar" width="50%" />
<p class="caption">(\#fig:debug-toolbar)RStudio's debugging toolbar</p>
</div>

除了使用这些工具逐行执行代码之外，您还将编写并运行一堆交互式代码来跟踪出了什么问题。
调试是系统地将您的期望与现实进行比较，直到发现不匹配的过程。
如果您不熟悉 R 调试，您可能需要阅读 "Advanced R" 的 [Debugging chapter](https://adv-r.hadley.nz/debugging.html#debugging-strategy) 来学习一些通用技术。

### Case study

> Once you eliminate the impossible, whatever remains, no matter how improbable, must be the truth --- Sherlock Holmes

为了演示基本的调试方法，我将向您展示我在编写 Section \@ref(hierarchical-select) 时遇到的一个小问题。
我将首先向您展示基本情景，然后您会看到一个我没有使用交互式调试工具解决的问题，一个需要交互式调试的问题，并发现最后的惊喜。

最初的目标非常简单：我有一个销售数据集，我想按地区过滤它。
数据如下：


```r
sales <- readr::read_csv("sales-dashboard/sales_data_sample.csv")
sales <- sales[c(
  "TERRITORY", "ORDERDATE", "ORDERNUMBER", "PRODUCTCODE",
  "QUANTITYORDERED", "PRICEEACH"
)]
sales
#> # A tibble: 2,823 × 6
#>   TERRITORY ORDERDATE      ORDERNUMBER PRODUCTCODE QUANTITYORDERED PRICEEACH
#>   <chr>     <chr>                <dbl> <chr>                 <dbl>     <dbl>
#> 1 <NA>      2/24/2003 0:00       10107 S10_1678                 30      95.7
#> 2 EMEA      5/7/2003 0:00        10121 S10_1678                 34      81.4
#> 3 EMEA      7/1/2003 0:00        10134 S10_1678                 41      94.7
#> 4 <NA>      8/25/2003 0:00       10145 S10_1678                 45      83.3
#> # ℹ 2,819 more rows
```

以下是 territories：


```r
unique(sales$TERRITORY)
#> [1] NA      "EMEA"  "APAC"  "Japan"
```

当我第一次开始解决这个问题时，我认为它很简单，我可以编写 app 而无需进行任何其他研究：


```r
ui <- fluidPage(
  selectInput("territory", "territory", choices = unique(sales$TERRITORY)),
  tableOutput("selected")
)
server <- function(input, output, session) {
  selected <- reactive(sales[sales$TERRITORY == input$territory, ])
  output$selected <- renderTable(head(selected(), 10))
}
```

我想，这是一个八行 app，可能会出什么问题吗？好吧，当我打开 app 时，我看到很多缺失值，无论我选择哪个区域。
最有可能成为问题根源的代码是选择要显示的数据的响应式：`sales[sales$TERRITORY == input$territory, ]`。
因此，我停止了该 app，并快速验证了子集化是否按照我想象的方式工作：


```r
sales[sales$TERRITORY == "EMEA", ]
#> # A tibble: 2,481 × 6
#>   TERRITORY ORDERDATE     ORDERNUMBER PRODUCTCODE QUANTITYORDERED PRICEEACH
#>   <chr>     <chr>               <dbl> <chr>                 <dbl>     <dbl>
#> 1 <NA>      <NA>                   NA <NA>                     NA      NA  
#> 2 EMEA      5/7/2003 0:00       10121 S10_1678                 34      81.4
#> 3 EMEA      7/1/2003 0:00       10134 S10_1678                 41      94.7
#> 4 <NA>      <NA>                   NA <NA>                     NA      NA  
#> # ℹ 2,477 more rows
```

哎呀！我忘记了 `TERRITORY` 包含一堆缺失值，这意味着 `sales$TERRITORY == "EMEA"` 将包含一堆缺失值：


```r
head(sales$TERRITORY == "EMEA", 25)
#>  [1]    NA  TRUE  TRUE    NA    NA    NA  TRUE  TRUE    NA  TRUE FALSE    NA
#> [13]    NA    NA  TRUE    NA  TRUE  TRUE    NA    NA  TRUE FALSE  TRUE    NA
#> [25]  TRUE
```

当我使用 `[` 对 `sales` data frame 进行子集化时，这些缺失值将成为缺失行；输入中的任何缺失值都将保留在输出中。
有很多方法可以解决这个问题，但我决定使用 `subset()`[^action-workflow-3]，因为会自动删除缺失值并减少我需要输入 `sales` 的次数。然后我仔细检查了这是否确实有效：

[^action-workflow-3]: 我正在使用 `subset()`，这样我的 app 就不需要任何其他包了。
    在更大的 app 中，我可能更喜欢 `dplyr::filter()`，因为我对它的行为更熟悉。


```r
subset(sales, TERRITORY == "EMEA")
#> # A tibble: 1,407 × 6
#>   TERRITORY ORDERDATE       ORDERNUMBER PRODUCTCODE QUANTITYORDERED PRICEEACH
#>   <chr>     <chr>                 <dbl> <chr>                 <dbl>     <dbl>
#> 1 EMEA      5/7/2003 0:00         10121 S10_1678                 34      81.4
#> 2 EMEA      7/1/2003 0:00         10134 S10_1678                 41      94.7
#> 3 EMEA      11/11/2003 0:00       10180 S10_1678                 29      86.1
#> 4 EMEA      11/18/2003 0:00       10188 S10_1678                 48     100  
#> # ℹ 1,403 more rows
```

这解决了大部分问题，但当我在区域下拉列表中选择 `NA` 时仍然遇到问题：仍然没有出现行。
于是，我再次检查控制台：


```r
subset(sales, TERRITORY == NA)
#> # A tibble: 0 × 6
#> # ℹ 6 variables: TERRITORY <chr>, ORDERDATE <chr>, ORDERNUMBER <dbl>,
#> #   PRODUCTCODE <chr>, QUANTITYORDERED <dbl>, PRICEEACH <dbl>
```

然后我想起这当然行不通，因为缺失值具有传染性：


```r
head(sales$TERRITORY == NA, 25)
#>  [1] NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA NA
```

您可以使用另一个技巧来解决此问题：从 `==` 切换到 `%in%`：


```r
head(sales$TERRITORY %in% NA, 25)
#>  [1]  TRUE FALSE FALSE  TRUE  TRUE  TRUE FALSE FALSE  TRUE FALSE FALSE  TRUE
#> [13]  TRUE  TRUE FALSE  TRUE FALSE FALSE  TRUE  TRUE FALSE FALSE FALSE  TRUE
#> [25] FALSE
subset(sales, TERRITORY %in% NA)
#> # A tibble: 1,074 × 6
#>   TERRITORY ORDERDATE       ORDERNUMBER PRODUCTCODE QUANTITYORDERED PRICEEACH
#>   <chr>     <chr>                 <dbl> <chr>                 <dbl>     <dbl>
#> 1 <NA>      2/24/2003 0:00        10107 S10_1678                 30      95.7
#> 2 <NA>      8/25/2003 0:00        10145 S10_1678                 45      83.3
#> 3 <NA>      10/10/2003 0:00       10159 S10_1678                 49     100  
#> 4 <NA>      10/28/2003 0:00       10168 S10_1678                 36      96.7
#> # ℹ 1,070 more rows
```

所以我更新了 app 并再次尝试。
还是没成功！
当我在下拉列表中选择 "NA" 时，我没有看到任何行。

此时，我想我已经在控制台上做了我能做的一切，我需要进行一个实验来找出为什么 Shiny 内部的代码没有按照我预期的方式工作。
我猜测问题最有可能的根源在于所选的响应式，因此我在那里添加了一个 `browser()` 语句。
（这使其成为两行响应式，因此我还需要将其包装在 `{}` 中。）


```r
server <- function(input, output, session) {
  selected <- reactive({
    browser()
    subset(sales, TERRITORY %in% input$territory)
  })
  output$selected <- renderTable(head(selected(), 10))
}
```

现在，当我的 app 运行时，我立即被转入交互式控制台。
我的第一步是验证我是否处于有问题的情况，因此我运行了 `subset(sales, TERRITORY %in% input$territory)`。
它返回一个空的数据框，所以我知道我在我需要的地方。
如果我没有看到这个问题，我会输入 `c` 让 app 继续运行，然后与更多的交互以使其达到失败状态。

然后我检查了 `subset()` 的输入是否符合我的预期。
我首先仔细检查 `sales` 数据集是否正常。
我真的没想到它会被损坏，因为 app 中没有任何内容触及它，但仔细检查您所做的每一个假设是最安全的。
`sales` 看起来不错，所以问题一定出在 `TERRITORY %in% input$territory` 中。
由于 `TERRITORY` 是 `sales` 的一部分，我首先检查 `input$territory`：


```r
input$territory
#> [1] "NA"
```

我盯着这个看了一会儿，因为它看起来也不错。
然后我想到了！
我以为是 `NA`，但实际上是 `"NA"`！
现在我可以在 Shiny app 之外重现问题：


```r
subset(sales, TERRITORY %in% "NA")
#> # A tibble: 0 × 6
#> # ℹ 6 variables: TERRITORY <chr>, ORDERDATE <chr>, ORDERNUMBER <dbl>,
#> #   PRODUCTCODE <chr>, QUANTITYORDERED <dbl>, PRICEEACH <dbl>
```

然后我想出了一个简单的修复方法并将其应用于我的 server，然后重新运行该 app：


```r
server <- function(input, output, session) {
  selected <- reactive({
    if (input$territory == "NA") {
      subset(sales, is.na(TERRITORY))
    } else {
      subset(sales, TERRITORY == input$territory)
    }
  })
  output$selected <- renderTable(head(selected(), 10))
}
```

万岁！
问题解决了！
但这让我感到非常惊讶 --- Shiny 默默地将 `NA` 转换为 `"NA"`，所以我还提交了一份错误报告：<https://github.com/rstudio/shiny/issues/2884>。

几周后，我再次查看这个示例，并开始思考不同的 territories。
我们有 Europe、Middle-East、Africa (EMEA)、Asia-Pacific (APAC)。
North America 在哪里？
然后我突然意识到：源数据可能使用缩写 NA，而 R 将其作为缺失值读入。
所以真正的修复应该发生在数据加载期间：


```r
sales <- readr::read_csv("sales-dashboard/sales_data_sample.csv", na = "")
unique(sales$TERRITORY)
#> [1] "NA"    "EMEA"  "APAC"  "Japan"
```

这让生活变得更加简单！

这是调试时的常见模式：在完全了解问题的根源之前，您通常需要剥开多层洋葱。

### Debugging reactivity

最难调试的问题是当你的响应式以意想不到的顺序触发时。
在本书中，我们推荐的工具相对较少，可以帮助您调试此问题。
在下一节中，您将学习如何创建一个最小的 reprex，这对于此类问题至关重要，在本书的后面部分，您将了解有关基础理论以及响应式日志等工具的更多信息，<https://github.com/rstudio/reactlog>。
但现在，我们将重点关注这里有用的经典技术："print" 调试。

打印调试的基本思想是，只要您需要了解代码的一部分何时被评估，并显示重要变量的值，就调用 `print()`。
我们称之为 "print" 调试（因为在大多数语言中你会使用打印函数），但在 R 中，使用 `message()` 更有意义：

-   `print()` 设计用于显示数据向量，因此它在字符串周围加上引号，并以 `[1]` 开始第一行。
-   `message()` 将其结果发送到“标准错误”，而不是“标准输出”。这些是描述输出流的技术术语，您通常不会注意到它们，因为它们在交互运行时以相同的方式显示。但如果您的 app 托管在其他地方，则发送到“标准错误”的输出将记录在日志中。

我还建议将 `message()` 与 `glue::glue()` 结合起来，这样可以轻松地在消息中交错文本和值。
如果您以前没有见过 [glue](http://glue.tidyverse.org/ "⌘+Click to follow link") ，其基本思想是，包装在 `{}` 内的任何内容都将被评估并插入到输出中：


```r
library(glue)
name <- "Hadley"
message(glue("Hello {name}"))
#> Hello Hadley
```

最后一个有用的工具是 `str()`，它可以打印任何对象的详细结构。
如果您需要仔细检查是否拥有所需的对象类型，这尤其有用。

这是一个展示一些基本想法的玩具 app。
请注意我如何在 `reactive()` 中使用 `message()`：我必须执行计算，发送消息，然后返回之前计算的值。


```r
ui <- fluidPage(
  sliderInput("x", "x", value = 1, min = 0, max = 10),
  sliderInput("y", "y", value = 2, min = 0, max = 10),
  sliderInput("z", "z", value = 3, min = 0, max = 10),
  textOutput("total")
)
server <- function(input, output, session) {
  observeEvent(input$x, {
    message(glue("Updating y from {input$y} to {input$x * 2}"))
    updateSliderInput(session, "y", value = input$x * 2)
  })
  
  total <- reactive({
    total <- input$x + input$y + input$z
    message(glue("New total is {total}"))
    total
  })
  
  output$total <- renderText({
    total()
  })
}
```

当我启动 app 时，控制台显示：

    Updating y from 2 to 2
    New total is 6

如果我将 `x` 滑块拖动到 `3`，我会看到

    Updating y from 2 to 6
    New total is 8
    New total is 12

如果您发现结果有点令人惊讶，请不要担心。
您将在 Chapter \@ref(action-feedback) 和 Chapter \@ref(the-reactive-graph) 中了解更多有关发生了什么的信息。

## Getting help

如果您在尝试这些技术后仍然遇到困难，那么可能是时候询问其他人了。
[Shiny community site](https://community.rstudio.com/c/shiny) 是获得帮助的好地方。
许多 Shiny 用户以及 Shiny 包本身的开发人员都会阅读该网站。
如果您想通过帮助他人来提高自己的 Shiny 技能，这里也是一个值得参观的好地方。

为了尽快获得最有用的帮助，您需要创建一个 reprex 或可重现的示例。
reprex 的目标是提供尽可能最小的 R 代码片段来说明问题并且可以轻松地在另一台计算机上运行。
创建 reprex 是一种常见的礼貌（并且符合您自己的最大利益）：如果您希望有人帮助您，您应该让他们尽可能轻松！
  
制作 reprex 是有礼貌的，因为它将问题的基本要素捕获为其他任何人都可以运行的形式，以便任何试图帮助您的人都可以快速准确地了解问题是什么，并且可以轻松地尝试可能的解决方案。

### Reprex basics

reprex 只是一些 R 代码，当您将其复制并粘贴到另一台计算机上的 R 会话中时，它就会起作用。
这是一个简单的 Shiny app reprex：


```r
library(shiny)
ui <- fluidPage(
  selectInput("n", "N", 1:10),
  plotOutput("plot")
)
server <- function(input, output, session) {
  output$plot <- renderPlot({
    n <- input$n * 2
    plot(head(cars, n))
  })
}
shinyApp(ui, server)
```

这段代码不会对运行它的计算机做出任何假设（除了安装了 Shiny！），因此任何人都可以运行这段代码并看到问题：app 抛出一个错误，指出 “non-numeric argument to binary operator”。

清楚地说明问题是获得帮助的第一步，因为任何人都可以通过复制和粘贴代码来重现问题，因此他们可以轻松地探索您的代码并测试可能的解决方案。
（在这种情况下，您需要 `as.numeric(input$n)` 因为 `selectInput()` 在 `input$n` 中创建一个字符串。）

### Making a reprex

制作 reprex 的第一步是创建一个独立的文件，其中包含运行代码所需的所有内容。
您应该通过启动新的 R 会话然后运行代码来检查它是否有效。
确保您没有忘记加载任何使您的 app 正常运行的软件包[^action-workflow-4]。

[^action-workflow-4]: 无论您通常如何加载包，我强烈建议使用多个 `library()` 调用。
这可以消除不熟悉您所使用的工具的人可能产生的混淆。

通常，让您的 app 在其他人的计算机上运行的最具挑战性的部分是消除仅存储在您的计算机上的数据的使用。
共有三种有用的模式：

-   通常，您使用的数据与问题没有直接关系，您可以使用 `mtcars` 或 `iris` 等内置数据集。

-   其他时候，您也许可以编写一些 R 代码来创建一个数据集来说明问题：
  
  
  ```r
  mydata <- data.frame(x = 1:5, y = c("a", "b", "c", "d", "e"))
  ```

-   如果这两种技术都失败了，您可以使用 `dput()` 将数据转换为代码。
例如，`dput(mydata)` 生成重新创建 `mydata` 的代码：
  
  
  ```r
  dput(mydata)
  #> structure(list(x = 1:5, y = c("a", "b", "c", "d", "e")), class = "data.frame", row.names = c(NA, 
  #> -5L))
  ```

获得该代码后，您可以将其放入您的 reprex 中以生成 `mydata`：
  
  
  ```r
  mydata <- structure(list(x = 1:5, y = structure(1:5, .Label = c("a", "b","c", "d", "e"), class = "factor")), class = "data.frame", row.names = c(NA, -5L))
  ```

通常，在原始数据上运行 `dput()` 会生成大量代码，因此请找到能够说明问题的数据子集。
您提供的数据集越小，其他人就越容易帮助您解决问题。

如果从磁盘读取数据似乎是问题的一个不可简化的部分，那么最后的策略是提供一个包含 `app.R` 和所需数据文件的完整项目。
提供此功能的最佳方法是作为托管在 GitHub 上的 RStudio 项目，但如果做不到这一点，您可以仔细制作一个可以在本地运行的 zip 文件。
确保您使用相对路径（即 `read.csv("my-data.csv")` 而不是 `read.csv("c:\\my-user-name\\files\\my-data.csv")`），以便您的代码在另一台计算机上运行时仍然有效。

您还应该考虑读者并花一些时间格式化您的代码，以便于阅读。
如果您采用 [tidyverse style guide](http://style.tidyverse.org/)，您可以使用 [styler](http://styler.r-lib.org) 包自动重新格式化您的代码；这可以快速将您的代码转移到更易于阅读的位置。

### Making a minimal reprex

创建一个可重现的示例是一个很好的第一步，因为它允许其他人精确地重现您的问题。
然而，有问题的代码通常会隐藏在运行良好的代码中，因此您可以通过删除正常的代码来使帮助者的工作变得更加轻松。

创建尽可能最小的 reprex 对于 Shiny apps 尤其重要，因为这些 apps 通常很复杂。
如果您能够提取出您遇到困难的 app 的确切部分，而不是强迫潜在的帮助者了解您的整个 app，您将获得更快、更高质量的帮助。
作为一个额外的好处，这个过程通常会引导您发现问题所在，因此您不必等待其他人的帮助！

将一堆代码简化为本质问题是一项技能，一开始你可能不会很擅长。
没关系！
即使代码复杂性的最小降低也会帮助帮助你的人，并且随着时间的推移，你的 reprex 缩减技能将会提高。

如果您不知道代码的哪一部分触发了问题，找到它的一个好方法是从应用程序中逐段删除代码段，直到问题消失。
如果删除特定代码段可以使问题停止，则该代码很可能与问题相关。
或者，有时更简单的做法是从一个全新的、空的 app 开始，然后逐步构建它，直到再次发现问题。

一旦您简化了 app 以演示问题，就值得进行最后一次检查：
  
  -   `UI` 中的每个输入和输出都与问题相关吗？
  
  -   您的 app 是否具有复杂的布局，您可以简化该布局以帮助专注于手头的问题？
  您是否删除了所有使您的 app 看起来不错但与问题无关的 UI 自定义？

-   现在可以删除 `server()` 中的任何响应式吗？

-   如果您尝试了多种方法来解决问题，您是否已清除所有无效尝试的痕迹？

-   您加载的每个包是否都需要说明问题？
    您可以通过用虚拟代码替换函数来消除包吗？

这可能需要大量工作，但回报是巨大的：通常您会在进行 reprex 时发现解决方案，如果没有，获得帮助也会容易得多。

### Case study

为了说明制作一流 reprex 的过程，我将使用 [Scott Novogoratz](https://community.rstudio.com/u/sanovogo) 在 [RStudio community](https://community.rstudio.com/t/37982) 上发布的一个示例。
最初的代码非常接近 reprex，但不太可重现，因为它忘记加载一对包。
作为起点，我：

-   添加了缺失的 `library(lubridate)` 和 `library(xts)`。
-   将 `ui` 和 `server` 拆分为单独的对象。
-   使用 `styler::style_selection()` 重新格式化代码。

这产生了以下 reprex：


```r
library(xts)
library(lubridate)
library(shiny)

ui <- fluidPage(
  uiOutput("interaction_slider"),
  verbatimTextOutput("breaks")
)
server <- function(input, output, session) {
  df <- data.frame(
    dateTime = c(
      "2019-08-20 16:00:00",
      "2019-08-20 16:00:01",
      "2019-08-20 16:00:02",
      "2019-08-20 16:00:03",
      "2019-08-20 16:00:04",
      "2019-08-20 16:00:05"
    ),
    var1 = c(9, 8, 11, 14, 16, 1),
    var2 = c(3, 4, 15, 12, 11, 19),
    var3 = c(2, 11, 9, 7, 14, 1)
  )

  timeSeries <- as.xts(df[, 2:4], 
    order.by = strptime(df[, 1], format = "%Y-%m-%d %H:%M:%S")
  )
  print(paste(min(time(timeSeries)), is.POSIXt(min(time(timeSeries))), sep = " "))
  print(paste(max(time(timeSeries)), is.POSIXt(max(time(timeSeries))), sep = " "))

  output$interaction_slider <- renderUI({
    sliderInput(
      "slider",
      "Select Range:",
      min = min(time(timeSeries)),
      max = max(time(timeSeries)),
      value = c(min, max)
    )
  })

  brks <- reactive({
    req(input$slider)
    seq(input$slider[1], input$slider[2], length.out = 10)
  })

  output$breaks <- brks
}
shinyApp(ui, server)
```

如果您运行此 reprex，您将在最初的帖子中看到相同的问题：错误指出 "Type mismatch for min, max, and value. Each must be Date, POSIXt, or number"。
这是一个可靠的 reprex：我可以轻松地在我的计算机上运行它，它立即说明了问题。
不过，它有点长，所以还不清楚是什么原因造成的。

为了使这个 reprex 更简单，我们可以仔细检查每一行代码，看看它是否重要。
在这样做的过程中，我发现：
  
-   删除以 `print()` 开头的两行不会影响错误。
    这两行使用了 `lubridate::is.POSIXt()`，这是 lubridate 的唯一用途，所以一旦我删除它们，我就不再需要加载 lubridate。

-   `df` 是一个转换为 xts 数据框（称为 `timeSeries`）的数据帧。但使用 `timeSeries` 的唯一方法是通过 `time(timeSeries)` 返回日期时间。
因此，我创建了一个新的变量 `datetime`，其中包含一些虚拟日期时间数据。
这仍然产生相同的错误，所以我删除了 `timeSeries` 和 `df`，并且由于这是唯一使用 xts 的地方，我还删除了 `library(xts)`。

这些更改共同产生了一个新的 `server()`，如下所示：
  

```r
datetime <- Sys.time() + (86400 * 0:10)

server <- function(input, output, session) {
  output$interaction_slider <- renderUI({
    sliderInput(
      "slider",
      "Select Range:",
      min   = min(datetime),
      max   = max(datetime),
      value = c(min, max)
    )
  })
  
  brks <- reactive({
    req(input$slider)
    seq(input$slider[1], input$slider[2], length.out = 10)
  })
  
  output$breaks <- brks
}
```

接下来，我注意到这个示例使用了相对复杂的 Shiny 技术，其中 UI 是在 server 函数中生成的。
但这里 `renderUI()` 不使用任何响应式输入，因此如果从 server 函数移出并进入 UI，它应该以相同的方式工作。

这产生了一个特别好的结果，因为现在错误发生得更早，甚至在我们启动 app 之前：


```r
ui <- fluidPage(
  sliderInput("slider",
    "Select Range:",
    min   = min(datetime),
    max   = max(datetime),
    value = c(min, max)
  ),
  verbatimTextOutput("breaks")
)
#> Error: Type mismatch for `min`, `max`, and `value`.
#> i All values must have same type: either numeric, Date, or POSIXt.
```

现在我们可以从错误消息中获取提示，并查看我们提供给 `min`、`max` 和 `value` 的每个输入，以了解问题出在哪里：
  

```r
min(datetime)
#> [1] "2024-08-10 19:39:40 CST"
max(datetime)
#> [1] "2024-08-20 19:39:40 CST"
c(min, max)
#> [[1]]
#> function (..., na.rm = FALSE)  .Primitive("min")
#> 
#> [[2]]
#> function (..., na.rm = FALSE)  .Primitive("max")
```

现在问题很明显：我们还没有分配 `min` 和 `max` 变量，所以我们不小心将 `min()` 和 `max()` 函数传递给了 `sliderInput()`。
解决该问题的一种方法是使用 `range()` 代替：
  

```r
ui <- fluidPage(
  sliderInput("slider",
              "Select Range:",
              min   = min(datetime),
              max   = max(datetime),
              value = range(datetime)
  ),
  verbatimTextOutput("breaks")
)
```

这是创建 reprex 的相当典型的结果：一旦将问题简化为其关键组件，解决方案就变得显而易见。
创建一个好的 reprex 是一种非常强大的调试技术。

为了简化这个 reprex，我必须对我不太熟悉的函数进行大量实验和阅读。[^action-workflow-5]
如果这是您的 reprex，那么通常会更容易做到这一点，因为您已经了解代码的意图。
不过，您通常需要进行大量实验才能找出问题到底出在哪里。
这可能会令人沮丧并且耗时，但它有很多好处：

[^action-workflow-5]: 例如，我不知道 `is.POSIXt()` 是 lubridate 包的一部分！

-   它使您能够创建问题的描述，任何了解 Shiny 的人都可以访问该描述，而不是任何了解 Shiny 和您正在工作的特定领域的人都可以访问。

-   您将为代码的工作原理建立一个更好的思维模型，这意味着您将来不太可能犯相同或类似的错误。

-   随着时间的推移，您创建 reprex 的速度会越来越快，这将成为调试时的常用技术之一。

-   即使你没有创造出完美的 reprex，你可以做的任何改善你的 reprex 的工作都比别人做的工作要少。
    如果您试图从软件包开发人员那里获得帮助，这一点尤其重要，因为他们通常对时间有很多要求。

当我尝试在 [RStudio community](https://community.rstudio.com/tag/shiny) 上帮助某人使用他们的 app 时，创建 reprex 始终是我做的第一件事。
这不是我用来欺骗那些我不想帮助的人的工作练习：这正是我的起点！

## Summary

本章为您提供了一些用于开发 apps、调试问题和获取帮助的有用工作流程。
这些工作流程可能看起来有点抽象并且很容易被忽视，因为它们并没有具体改进单个 app。
但我认为工作流程是我的“秘密”力量之一：我能够取得如此多成就的原因之一是我投入时间来分析和改进我的工作流程。
我强烈鼓励您也这样做！

关于布局和主题的下一章是有用技术的第一章。
无需按顺序阅读；请随意跳至当前 app 所需的章节。

