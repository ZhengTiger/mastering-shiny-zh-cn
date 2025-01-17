# Preface {.unnumbered}



## What is Shiny?

如果您以前从未使用过 Shiny，欢迎！
Shiny 是一个 R 包，可让您轻松创建丰富的交互式 Web 应用程序。
Shiny 允许您在 R 中进行工作并通过网络浏览器公开它，以便任何人都可以使用它。
Shiny 让您可以轻松地以最少的痛苦来生成精美的 Web 应用程序，从而使您看起来很棒。

在过去，创建 Web 应用程序对于大多数 R 用户来说都很困难，因为：

-   您需要深入了解 HTML、CSS 和 JavaScript 等 Web 技术。

-   制作复杂的交互式应用程序需要仔细分析交互流，以确保当输入更改时，仅更新相关的输出。

Shiny 使 R 程序员可以通过以下方式更轻松地创建 Web 应用程序：

-   提供一组精心策划的用户界面（简称 **UI**）功能，用于生成常见任务所需的 HTML、CSS 和 JavaScript。
    这意味着您不需要了解 HTML/CSS/JavaScript 的详细信息，除非您想要超越 Shiny 为您提供的基础。

-   引入一种称为**反应式编程（reactive programming）**的新编程风格，它自动跟踪代码片段的依赖关系。
    这意味着每当输入发生变化时，Shiny 都可以自动找出如何做最少的工作来更新所有相关的输出。

人们使用 Shiny 来：

-   创建跟踪重要的高级性能指标的仪表盘，同时促进深入了解需要更多调查的指标。

-   用交互式应用程序替换数百页 PDF，让用户可以跳转到他们关心的结果的确切部分。

-   通过信息丰富的可视化和交互式敏感性分析，向非技术受众传达复杂的模型。

-   为常见工作流程提供自助数据分析，用 Shiny 应用程序取代电子邮件请求，允许人们上传自己的数据并执行标准分析。
    您可以为没有编程技能的用户提供复杂的 R 分析。

-   创建用于教授统计学和数据科学概念的交互式演示，使学习者能够调整输入并观察分析中这些变化的下游影响。

简而言之，Shiny 使您能够将一些 R 超能力传递给任何可以使用网络的人。

## Who should read this book?

本书针对两种主要读者：

-   有兴趣了解 Shiny 以便将他们的分析转化为交互式 Web 应用程序的 R 用户。
    为了充分利用本书，您应该能够熟练地使用 R 进行数据分析，并且至少应该编写一些函数。

-   现有的 Shiny 用户希望提高对 Shiny 基础理论的了解，以便更快、更轻松地编写更高质量的应用程序。
    如果您的应用程序开始变得越来越大并且您开始在管理复杂性方面遇到问题，那么您应该会发现这本书特别有帮助。

## What will you learn?

本书分为四个部分：

1.  在 "Getting started" 部分，您将学习 Shiny 的基础知识，以便您可以尽快启动并运行。
    您将了解应用程序结构的基础知识、有用的 UI 组件以及响应式编程的基础知识。

2.  "Shiny in action" 部分建立在帮助您解决常见问题的基础知识之上，包括向用户提供反馈、上传和下载数据、使用代码生成 UI、减少代码重复以及使用 Shiny 对 tidyverse 进行编程。

3.  在 "Mastering reactivity" 部分，您将深入了解反应式编程的理论和实践，这是 Shiny 的编程范例。
    如果您是现有的 Shiny 用户，您将从本章中获得最大的价值，因为它将为您提供坚实的理论基础，使您能够创建专门针对您的问题定制的新工具。

4.  最后，在 "Best practices" 部分，我们将完成对使您的 Shiny 应用程序在生产中良好运行的有用技术的调查。
    您将学习如何将复杂的应用程序分解为函数和模块，如何使用包来组织代码，如何测试代码以确保其正确，以及如何衡量和提高性能。

## What won't you learn?

本书的重点是制作有效的 Shiny 应用程序并理解反应式的基本理论。
我将尽力展示数据科学、R 编程和软件工程的最佳实践，但您需要其他参考资料来掌握这些重要技能。
如果您喜欢我在本书中的写作，您可能会喜欢我关于这些主题的其他书籍：[R for Data Science](http://r4ds.had.co.nz/)、[Advanced R](http://adv-r.hadley.nz/) 和 [R Packages](http://r-pkgs.org/)。

还有一些特定于 Shiny 的重要主题我没有涉及：

-   本书仅涵盖内置的用户界面工具包。
    这并没有提供最性感的设计，但它很容易学习并且可以让你走得很远。
    如果您有其他需求（或者只是对默认设置感到厌倦），还有许多其他软件包可以提供替代前端。
    参见 Section \@ref(other-tools) 获取更多细节。

-   部署 Shiny 应用程序。
    将 Shiny “投入生产” 超出了本书的讨论范围，因为每个公司的情况差异很大，而且其中大部分与 R 无关（大多数挑战往往是文化或组织方面的，而不是技术方面的）。
    如果您在生产中不熟悉 Shiny，我建议您从 Joe Cheng's 2019 rstudio::conf keynote: <https://rstudio.com/resources/rstudioconf-2019/shiny-in-production-principles-practices-and-tools/> 开始。
    这将为您提供基本情况，广泛讨论将 Shiny 投入生产需要什么以及如何克服您可能面临的一些挑战。
    完成此操作后，请访问 [RStudio Connect website](https://rstudio.com/products/connect/)，了解用于在公司内部署应用程序的 RStudio 产品，并访问 [Shiny website](https://shiny.rstudio.com/articles/#deployment)了解其他常见部署场景。

## Prerequisites {#prerequisites}

在我们继续之前，请确保您拥有本书所需的所有软件：

-   **R**：如果你还没有安装 R，你可能读错书了；我假设您对本书中的 R 有基本的了解。
    如果您想学习如何使用 R，我会推荐我的 [*R for Data Science*](https://r4ds.had.co.nz/)，它旨在帮助您轻松上手并运行 R。

-   **RStudio**：RStudio 是一个免费的开源 R 集成开发环境 (IDE)。
    虽然您可以在任何 R 环境（包括 R GUI 和 [ESS](http://ess.r-project.org)）中编写和使用 Shiny 应用程序，但 RStudio 有一些专门用于创作、调试和部署 Shiny 应用程序的出色功能。
    我们建议您尝试一下，但并不需要使用 Shiny 或本书即可获得成功。
    您可以从 <https://www.rstudio.com/products/rstudio/download> 下载 RStudio Desktop。

-   **R packages**：本书使用了一堆 R 包。
    您可以通过运行以下命令一次性安装它们：

    

    
    ```r
    install.packages(c(
      "gapminder", "ggforce", "gh", "globals", "openintro", "profvis", 
      "RSQLite", "shiny", "shinycssloaders", "shinyFeedback", 
      "shinythemes", "testthat", "thematic", "tidyverse", "vroom", 
      "waiter", "xml2", "zeallot" 
    ))
    ```

    如果您过去下载过 Shiny，请确保您的版本至少为 1.6.0。

## Acknowledgements

这本书是公开撰写的，完成后各章节会在 Twitter 上发布广告。
这确实是社区的努力：许多人阅读草稿、修正拼写错误、提出改进建议并贡献内容。
如果没有这些贡献者，这本书就不会这么好，我非常感谢他们的帮助。




```
#> Warning: package 'dplyr' was built under R version 4.2.3
```

A big thank you to all 83 people who contributed specific improvements via GitHub pull requests (in alphabetical order by username): Adam Pearce (\@1wheel), Adi Sarid (\@adisarid), Alexandros Melemenidis (\@alex-m-ffm), Anton Klåvus (\@antonvsdata), Betsy Rosalen (\@betsyrosalen), Michael Beigelmacher (\@brooklynbagel), Bryan Smith (\@BSCowboy), c1au6io_hh (\@c1au6i0), \@canovasjm, Chris Beeley (\@ChrisBeeley), \@chsafouane, Chuliang Xiao (\@ChuliangXiao), Conor Neilson (\@condwanaland), \@d-edison, Dean Attali (\@daattali), DanielDavid521 (\@Danieldavid521), David Granjon (\@DivadNojnarg), Eduardo Vásquez (\@edovtp), Emil Hvitfeldt (\@EmilHvitfeldt), Emilio (\@emilopezcano), Emily Riederer (\@emilyriederer), Eric Simms (\@esimms999), Federico Marini (\@federicomarini), Frederik Kok Hansen (\@fkoh111), Frans van Dunné (\@FvD), Giorgio Comai (\@giocomai), Hedley (\@heds1), Henning (\@henningsway), Hlynur (\@hlynurhallgrims), \@hsm207, \@jacobxk, James Pooley (\@jamespooley), Joe Cheng (\@jcheng5), Julien Colomb (\@jcolomb), Juan C Rodriguez (\@jcrodriguez1989), Jennifer (Jenny) Bryan (\@jennybc), Jim Hester (\@jimhester), Joachim Gassen (\@joachim-gassen), Jon Calder (\@jonmcalder), Jonathan Carroll (\@jonocarroll), Julian Stanley (\@julianstanley), \@jyuu, \@kaanpekel, Karandeep Singh (\@kdpsingh), Robert Kirk DeLisle (\@KirkDCO), Elaine (\@loomalaine), Malcolm Barrett (\@malcolmbarrett), Marly Gotti (\@marlycormar), Matthew Wilson (\@MattW-Geospatial), Matthew T. Warkentin (\@mattwarkentin), Mauro Lepore (\@maurolepore), Maximilian Rohde (\@maxdrohde), Matthew Berginski (\@mbergins), Michael Dewar (\@michael-dewar), Mine Cetinkaya-Rundel (\@mine-cetinkaya-rundel), Maria Paula Caldas (\@mpaulacaldas), nthobservation (\@nthobservation), Pietro Monticone (\@pitmonticone), psychometrician (\@psychometrician), Ram Thapa (\@raamthapa), Janko Thyson (\@rappster), Rebecca Janis (\@rbjanis), Tom Palmer (\@remlapmot), Russ Hyde (\@russHyde), Barret Schloerke (\@schloerke), Scott (\@scottyd22), Matthew Sedaghatfar (\@sedaghatfar), Shixiang Wang (\@ShixiangWang), Praer (Suthira Owlarn) (\@sowla), Sébastien Rochette (\@statnmap), \@stevensbr, André Calero Valdez (\@Sumidu), Tanner Stauss (\@tmstauss), Tony Fujs (\@tonyfujs), Stefan Moog (\@trekonom), Jeff Allen (\@trestletech), Trey Gilliland (\@treygilliland), Albrecht (\@Tungurahua), Valeri Voev (\@ValeriVoev), Vickus (\@Vickusr), William Doane (\@WilDoane), 黄湘云 (\@XiangyunHuang), gXcloud (\@xwydq).

## Colophon

本书是使用 [bookdown](http://bookdown.org/) 在 [RStudio](http://www.rstudio.com/ide/) 中编写的。
该网站由 [netlify](http://netlify.com/) 托管，并在 [Github Actions](https://github.com/features/actions) 每次提交后自动更新。完整的源代码可以从 [GitHub](https://github.com/hadley/mastering-shiny) 获取。

本书的这个版本是使用 R 版本 R version 4.2.0 (2022-04-22 ucrt) 和以下软件包构建的：



|package         |version |source         |
|:---------------|:-------|:--------------|
|gapminder       |1.0.0   |CRAN (R 4.2.3) |
|ggforce         |0.4.1   |CRAN (R 4.2.1) |
|gh              |1.4.0   |CRAN (R 4.2.3) |
|globals         |0.16.2  |CRAN (R 4.2.2) |
|openintro       |2.4.0   |CRAN (R 4.2.3) |
|profvis         |0.3.8   |CRAN (R 4.2.3) |
|RSQLite         |2.3.1   |CRAN (R 4.2.3) |
|shiny           |1.7.4   |CRAN (R 4.2.2) |
|shinycssloaders |1.0.0   |CRAN (R 4.2.3) |
|shinyFeedback   |0.4.0   |CRAN (R 4.2.3) |
|shinythemes     |1.2.0   |CRAN (R 4.2.3) |
|testthat        |3.1.8   |CRAN (R 4.2.3) |
|thematic        |NA      |NA             |
|tidyverse       |2.0.0   |CRAN (R 4.2.3) |
|vroom           |1.6.3   |CRAN (R 4.2.3) |
|waiter          |0.2.5   |CRAN (R 4.2.3) |
|xml2            |1.3.4   |CRAN (R 4.2.3) |
|zeallot         |0.1.0   |CRAN (R 4.2.2) |






