Add more interactivity to interactive charts
================

<!-- [![CRAN_Status_Badge](http://www.r-pkg.org/badges/version/manipulateWidget)](http://cran.r-project.org/package=manipulateWidget) -->
This R package is largely inspired by the `manipulate` package from Rstudio. It provides the function \``manipulateWidget` that can be used to create in a very easy way a graphical interface that let the user modify the data or the parameters of an interactive chart. Technically, the function generates a Shiny gadget, but the user does not even have to know what is Shiny.

The package also provides the `combineWidgets` function to easily combine multiple interactive charts in a single view. Of course both functions can be used together.

Here is an example that uses packages `dygraphs` and `plot_ly`.

![An example of what one can do with manipulateWidgets](vignettes/fancy-example.gif)

Installation
------------

The package can be installed from CRAN:

``` r
install.packages("manipulateWidget")
```

You can also install the latest development version from github:

``` r
devtools::install_github("rte-antares-rpackage/manipulateWidget", ref="develop")
```

Getting started
---------------

The main function of the package is `manipulateWidget`. It accepts an expression that generates an interactive chart (and more precisely an htmlwidget object. See <http://www.htmlwidgets.org/> if you have never heard about it) and a set of controls created with functions mwSlider, mwCheckbox... which are used to dynamically change values within the expression. Each time the user modifies the value of a control, the expression is evaluated again and the chart is updated. Consider the following code:

``` r
manipulateWidget(
  myPlotFun(country), 
  country = mwSelect(c("BE", "DE", "ES", "FR"))
)
```

It generates a graphical interface with a select input on its left with options "BE", "DE", "ES", "FR". The value of this input is mapped to the variable `country` in the expression. By default, at the beginning the value of `country` will be equal to the first choice of the input. So the function will first execute `myPlotFun("BE")` and the result will be displayed in the main panel of the interface. If the user changes the value to "FR", then the expression `myPlotFun("FR")` is evaluated and the new result is displayed.

The interface also contains a button "Done". When the user clicks on it, the last chart is returned. It can be stored in a variable, be modified by the user, saved as a html file with saveWidget from package htmlwidgets or converted to a static image file with package `webshot`.

Of course, you can create as many controls as you want. The interface of the animated example in the introduction was generated with the following code:

``` r
manipulateWidget(
  myPlotFun(distribution, range, title),
  distribution = mwSelect(choices = c("gaussian", "uniform")),
  range = mwSlider(2000, 2100, value = c(2000, 2100), label = "period"),
  title = mwText()
)
```

To see all available controls that can be added to the UI, take a look at the list of the functions of the package:

``` r
help(package = "manipulateWidget")
```

Combining widgets
-----------------

The `combineWidgets` function gives an easy way to combine interactive charts (like `par(mfrow = c(...))` or `layout` for static plots). To do it, one has simply to pass to the function the widgets to combine. In the next example, we visualize two random time series with dygraphs and combine them.

``` r
library(dygraphs)

plotRandomTS <- function(id) {
  dygraph(data.frame(x = 1:10, y = rnorm(10)), main = paste("Random plot", id))
}

combineWidgets(plotRandomTS(1), plotRandomTS(2))
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-33d9d1238b195280b120">{"x":{"data":[{"attrs":{"title":"Random plot 1","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[1.40601158108116,0.495471566542517,0.840357945474041,1.75868694194005,2.77712438830544,-1.98537975060116,-1.10458985173153,-1.1598323489642,0.134427331143017,0.42412215011891]]},{"attrs":{"title":"Random plot 2","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[0.252554313730628,-0.8849729427917,0.00102699615810254,2.12131604300841,1.64793581576162,-0.31370119380565,0.832745875307605,1.32065379231245,0.515518089580786,0.53262335043403]]}],"widgetType":["dygraphs","dygraphs"],"elementId":["widget573714066","widget261698369"],"html":"<div class=\"cw-container\"><div class=\"cw-subcontainer\"><div class=\"cw-content cw-by-row\"><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget573714066\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget261698369\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div>\u003c/div>\u003c/div>\u003c/div>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
The functions tries to find the best number of columns and rows. But one can control them with parameters `nrow`and `ncol`. It is also possible to control their relative size with parameters `rowsize` and `colsize`. To achieve complex layouts, it is possible to use nested combined widgets. Here is an example of a complex layout. (Note that this is only a screenshot of the result. In reality, these charts are interactive).

``` r
combineWidgets(
  ncol = 2, colsize = c(2, 1),
  plotRandomTS(1),
  combineWidgets(
    ncol = 1,
    plotRandomTS(2),
    plotRandomTS(3),
    plotRandomTS(4)
  )
)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-93d17804e1e3509d6b85">{"x":{"data":[{"attrs":{"title":"Random plot 1","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-1.52475777331185,1.2460110928202,-2.05062628845192,0.509007224466827,-0.516258844910193,-0.133225227481221,-1.8239969165476,-0.102731607311447,-0.49596272526622,-0.17656313671648]]},{"data":[{"attrs":{"title":"Random plot 2","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-1.11638479245043,-2.38791868956293,0.640255225338178,0.36091808200555,1.37077034570963,1.03159127766336,-0.449951217465151,1.37228816989778,0.118579498029422,-1.13838439898559]]},{"attrs":{"title":"Random plot 3","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-0.460072431786968,0.891771875307031,-0.617694493914099,-0.454637963969381,-2.02944710043045,-0.141383083615025,-0.641347219804898,-0.47860290726453,0.483304621067876,0.000983254652010224]]},{"attrs":{"title":"Random plot 4","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-1.35497397030757,-0.892787027112247,0.627331062519029,0.414892272969711,1.92982688845057,-2.42779648502848,-0.628554611764799,0.14300775882986,1.83764935148941,1.34585151456733]]}],"widgetType":["dygraphs","dygraphs","dygraphs"],"elementId":["widget520890502","widget827138578","widget541738307"],"html":"<div class=\"cw-container\"><div class=\"cw-subcontainer\"><div class=\"cw-content cw-by-row\"><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget520890502\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget827138578\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget541738307\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div>\u003c/div>\u003c/div>\u003c/div>"}],"widgetType":["dygraphs","combineWidgets"],"elementId":["widget968732753","widget305811669"],"html":"<div class=\"cw-container\"><div class=\"cw-subcontainer\"><div class=\"cw-content cw-by-row\"><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:2;-webkit-flex:2\">\n                 <div id=\"widget968732753\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget305811669\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div>\u003c/div>\u003c/div>\u003c/div>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
Even if the main use of `combineWidgets` is to combine `htmlwidgets`, it can also display text or html tags. It can be useful to include comments in a chart. Moreover it has arguments to add a title and to add some html content in the sides of the chart.

``` r
combineWidgets(
  plotRandomTS(1),
  plotRandomTS(2),
  plotRandomTS(3),
  plotRandomTS(4),
  title = "Four random plots",
  header = "Here goes the header content. <span style='color:red'>It can include html code</span>.",
  footer = "Here goes the footer content.",
  leftCol = "<div style='margin-top:150px;'>left column</div>",
  rightCol = "<div style='margin-top:150px;'>right column</div>"
)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-72451643c8c337eafb3e">{"x":{"data":[{"attrs":{"title":"Random plot 1","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-0.86902459606021,-1.28256942731799,0.0614348235418766,1.56852292240902,1.08807398701187,-1.16297964683243,-0.476008517188176,-0.343550326240136,0.349543177040756,-0.50621147009952]]},{"attrs":{"title":"Random plot 2","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-0.241746944048865,-1.35348337281729,0.403918152196466,-0.375088514252098,0.0864889397900065,1.13523677585508,-0.566265533536967,0.45427718945896,0.0706917807826116,-0.0969424746079407]]},{"attrs":{"title":"Random plot 3","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[1.30517518515189,-0.284973889596889,0.761208998946568,-0.85696843758635,-1.31518844376822,-0.179891913936536,1.18412331051385,0.184451362388942,-1.02780671877461,-0.91190295729252]]},{"attrs":{"title":"Random plot 4","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-0.183231739155073,0.917196572916035,0.681011221065169,0.524295315175646,-0.0876304022469334,-0.769165069488984,1.69885473606541,0.346923314208442,-0.928674968897156,0.787165653225282]]}],"widgetType":["dygraphs","dygraphs","dygraphs","dygraphs"],"elementId":["widget139780977","widget640947595","widget332774923","widget904546622"],"html":"<div class=\"cw-container\"><div><h2 class=\"cw-title\" style=\"\">Four random plots\u003c/h2>\u003c/div><div>Here goes the header content. <span style='color:red'>It can include html code\u003c/span>.\u003c/div><div class=\"cw-subcontainer\"><div style='height:100%'><div style='margin-top:150px;'>left column\u003c/div>\u003c/div><div class=\"cw-content cw-by-row\"><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget139780977\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget640947595\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget332774923\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget904546622\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div>\u003c/div><div style='height:100%'><div style='margin-top:150px;'>right column\u003c/div>\u003c/div>\u003c/div><div>Here goes the footer content.\u003c/div>\u003c/div>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
Advanced usage
--------------

### Grouping controls

If you have a large number of inputs, you can easily group them. To do so, simply pass to the function `manipulateWidget` a list of inputs instead of passing directly the inputs. Here is a toy example. Groups are by default collapsed and user can click on their title to display/collapse then.

``` r
mydata <- data.frame(x = 1:100, y = rnorm(100))
manipulateWidget(
  dygraph(mydata[range[1]:range[2], ],
          main = title, xlab = xlab, ylab = ylab),
  range = mwSlider(1, 100, c(1, 100)),
  "Graphical parameters" = list(
    title = mwText("Fictive time series"),
    xlab = mwText("X axis label"),
    ylab = mwText("Y axis label")
  )
)
```

![Grouping inputs](vignettes/groups-inputs.gif)

### Conditional inputs

Sometimes some inputs are relevant only if other inputs have some value. `manipulateWidget`provides a way to show/hide inputs conditionally to the value of the other inputs thanks to parameter `.display`. This parameter expects a named list of expressions. The names are the ones of the inputs to show/hide and the expressions can include any input and have to evaluate to `TRUE/FALSE`. Here is a toy example, using package `plot_ly`. User can choose points or lines to represent some data. If he chooses lines, then an input appears to let him choose the width of the lines.

``` r
mydata <- data.frame(x = 1:100, y = rnorm(100))

myPlot <- function(type, lwd) {
  if (type == "points") {
    plot_ly(mydata, x= ~x, y = ~y, type = "scatter", mode = "markers")
  } else {
    plot_ly(mydata, x= ~x, y = ~y, type = "scatter", mode = "lines", 
            line = list(width = lwd))
  }
}

manipulateWidget(
  myPlot(type, lwd),
  type = mwSelect(c("points", "lines"), "points"),
  lwd = mwSlider(1, 10, 1),
  .display = list(lwd = type == "lines")
)
```

![Conditional inputs](vignettes/conditional-inputs.gif)

### Updating a widget

The "normal" use of `manipulateWidget` is to provide an expression that always return an `htmlwidget`. In such case, every time the user changes the value of an input, the current widget is destroyed and a new one is created and rendered. This behavior is not optimal and sometimes it can be painful for the user: consider for instance an interactive map. Each time user changes an input, the map is destroyed and created again, then zoom and location on the map are lost every time.

Some packages provide functions to update a widget that has already been rendered. This is the case for instance for package `leaflet` with the function `leafletProxy`. To use such functions, `manipulateWidget` evaluates the parameter `.expr` with two extra variables:

-   `.initial`: `TRUE` if the expression is evaluated for the first time and then the widget has not been rendered yet, `FALSE` if the widget has already been rendered.

-   `.session`: A shiny session object.

Moreover the ID of the rendered widget is always "output". Then it is quite easy to write an expression that initializes a widget when it is evaluated the first time and then that updates this widget. Here is an example using package `leaflet`.

``` r
lon <- rnorm(10, sd = 20)
lat <- rnorm(10, sd = 20)

myMapFun <- function(radius, color, initial, session) {
  if (initial) {
    # Widget has not been rendered
    map <- leaflet() %>% addTiles()
  } else {
    # widget has already been rendered
    map <- leafletProxy("output", session) %>% clearMarkers()
  }

  map %>% addCircleMarkers(lon, lat, radius = radius, color = color)
}

manipulateWidget(myMapFun(radius, color, .initial, .session),
                 radius = mwSlider(5, 30, 10),
                 color = mwSelect(c("red", "blue", "green")))
```

![Conditional inputs](vignettes/update-widget.gif)

By the way
----------

Here is the complete code to generate the animated example in the introduction:

``` r
library(dygraphs)
library(plotly)
library(manipulateWidget)

myPlotFun <- function(distribution, range, title) {
  randomFun <- switch(distribution, gaussian = rnorm, uniform = runif)
  myData <- data.frame(
    year = seq(range[1], range[2]),
    value = randomFun(n = diff(range) + 1)
  )
  combineWidgets(
    ncol = 2, colsize = c(2, 1),
    dygraph(myData, main = title),
    combineWidgets(
      plot_ly(x = myData$value, type = "histogram"),
      paste(
        "The graph on the left represents a random time series generated using a <b>",
        distribution, "</b>distribution function.<br/>",
        "The chart above represents the empirical distribution of the generated values."
      )
    )
  )
  
}

manipulateWidget(
  myPlotFun(distribution, range, title),
  distribution = mwSelect(choices = c("gaussian", "uniform")),
  range = mwSlider(2000, 2100, value = c(2000, 2100), label = "period"),
  title = mwText()
)
```
