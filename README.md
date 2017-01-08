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

<script type="application/json" data-for="htmlwidget-a2f508ae9731b674e87a">{"x":{"data":[{"attrs":{"title":"Random plot 1","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-0.437984051252037,2.47405816874266,1.558237270483,-0.936478022959284,-0.343784894905779,-0.342342175959372,-0.0521619499137192,-0.621290762635185,0.726510941908326,-0.212560192189712]]},{"attrs":{"title":"Random plot 2","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-1.47828663386156,-1.3328661277628,0.92734756347618,0.527261362271806,-0.664217099030414,1.05012737842236,1.53708212569008,0.881892195553991,-0.772361416740201,1.24387955389871]]}],"widgetType":["dygraphs","dygraphs"],"elementId":["widget853238737","widget190190754"],"html":"<div class=\"cw-container\"><div class=\"cw-subcontainer\"><div class=\"cw-content cw-by-row\"><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget853238737\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget190190754\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div>\u003c/div>\u003c/div>\u003c/div>"},"evals":[],"jsHooks":[]}</script>
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

<script type="application/json" data-for="htmlwidget-c4987019a6df38948405">{"x":{"data":[{"attrs":{"title":"Random plot 1","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[0.346138322782276,-1.85703120727889,0.233463791424466,0.557012478589987,1.34167220018238,0.737119744590809,-0.156481076010666,0.391062655128304,-0.776028633893976,0.0488765986907024]]},{"data":[{"attrs":{"title":"Random plot 2","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[0.631114388892456,0.28693204262128,0.454132590207286,-1.2580625111143,1.48154385738307,-2.33442700944671,-0.541224399430517,-0.700968336065547,-0.760242321557685,-1.19623867577945]]},{"attrs":{"title":"Random plot 3","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-0.447297624708491,-0.866518566566469,-0.138123656083068,1.11119992306126,0.518596177881572,-0.339174548756961,-1.62165002770082,-0.327475807224424,-0.523510909823763,-0.369237275473394]]},{"attrs":{"title":"Random plot 4","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[0.0227465163805343,-0.102310488857984,-0.701794579879853,0.517194174622881,-0.697238632436384,-0.352221121171442,0.171767340147331,-1.27913211678496,0.125900451413626,-0.103594066231462]]}],"widgetType":["dygraphs","dygraphs","dygraphs"],"elementId":["widget610628595","widget720085839","widget239403285"],"html":"<div class=\"cw-container\"><div class=\"cw-subcontainer\"><div class=\"cw-content cw-by-row\"><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget610628595\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget720085839\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget239403285\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div>\u003c/div>\u003c/div>\u003c/div>"}],"widgetType":["dygraphs","combineWidgets"],"elementId":["widget75639675","widget433974652"],"html":"<div class=\"cw-container\"><div class=\"cw-subcontainer\"><div class=\"cw-content cw-by-row\"><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:2;-webkit-flex:2\">\n                 <div id=\"widget75639675\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget433974652\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div>\u003c/div>\u003c/div>\u003c/div>"},"evals":[],"jsHooks":[]}</script>
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

<script type="application/json" data-for="htmlwidget-bc639c93acc71af0ee22">{"x":{"data":[{"attrs":{"title":"Random plot 1","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[-0.490651656724251,0.223773391401521,-0.536644206977287,0.192911432028301,0.0333095787655606,-0.793789184826496,0.818407264845104,-0.526996391474613,1.21970555982532,-1.26402190700231]]},{"attrs":{"title":"Random plot 2","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[1.30110414875019,0.86598675762428,-0.24709544298791,0.549379947774406,-0.270710320229195,-0.623074239832572,1.19829441215615,-1.68666991856752,0.629249335447556,-0.200783608389545]]},{"attrs":{"title":"Random plot 3","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[2.28717036367746,-1.26504891908543,0.699847419920877,1.16925024576096,-0.317437120453802,-0.919651743297421,0.0996909438240766,0.138835991152573,0.917260606198515,-0.496440933216207]]},{"attrs":{"title":"Random plot 4","labels":["x","y"],"legend":"auto","retainDateWindow":false,"axes":{"x":{"pixelsPerLabel":60}}},"annotations":[],"shadings":[],"events":[],"format":"numeric","data":[[1,2,3,4,5,6,7,8,9,10],[0.619760130682565,1.32155725129884,1.04982628888615,0.288343168405171,-0.663228384157146,-0.0127150676663977,-0.344107149400675,0.501955962530138,0.358804983530192,-0.350200089607203]]}],"widgetType":["dygraphs","dygraphs","dygraphs","dygraphs"],"elementId":["widget758766336","widget735637016","widget4594562","widget687666302"],"html":"<div class=\"cw-container\"><div><h2 class=\"cw-title\" style=\"\">Four random plots\u003c/h2>\u003c/div><div>Here goes the header content. <span style='color:red'>It can include html code\u003c/span>.\u003c/div><div class=\"cw-subcontainer\"><div style='height:100%'><div style='margin-top:150px;'>left column\u003c/div>\u003c/div><div class=\"cw-content cw-by-row\"><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget758766336\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget735637016\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div><div class=\"cw-row cw-by-row\" style=\"flex:1;-webkit-flex:1\"><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget4594562\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div><div class=\"cw-col\" style=\"flex:1;-webkit-flex:1\">\n                 <div id=\"widget687666302\" class=\"cw-widget\" style=\"width:100%;height:100%\">\u003c/div>\n               \u003c/div>\u003c/div>\u003c/div><div style='height:100%'><div style='margin-top:150px;'>right column\u003c/div>\u003c/div>\u003c/div><div>Here goes the footer content.\u003c/div>\u003c/div>"},"evals":[],"jsHooks":[]}</script>
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
