# Basic reactivity {#basic-reactivity}



## Introduction

在 Shiny 中，您可以使用响应式编程来表达你的 server 逻辑。
响应式编程是一种优雅而强大的编程范式，但一开始它可能会让人迷失方向，因为它与编写脚本是一种非常不同的范式。
响应式编程的关键思想是指定依赖关系图，以便当输入更改时，所有相关输出都会自动更新。
这使得 app 的流程变得相当简单，但需要一段时间才能理解它们是如何组合在一起的。

本章将简要介绍响应式编程，教您在 Shiny apps 中使用的最常见响应式结构的基础知识。
我们将从 server 函数的调查开始，更详细地讨论 `input` 和 `output` 参数的工作原理。
接下来，我们将回顾最简单的响应式形式（其中输入直接连接到输出），然后讨论响应式表达式如何帮助您消除重复的工作。
最后，我们将回顾一下 Shiny 新用户遇到的一些常见障碍。



## The server function

正如您所看到的，每个 Shiny app 的内部结构如下所示：


```r
library(shiny)

ui <- fluidPage(
  # front end interface
)

server <- function(input, output, session) {
  # back end logic
}

shinyApp(ui, server)
```

上一章介绍了前端的基础知识，即包含呈现给 app 的每个用户的 HTML 的 `ui` 对象。
`ui` 很简单，因为每个用户都会获得相同的 HTML。
`server` 更加复杂，因为每个用户都需要获得独立版本的 app；当用户 A 移动滑块时，用户 B 不应看到其输出发生变化。

为了实现这种独立性，Shiny 每次启动新会话[^basic-reactivity-1]时都会调用 `server()` 函数。
就像任何其他 R 函数一样，当调用 server 函数时，它会创建一个独立于该函数的所有其他调用的新本地环境。
这允许每个会话具有唯一的状态，并隔离函数内创建的变量。
这就是为什么您在 Shiny 中进行的几乎所有响应式编程都将在 server 函数中进行[^basic-reactivity-2]。

[^basic-reactivity-1]: 每次连接到 Shiny app 都会启动一个新会话，无论是来自不同人的连接，还是来自同一个人的多个选项卡。

[^basic-reactivity-2]: 主要例外是有些工作可以跨多个用户共享。
    例如，所有用户可能都在查看同一个大型 csv 文件，因此您不妨加载一次并在用户之间共享。
    我们将在 Section \@ref(schedule-data-munging) 中回顾这个想法。

Server 函数采用三个参数：`input`、`output` 和 `session`。
因为您从不自己调用 server 函数，所以您永远不会自己创建这些对象。
相反，它们是在会话开始时由 Shiny 创建的，并连接回特定会话。
目前，我们将重点关注 `input` 和 `output` 参数，并将 `session` 留给后面的章节。

### Input {#input}

`input` 参数是一个类似列表的对象，其中包含从浏览器发送的所有输入数据，根据输入 ID 命名。
例如，如果您的 UI 包含一个输入 ID 为 `count` 的数字输入控件，如下所示：


```r
ui <- fluidPage(
  numericInput("count", label = "Number of values", value = 100)
)
```

然后您可以使用 `input$count` 访问该输入的值。
它最初包含值 `100`，并且当用户更改浏览器中的值时，它将自动更新。

与典型列表不同，`input` 对象是只读的。
如果您尝试修改 server 函数内的 input，您将收到报错：


```r
server <- function(input, output, session) {
  input$count <- 10  
}

shinyApp(ui, server)
#> Error: Can't modify read-only reactive value 'count'
```

发生此错误的原因是 `input` 反映了浏览器中发生的情况，而浏览器是 Shiny 的“单一事实来源”。
如果您可以修改 R 中的值，则可能会导致不一致，即输入滑块在浏览器中表示一件事，而 `input$count` 在 R 中表示不同的内容。
这将使编程变得具有挑战性！
稍后，在 Chapter \@ref(action-feedback) 中，您将学习如何使用 `updateNumericInput()` 等函数来修改浏览器中的值，然后 `input$count` 也会相应更新。

关于 `input` 的另一件重要的事情是：它对允许谁阅读它是有选择性的。
要读取 `input`，您必须处于由 `renderText()` 或 `reactive()` 等函数创建的**响应式**上下文中。
我们很快就会回到这个想法，但这是一个重要的约束，它允许输出在输入更改时自动更新。
此代码说明了如果您犯此错误，您将看到的错误：


```r
server <- function(input, output, session) {
  message("The value of input$count is ", input$count)
}

shinyApp(ui, server)
#> Error: Can't access reactive value 'count' outside of reactive consumer.
#> ℹ Do you need to wrap inside reactive() or observer()?
```

### Output {#output}

`output` 与 `input` 非常相似：它也是一个根据输出 ID 命名的类似列表的对象。
主要区别在于您使用它来发送输出而不是接收输入。
您始终将 `output` 对象与 `render` 函数结合使用，如以下简单示例所示：


```r
ui <- fluidPage(
  textOutput("greeting")
)

server <- function(input, output, session) {
  output$greeting <- renderText("Hello human!")
}
```

（请注意，该 ID 在 UI 中被引用，但在 server 中未被引用。）

render 函数做了两件事：

-   它设置了一个特殊的响应式上下文，可以自动跟踪输出使用的输入。

-   它将 R 代码的输出转换为适合在网页上显示的 HTML。

与 `input` 一样，`output` 对您的使用方式也很挑剔。
如果出现以下情况，您将收到错误消息：

-   你忘记了 `render` 函数。

    
    ```r
    server <- function(input, output, session) {
      output$greeting <- "Hello human"
    }
    shinyApp(ui, server)
    #> Error: Unexpected character object for output$greeting
    #> ℹ Did you forget to use a render function?
    ```

-   您尝试读取 output。

    
    ```r
    server <- function(input, output, session) {
      message("The greeting is ", output$greeting)
    }
    shinyApp(ui, server)
    #> Error: Reading from shinyoutput object is not allowed.
    ```

## Reactive programming

如果 app 只有输入或只有输出，那么它会非常无聊。
当您拥有同时具备这两种功能的 app 时，Shiny 的真正魔力就会发挥出来。
让我们看一个简单的例子：


```r
ui <- fluidPage(
  textInput("name", "What's your name?"),
  textOutput("greeting")
)

server <- function(input, output, session) {
  output$greeting <- renderText({
    paste0("Hello ", input$name, "!")
  })
}
```

很难在一本书中展示它是如何工作的，但我在 Figure \@ref(fig:connection) 中尽力了。
如果您运行该 app，并在 name 框中输入内容，您将看到问候语会在您输入时自动更新[^basic-reactivity-3]。

[^basic-reactivity-3]: 如果您正在运行实时 app，请注意，您必须相当缓慢地输入，以便输出一次更新一个字母。
    这是因为 Shiny 使用了一种称为 **debouncing** 的技术，这意味着它会等待几毫秒后再发送更新。
    这大大减少了 Shiny 需要做的工作量，而不会明显减少 app 的响应时间。

<div class="figure">
<img src="demos/basic-reactivity/connection-1.png" alt="响应式意味着输出会随着输入的变化而自动更新，就像在这个 app 中我输入“J”、“o”、“e”一样。 See live at &lt;https://hadley.shinyapps.io/ms-connection&gt;." width="25%" /><img src="demos/basic-reactivity/connection-2.png" alt="响应式意味着输出会随着输入的变化而自动更新，就像在这个 app 中我输入“J”、“o”、“e”一样。 See live at &lt;https://hadley.shinyapps.io/ms-connection&gt;." width="25%" /><img src="demos/basic-reactivity/connection-3.png" alt="响应式意味着输出会随着输入的变化而自动更新，就像在这个 app 中我输入“J”、“o”、“e”一样。 See live at &lt;https://hadley.shinyapps.io/ms-connection&gt;." width="25%" />
<p class="caption">(\#fig:connection)响应式意味着输出会随着输入的变化而自动更新，就像在这个 app 中我输入“J”、“o”、“e”一样。 See live at <https://hadley.shinyapps.io/ms-connection>.</p>
</div>

这是 Shiny 的重要思想：您不需要告诉输出何时更新，因为 Shiny 会自动为您计算出来。
它是如何工作的？
函数体内究竟发生了什么？
让我们更精确地考虑一下 server 函数内部的代码：


```r
output$greeting <- renderText({
  paste0("Hello ", input$name, "!")
})
```

很容易将其理解为“将‘hello’和用户名粘贴在一起，然后将其发送到`output$greeting`”。
但这种思维模式在一个微妙但重要的方面是错误的。
想一想：使用这种模型，您只需发出一次指令。
但每次我们更新 `input$name` 时，Shiny 都会执行该操作，所以肯定还有更多的事情发生。

该 app 之所以有效，是因为代码不会告诉 Shiny 创建字符串并将其发送到浏览器，而是通知 Shiny 在需要时如何创建字符串。
代码何时（甚至是否！）运行取决于 Shiny。
它可能会在 app 启动后立即运行，也可能会晚一些；它可能会运行很多次，也可能永远不会运行！
这并不是说 Shiny 反复无常，只是说决定何时执行代码是 Shiny 的责任，而不是你的。
将您的 app 视为向 Shiny 提供食谱，而不是向其发出命令。

### Imperative vs declarative programming

命令和配方之间的区别是两种重要编程风格之间的主要区别之一：

-   在**命令式（imperative）**编程中，您发出特定的命令，它会立即执行。
    这是您在分析脚本中习惯的编程风格：命令 R 加载数据、转换数据、可视化数据并将结果保存到磁盘。

-   在**声明式（declarative）**编程中，您表达更高级别的目标或描述重要的约束，并依靠其他人来决定如何和/或何时将其转化为行动。
    这是您在 Shiny 中使用的编程风格。

使用命令式代码，您可以说“给我做一个三明治”[^basic-reactivity-4]。
使用声明性代码，您可以说“每当我查看冰箱内部时，确保冰箱中有一个三明治”。
命令式代码是断言的（assertive）；声明式代码是被动攻击性的（passive-aggressive）。

[^basic-reactivity-4]: <https://xkcd.com/149/>

大多数时候，声明式编程非常自由：您描述您的总体目标，软件就会计算出如何实现这些目标，而无需进一步干预。
缺点是有时您确切地知道自己想要什么，但无法弄清楚如何以声明性系统可以理解的方式构建它[^basic-reactivity-5]。
本书的目的是帮助您加深对基础理论的理解，从而尽可能减少这种情况的发生。

[^basic-reactivity-5]: 如果您曾经努力让 ggplot2 图例看起来完全符合您的要求，那么您就遇到过这个问题！

### Laziness

Shiny 中声明式编程的优点之一是它允许 apps 变得非常懒惰。
Shiny app 只会执行更新您当前可以看到的输出控件所需的最少工作量[^basic-reactivity-6]。
然而，这种懒惰带来了一个您应该意识到的重要缺点。
你能发现下面的 server 函数有什么问题吗？

[^basic-reactivity-6]: 是的，如果您在浏览器中看不到输出，Shiny 就不会更新输出！
    Shiny 非常懒惰，除非您能实际看到结果，否则它不会执行工作。


```r
server <- function(input, output, session) {
  output$greting <- renderText({
    paste0("Hello ", input$name, "!")
  })
}
```

如果你仔细观察，你可能会注意到我写的是 `greting` 而不是 `greeting`。
这不会在 Shiny 中产生错误，但它不会做你想要的事情。
`greting` 输出不存在，因此 `renderText()` 中的代码永远不会运行。

如果您正在开发一个 Shiny app，并且您无法弄清楚为什么您的代码永远不会运行，请仔细检查您的 UI 和 server 函数是否使用相同的标识符。

### The reactive graph

Shiny 的懒惰还有另一个重要的特性。
在大多数 R 代码中，您可以通过从上到下阅读代码来了解执行顺序。
这在 Shiny 中不起作用，因为代码仅在需要时运行。
要了解执行顺序，您需要查看**响应式图（reactive graph）**，它描述了输入和输出如何连接。
上面 app 的响应式图非常简单，如 Figure \@ref(fig:graph-simple) 所示。

<div class="figure">
<img src="diagrams/basic-reactivity/graph-1b.png" alt="响应式图显示了输入和输出的连接方式" width="181" />
<p class="caption">(\#fig:graph-simple)响应式图显示了输入和输出的连接方式</p>
</div>

响应式图对于每个输入和输出都包含一个符号，每当输出访问输入时，我们就将输入连接到输出。
该图告诉您，只要 `name` 更改，就需要重新计算 `greeting`。
我们经常将这种关系描述为 `greeting` 对 `name` 有**响应式依赖（reactive dependency）**。

请注意我们用于输入和输出的图形约定：`name` 输入自然适合 `greeting` 输出。
我们可以将它们紧密地画在一起，如 Figure \@ref(fig:graph-collapsed) 所示，以强调它们组合在一起的方式；我们通常不会这样做，因为它只适用于最简单的 apps。

<div class="figure">
<img src="diagrams/basic-reactivity/graph-1a.png" alt="响应式图组件使用的形状唤起了它们连接的方式。" width="143" />
<p class="caption">(\#fig:graph-collapsed)响应式图组件使用的形状唤起了它们连接的方式。</p>
</div>

响应式图是了解 app 工作原理的强大工具。
随着您的 app 变得越来越复杂，制作响应式图的快速高级草图通常很有用，以提醒您所有部分如何组合在一起。
在本书中，我们将向您展示响应式图，以帮助您理解示例的工作原理，稍后在 Chapter 14 中，您将学习如何使用 reactlog 来为您绘制图表。

### Reactive expressions

您将在响应式图中看到一个更重要的组件：响应式表达式。
我们很快就会详细讨论响应式表达式；现在将它们视为一种工具，通过在响应式图中引入额外的节点来减少响应式代码中的重复。

我们在非常简单的 app 中不需要响应式表达式，但无论如何我都会添加一个，以便您可以看到它如何影响响应式图，Figure \@ref(fig:graph-expression)。


```r
server <- function(input, output, session) {
  string <- reactive(paste0("Hello ", input$name, "!"))
  output$greeting <- renderText(string())
}
```

<div class="figure">
<img src="diagrams/basic-reactivity/graph-2b.png" alt="响应式表达式两边都有角度，因为它将输入连接到输出。" width="291" />
<p class="caption">(\#fig:graph-expression)响应式表达式两边都有角度，因为它将输入连接到输出。</p>
</div>

响应式表达式接受输入并产生输出，因此它们具有结合输入和输出特征的形状。
希望这些形状能帮助您记住组件如何组合在一起。

### Execution order

重要的是要理解代码运行的顺序完全由响应式图决定。
这与大多数 R 代码不同，大多数 R 代码的执行顺序由行的顺序决定。
例如，我们可以在简单的 server 函数中翻转两行的顺序：


```r
server <- function(input, output, session) {
  output$greeting <- renderText(string())
  string <- reactive(paste0("Hello ", input$name, "!"))
}
```

您可能认为这会产生错误，因为 `output$greeting` 引用尚未创建的响应式表达式、字符串。
但请记住，Shiny 是惰性的，因此代码仅在创建字符串后会话启动时运行。

相反，此代码会生成与上面相同的响应式图，因此代码的运行顺序完全相同。
像这样组织代码会让人类感到困惑，最好避免。
相反，请确保响应式表达式和输出仅引用上面定义的内容，而不是下面定义的内容[^basic-reactivity-7]。
这将使您的代码更容易理解。

[^basic-reactivity-7]: 这种排序的技术术语是 "topological sort"。

这个概念非常重要，并且与大多数其他 R 代码不同，所以我再说一遍：响应式代码的运行顺序仅由响应式图决定，而不是由其在 server 函数中的布局决定。

### Exercises

1.  给定这个 UI:

    
    ```r
    ui <- fluidPage(
      textInput("name", "What's your name?"),
      textOutput("greeting")
    )
    ```

    修复以下三个 server 函数中发现的简单错误。
    首先尝试通过阅读代码来发现问题；然后运行代码以确保您已修复它。

    
    ```r
    server1 <- function(input, output, server) {
      input$greeting <- renderText(paste0("Hello ", name))
    }
    
    server2 <- function(input, output, server) {
      greeting <- paste0("Hello ", input$name)
      output$greeting <- renderText(greeting)
    }
    
    server3 <- function(input, output, server) {
      output$greting <- paste0("Hello", input$name)
    }
    ```

2.  绘制以下 server 函数的响应式图：

    
    ```r
    server1 <- function(input, output, session) {
      c <- reactive(input$a + input$b)
      e <- reactive(c() + input$d)
      output$f <- renderText(e())
    }
    server2 <- function(input, output, session) {
      x <- reactive(input$x1 + input$x2 + input$x3)
      y <- reactive(input$y1 + input$y2)
      output$z <- renderText(x() / y())
    }
    server3 <- function(input, output, session) {
      d <- reactive(c() ^ input$d)
      a <- reactive(input$a * 10)
      c <- reactive(b() / input$c) 
      b <- reactive(a() + input$b)
    }
    ```

3.  为什么这段代码会失败？

    
    ```r
    var <- reactive(df[[input$var]])
    range <- reactive(range(var(), na.rm = TRUE))
    ```

    为什么 `range()` 和 `var()` 对于响应式来说是不好的名字？

## Reactive expressions

我们已经快速浏览了几次响应式表达式，因此您希望能够了解它们可能会做什么。
现在我们将深入探讨更多细节，并展示为什么它们在构建真实 apps 时如此重要。

响应式表达式很重要，因为它们为 *Shiny* 提供了更多信息，以便它在输入更改时可以减少重新计算，从而使 apps 更加高效，并且它们通过简化响应式图使人们更容易理解 app。
响应式表达式具有输入和输出的风格：

-   与 inputs 一样，您可以在 output 中使用响应式表达式的结果。

-   与 outputs 一样，响应式表达式依赖于 inputs 并自动知道何时需要更新。

这种二元性意味着我们需要一些新的词汇：我将使用**生产者（producers）**来指代响应式输入和表达式，使用**消费者（consumers）**来指代响应式表达式和输出。
Figure \@ref(fig:prod-consumer) 用维恩图显示了这种关系。

<div class="figure">
<img src="diagrams/basic-reactivity/producers-consumers.png" alt="输入和表达式是响应式生产者；表达式和输出是响应式消费者。" width="332" />
<p class="caption">(\#fig:prod-consumer)输入和表达式是响应式生产者；表达式和输出是响应式消费者。</p>
</div>

我们将需要一个更复杂的 app 来了解使用响应式表达式的好处。
首先，我们将定义一些常规 R 函数来为我们的 app 提供支持，从而做好准备。

### The motivation

想象一下，我想通过绘图和假设检验来比较两个模拟数据集。
我做了一些实验并提出了以下函数：`freqpoly()` 使用频率多边形可视化两个分布[^basic-reactivity-8]，`t_test()` 使用 t-test 来比较平均值并用字符串总结结果：

[^basic-reactivity-8]: 如果您以前没有听说过频率多边形，它只是用线而不是条形绘制的直方图，这使得在同一张图上比较多个数据集变得更加容易。


```r
library(ggplot2)

freqpoly <- function(x1, x2, binwidth = 0.1, xlim = c(-3, 3)) {
  df <- data.frame(
    x = c(x1, x2),
    g = c(rep("x1", length(x1)), rep("x2", length(x2)))
  )

  ggplot(df, aes(x, colour = g)) +
    geom_freqpoly(binwidth = binwidth, size = 1) +
    coord_cartesian(xlim = xlim)
}

t_test <- function(x1, x2) {
  test <- t.test(x1, x2)
  
  # use sprintf() to format t.test() results compactly
  sprintf(
    "p value: %0.3f\n[%0.2f, %0.2f]",
    test$p.value, test$conf.int[1], test$conf.int[2]
  )
}
```

如果我有一些模拟数据，我可以使用这些函数来比较两个变量：


```r
x1 <- rnorm(100, mean = 0, sd = 0.5)
x2 <- rnorm(200, mean = 0.15, sd = 0.9)

freqpoly(x1, x2)
cat(t_test(x1, x2))
#> p value: 0.272
#> [-0.24, 0.07]
```

<img src="basic-reactivity_files/figure-html/unnamed-chunk-19-1.png" width="70%" />

在真正的分析中，您可能会在最终使用这些函数之前进行大量探索。
我在这里跳过了该探索，以便我们可以尽快使用该 app。
但是，将命令式代码提取到常规函数中对于所有 Shiny app 来说都是一项重要技术：从 app 中提取的代码越多，它就越容易理解。
这是很好的软件工程，因为它有助于隔离问题：app 外部的函数专注于计算，以便 app 内部的代码可以专注于响应用户操作。
我们将在 Chapter \@ref(scaling-functions) 中再次讨论这个想法。

### The app

我想使用这两个工具来快速探索一系列模拟。
Shiny app 是实现此目的的好方法，因为它可以让您避免繁琐的修改和重新运行 R 代码。
下面我将这些片段封装到一个 Shiny app 中，我可以在其中交互式地调整输入。

让我们从 UI 开始。
我们将回到 Section \@ref(multi-row) 中 `fluidRow()` 和 `column()` 的具体用途；但你可以从他们的名字猜出他们的目的😄。
第一行有三列用于 input controls（distribution 1, distribution 2, and plot controls）。
第二行有一个宽的列用于绘图，一个窄的列用于假设检验。


```r
ui <- fluidPage(
  fluidRow(
    column(4, 
      "Distribution 1",
      numericInput("n1", label = "n", value = 1000, min = 1),
      numericInput("mean1", label = "µ", value = 0, step = 0.1),
      numericInput("sd1", label = "σ", value = 0.5, min = 0.1, step = 0.1)
    ),
    column(4, 
      "Distribution 2",
      numericInput("n2", label = "n", value = 1000, min = 1),
      numericInput("mean2", label = "µ", value = 0, step = 0.1),
      numericInput("sd2", label = "σ", value = 0.5, min = 0.1, step = 0.1)
    ),
    column(4,
      "Frequency polygon",
      numericInput("binwidth", label = "Bin width", value = 0.1, step = 0.1),
      sliderInput("range", label = "range", value = c(-3, 3), min = -5, max = 5)
    )
  ),
  fluidRow(
    column(9, plotOutput("hist")),
    column(3, verbatimTextOutput("ttest"))
  )
)
```

指定分布绘制后，server 函数结合了对 `freqpoly()` 和 `t_test()` 函数的调用：


```r
server <- function(input, output, session) {
  output$hist <- renderPlot({
    x1 <- rnorm(input$n1, input$mean1, input$sd1)
    x2 <- rnorm(input$n2, input$mean2, input$sd2)
    
    freqpoly(x1, x2, binwidth = input$binwidth, xlim = input$range)
  }, res = 96)

  output$ttest <- renderText({
    x1 <- rnorm(input$n1, input$mean1, input$sd1)
    x2 <- rnorm(input$n2, input$mean2, input$sd2)
    
    t_test(x1, x2)
  })
}
```

<div class="figure">
<img src="demos/basic-reactivity/case-study-1.png" alt="一个 Shiny app，可让您使用 t-test 和频数多边形来比较两个模拟分布。 See live at &lt;https://hadley.shinyapps.io/ms-case-study-1&gt;." width="100%" />
<p class="caption">(\#fig:ttest)一个 Shiny app，可让您使用 t-test 和频数多边形来比较两个模拟分布。 See live at <https://hadley.shinyapps.io/ms-case-study-1>.</p>
</div>

`server` 和 `ui` 的定义如 Figure \@ref(fig:ttest) 所示。
您可以在 <https://hadley.shinyapps.io/ms-case-study-1> 找到实时版本；我建议您打开该 app 并快速玩一下，以确保您在继续阅读之前了解其基本操作。

### The reactive graph

让我们首先绘制这个 app 的响应式图。
Shiny 足够聪明，只有当它引用的输入发生变化时才会更新输出；它不够智能，无法仅选择性地运行输出中的代码片段。
换句话说，输出是原子的：它们要么被执行，要么不作为一个整体执行。

例如，从 server 获取此片段：


```r
x1 <- rnorm(input$n1, input$mean1, input$sd1)
x2 <- rnorm(input$n2, input$mean2, input$sd2)
t_test(x1, x2)
```

作为阅读这段代码的人，您可以看出，当 `n1`、`mean1` 或 `sd1` 更改时，我们只需要更新 `x1`，当 `n2`、`mean2` 或 `sd2` 更改时，我们只需要更新 `x2`。
然而，Shiny 只将输出视为一个整体，因此每当 `n1`、`mean1`、`sd1`、`n2`、`mean2` 或 `sd2` 之一发生变化时，它都会更新 `x1` 和 `x2`。
这导致响应式图如 Figure \@ref(fig:ttest-react1) 所示：

<div class="figure">
<img src="diagrams/basic-reactivity/case-study-1.png" alt="响应式图显示每个输出都取决于每个输入" width="207" />
<p class="caption">(\#fig:ttest-react1)响应式图显示每个输出都取决于每个输入</p>
</div>

您会注意到该图非常密集：几乎每个输入都直接连接到每个输出。
这会产生两个问题：

-   该 app 很难理解，因为有很多连接。
    该 app 中没有任何部分可以单独提取和分析。

-   该 app 效率低下，因为它做了超出必要的工作。
    例如，如果更改绘图的 breaks，则会重新计算数据；如果更改 `n1` 的值，`x2` 也会更新（在两个地方！）。

该 app 还有另一个主要缺陷：频率多边形和 t-test 使用单独的随机抽取。
这是相当误导的，因为您期望他们处理相同的基础数据。

幸运的是，我们可以通过使用响应式表达式来消除重复计算来解决所有这些问题。

### Simplifying the graph

在下面的 server 函数中，我们重构现有代码，将重复的代码提取为两个新的响应式表达式 `x1` 和 `x2`，它们模拟来自两个分布的数据。
为了创建响应式表达式，我们调用 `reactive()` 并将结果分配给一个变量。
为了稍后使用该表达式，我们将变量称为函数。


```r
server <- function(input, output, session) {
  x1 <- reactive(rnorm(input$n1, input$mean1, input$sd1))
  x2 <- reactive(rnorm(input$n2, input$mean2, input$sd2))

  output$hist <- renderPlot({
    freqpoly(x1(), x2(), binwidth = input$binwidth, xlim = input$range)
  }, res = 96)

  output$ttest <- renderText({
    t_test(x1(), x2())
  })
}
```

这种转换产生了 Figure \@ref(fig:ttest-react2) 所示的更加简单的图。
这个更简单的图使您更容易理解 app，因为您可以单独理解连接的组件；分布参数的值仅影响通过 `x1` 和 `x2` 的输出。
这种重写还使 app 更加高效，因为它执行的计算要少得多。
现在，当您更改 `binwidth` 或 `range` 时，只有绘图发生变化，而不是基础数据。

<div class="figure">
<img src="diagrams/basic-reactivity/case-study-2.png" alt="使用响应式表达式可以大大简化图，使其更容易理解" width="252" />
<p class="caption">(\#fig:ttest-react2)使用响应式表达式可以大大简化图，使其更容易理解</p>
</div>

为了强调这种模块化，Figure \@ref(fig:ttest-module) 在独立组件周围画了方框。
当我们讨论模块时，我们将在 Chapter \@ref(scaling-modules) 中回到这个想法。
模块允许您提取重复的代码以供重复使用，同时保证它与 app 中的其他所有内容隔离。
对于更复杂的 apps 来说，模块是一种非常有用且强大的技术。

<div class="figure">
<img src="diagrams/basic-reactivity/case-study-3.png" alt="模块强制 app 各部分之间的隔离" width="255" />
<p class="caption">(\#fig:ttest-module)模块强制 app 各部分之间的隔离</p>
</div>

您可能熟悉编程的“三规则”：每当您将某些内容复制并粘贴三次时，您应该弄清楚如何减少重复（通常通过编写函数）。
这很重要，因为它减少了代码中的重复量，这使得代码更容易理解，并且随着需求的变化更容易更新。

然而，在 Shiny 中，我认为您应该考虑一则规则：每当您复制并粘贴某些内容一次时，您应该考虑将重复的代码提取到响应式表达式中。
该规则对于 Shiny 来说更为严格，因为响应式表达式不仅使人们更容易理解代码，还提高了 Shiny 有效重新运行代码的能力。

### Why do we need reactive expressions? {#reactive-roadblocks}

当您第一次开始使用响应式代码时，您可能想知道为什么我们需要响应式表达式。
为什么不能使用现有的工具来减少代码重复：创建新变量和编写函数？
不幸的是，这些技术都不能在响应式环境中工作。

如果您尝试使用变量来减少重复，您可能会编写如下内容：


```r
server <- function(input, output, session) {
  x1 <- rnorm(input$n1, input$mean1, input$sd1)
  x2 <- rnorm(input$n2, input$mean2, input$sd2)

  output$hist <- renderPlot({
    freqpoly(x1, x2, binwidth = input$binwidth, xlim = input$range)
  }, res = 96)

  output$ttest <- renderText({
    t_test(x1, x2)
  })
}
```

如果运行此代码，您将收到报错，因为您正在尝试访问响应式上下文之外的输入值。
即使您没有收到该报错，您仍然会遇到问题：`x1` 和 `x2` 只会在会话开始时计算一次，而不是每次更新其中一个输入时计算。

如果您尝试使用一个函数，该 app 将运行：


```r
server <- function(input, output, session) { 
  x1 <- function() rnorm(input$n1, input$mean1, input$sd1)
  x2 <- function() rnorm(input$n2, input$mean2, input$sd2)

  output$hist <- renderPlot({
    freqpoly(x1(), x2(), binwidth = input$binwidth, xlim = input$range)
  }, res = 96)

  output$ttest <- renderText({
    t_test(x1(), x2())
  })
}
```

但它与原始代码存在相同的问题：任何输入都会导致所有输出重新计算，并且 t-test 和频数多边形将在单独的样本上运行。
响应式表达式会自动缓存其结果，并且仅在其输入更改时更新[^basic-reactivity-9]。

[^basic-reactivity-9]: 如果您熟悉记忆法，这是一个类似的想法。

虽然变量只计算一次值（粥太冷），函数每次调用时都计算值（粥太热），但响应式表达式仅在值可能发生变化时才计算值（粥正好是正确的！）。

## Controlling timing of evaluation

现在您已经熟悉了响应式的基本思想，我们将讨论两种更高级的技术，这些技术允许您增加或减少响应式表达式的执行频率。
在这里我将展示如何使用基本技术；在 Chapter \@ref(reactivity-objects) 中，我们将回到它们的底层实现。

为了探索基本想法，我将简化我的模拟 app。
我将使用只有一个参数的分布，并强制两个样本共享相同的 `n`。
我还将删除绘图控件。
这会产生一个更小的 UI 对象和 server 函数：


```r
ui <- fluidPage(
  fluidRow(
    column(3, 
      numericInput("lambda1", label = "lambda1", value = 3),
      numericInput("lambda2", label = "lambda2", value = 5),
      numericInput("n", label = "n", value = 1e4, min = 0)
    ),
    column(9, plotOutput("hist"))
  )
)
server <- function(input, output, session) {
  x1 <- reactive(rpois(input$n, input$lambda1))
  x2 <- reactive(rpois(input$n, input$lambda2))
  output$hist <- renderPlot({
    freqpoly(x1(), x2(), binwidth = 1, xlim = c(0, 40))
  }, res = 96)
}
```

这会生成如 Figure \@ref(fig:sim) 所示的 app 和如 Figure \@ref(fig:sim-react) 所示的响应式图。

<div class="figure">
<img src="demos/basic-reactivity/simulation-2.png" alt="一个更简单的 app，显示从两个泊松分布中提取的随机数的频率多边形。 See live at &lt;https://hadley.shinyapps.io/ms-simulation-2&gt;." width="100%" />
<p class="caption">(\#fig:sim)一个更简单的 app，显示从两个泊松分布中提取的随机数的频率多边形。 See live at <https://hadley.shinyapps.io/ms-simulation-2>.</p>
</div>

<div class="figure">
<img src="diagrams/basic-reactivity/timing.png" alt="The reactive graph" width="314" />
<p class="caption">(\#fig:sim-react)The reactive graph</p>
</div>

### Timed invalidation

想象一下，您想通过不断重新模拟数据来强化这是针对模拟数据的事实，以便您看到动画而不是静态图[^basic-reactivity-10]。
我们可以使用一个新函数来增加更新频率：`reactiveTimer()`。

[^basic-reactivity-10]: 纽约时报在讨论如何解读就业报告的文章中特别有效地运用了这一技巧： <https://www.nytimes.com/2014/05/02/upshot/how-not-to-be-misled-by-the-jobs-report.html>

`reactiveTimer()` 是一个响应式表达式，它依赖于隐藏输入：当前时间。
当您希望响应式表达式比其他方式更频繁地使自身无效时，可以使用 `reactiveTimer()`。
例如，以下代码使用 500 ms 的间隔，以便绘图每秒更新两次。
这个速度足够快，足以提醒您正在查看模拟，而不会因快速的变化而感到头晕。
此更改产生如 Figure \@ref(fig:sim-timer) 所示的响应式图


```r
server <- function(input, output, session) {
  timer <- reactiveTimer(500)
  
  x1 <- reactive({
    timer()
    rpois(input$n, input$lambda1)
  })
  x2 <- reactive({
    timer()
    rpois(input$n, input$lambda2)
  })
  
  output$hist <- renderPlot({
    freqpoly(x1(), x2(), binwidth = 1, xlim = c(0, 40))
  }, res = 96)
}
```

<div class="figure">
<img src="diagrams/basic-reactivity/timing-timer.png" alt="`reactiveTimer(500)` 引入了一种新的响应式输入，每半秒自动失效一次" width="416" />
<p class="caption">(\#fig:sim-timer)`reactiveTimer(500)` 引入了一种新的响应式输入，每半秒自动失效一次</p>
</div>

请注意我们如何在计算 `x1()` 和 `x2()` 的响应式表达式中使用 `timer()`：我们调用它，但不使用该值。
这让 `x1` 和 `x2` 对 `timer` 产生响应式依赖，而不必担心它返回的具体值。

### On click

在上面的场景中，想一想如果模拟代码运行时间为 1 秒会发生什么。
我们每 0.5s 执行一次模拟，因此 Shiny 要做的事情会越来越多，并且永远无法赶上。
如果有人快速单击 app 中的按钮并且您正在进行的计算相对昂贵，也会发生同样的问题。
有可能为 Shiny 创建大量积压工作，并且在处理积压工作时，它无法响应任何新事件。
这会导致糟糕的用户体验。

如果您的 app 中出现这种情况，您可能希望要求用户通过单击按钮来选择执行昂贵的计算。
这是 `actionButton()` 的一个很好的用例：


```r
ui <- fluidPage(
  fluidRow(
    column(3, 
      numericInput("lambda1", label = "lambda1", value = 3),
      numericInput("lambda2", label = "lambda2", value = 5),
      numericInput("n", label = "n", value = 1e4, min = 0),
      actionButton("simulate", "Simulate!")
    ),
    column(9, plotOutput("hist"))
  )
)
```

要使用 action button，我们需要学习一种新工具。
要了解原因，我们首先使用与上述相同的方法来解决问题。
如上所述，我们引用 `simulate` 时不使用其值来对其进行响应式依赖。


```r
server <- function(input, output, session) {
  x1 <- reactive({
    input$simulate
    rpois(input$n, input$lambda1)
  })
  x2 <- reactive({
    input$simulate
    rpois(input$n, input$lambda2)
  })
  output$hist <- renderPlot({
    freqpoly(x1(), x2(), binwidth = 1, xlim = c(0, 40))
  }, res = 96)
}
```

<div class="figure">
<img src="demos/basic-reactivity/action-button.png" alt="App with action button. See live at &lt;https://hadley.shinyapps.io/ms-action-button&gt;." width="100%" />
<p class="caption">(\#fig:sim-button)App with action button. See live at <https://hadley.shinyapps.io/ms-action-button>.</p>
</div>

<div class="figure">
<img src="diagrams/basic-reactivity/timing-button.png" alt="这个响应式图并没有实现我们的目标；我们添加了一个依赖项，而不是替换现有的依赖项。" width="416" />
<p class="caption">(\#fig:sim-button1-react)这个响应式图并没有实现我们的目标；我们添加了一个依赖项，而不是替换现有的依赖项。</p>
</div>

这会产生 Figure \@ref(fig:sim-button) 中的 app 和 Figure \@ref(fig:sim-button1-react) 中的响应式图。
这并没有达到我们的目标，因为它只是引入了一个新的依赖项：当我们单击 simulate 按钮时，`x1()` 和 `x2()` 将更新，但当 `lambda1`、`lambda2` 或 `n` 更改时，它们也会继续更新。
我们想要替换现有的依赖项，而不是添加它们。

为了解决这个问题，我们需要一个新工具：一种使用输入值而不对其产生响应式依赖的方法。
我们需要 `eventReactive()`，它有两个参数：第一个参数指定要依赖的内容，第二个参数指定要计算的内容。
这使得该 app 仅在单击 `simulate` 时计算 `x1()` 和 `x2()`：


```r
server <- function(input, output, session) {
  x1 <- eventReactive(input$simulate, {
    rpois(input$n, input$lambda1)
  })
  x2 <- eventReactive(input$simulate, {
    rpois(input$n, input$lambda2)
  })

  output$hist <- renderPlot({
    freqpoly(x1(), x2(), binwidth = 1, xlim = c(0, 40))
  }, res = 96)
}
```

Figure \@ref(fig:sim-button2-react) 显示了新的响应式图。
请注意，根据需要，`x1` 和 `x2` 不再对 `lambda1`、`lambda2` 和 `n` 具有响应式依赖：更改它们的值将不会触发计算。
我将箭头保留为非常浅的灰色，只是为了提醒您 `x1` 和 `x2` 继续使用这些值，但不再对它们产生响应式依赖。

<div class="figure">
<img src="diagrams/basic-reactivity/timing-button-2.png" alt="`eventReactive()` 可以将依赖项（黑色箭头）与用于计算结果的值（浅灰色箭头）分开。" width="416" />
<p class="caption">(\#fig:sim-button2-react)`eventReactive()` 可以将依赖项（黑色箭头）与用于计算结果的值（浅灰色箭头）分开。</p>
</div>

## Observers

到目前为止，我们关注的是 app 内部发生的事情。
但有时您需要到达 app 之外，并导致世界其他地方发生副作用。
这可能是将文件保存到共享网络驱动器、将数据发送到 Web API、更新数据库或（最常见）将调试消息打印到控制台。
这些操作不会影响 app 的外观，因此您不应使用输出和 `render` 函数。
相反，您需要使用**观察者（observer）**。

创建观察者的方法有多种，我们将在 Section \@ref(observers-details) 稍后再讨论它们。
现在，我想向您展示如何使用 `observeEvent()`，因为当您第一次学习 Shiny 时，它为您提供了一个重要的调试工具。

`observeEvent()` 与 `eventReactive()` 非常相似。
它有两个重要的参数：`eventExpr` 和 `handlerExpr`。
第一个参数是要依赖的输入或表达式；第二个参数是将运行的代码。
例如，对 `server()` 进行以下修改意味着每次更新 `name` 时，都会向控制台发送一条消息：


```r
ui <- fluidPage(
  textInput("name", "What's your name?"),
  textOutput("greeting")
)

server <- function(input, output, session) {
  string <- reactive(paste0("Hello ", input$name, "!"))
  
  output$greeting <- renderText(string())
  observeEvent(input$name, {
    message("Greeting performed")
  })
}
```

`observeEvent()` 和 `eventReactive()` 之间有两个重要的区别：

-   您没有将 `observeEvent()` 的结果分配给变量，因此
-   你不能从其他响应式消费者那里引用它。

观察者和输出密切相关。
您可以将输出视为具有特殊的副作用：更新用户浏览器中的 HTML。
为了强调这种接近性，我们将在响应式图中以相同的方式绘制它们。
这会产生如 Figure \@ref(fig:observer) 所示的响应式图。

<div class="figure">
<img src="diagrams/basic-reactivity/graph-3.png" alt="在响应式图中，observer 看起来与 output 相同" width="306" />
<p class="caption">(\#fig:observer)在响应式图中，observer 看起来与 output 相同</p>
</div>

## Summary

本章应该可以提高您对 Shiny apps 后端（响应用户操作的 `server()` 代码）的理解。
您还迈出了掌握支撑 Shiny 的响应式编程范例的第一步。
你在这里学到的东西将会带你走很长的路；我们将在 Chapter \@ref(reactive-motivation) 中回到基本理论。
响应式非常强大，但它与您最习惯的 R 编程的命令式风格也有很大不同。
如果需要一段时间才能了解所有后果，请不要感到惊讶。

本章总结了我们对 Shiny 基础的概述。
下一章将通过创建一个旨在支持数据分析的更大的 Shiny app 来帮助您练习到目前为止所看到的材料。
