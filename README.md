<img src="man/figures/hexsticker.svg" align="right" alt="" width="130" />

# imola
<!-- badges: start -->
<!-- [![R-CMD-check](https://github.com/pedrocoutinhosilva/imola/workflows/R-CMD-check/badge.svg)](https://CRAN.R-project.org/package=imola) -->
[![cranlogs](https://www.r-pkg.org/badges/version/imola)](https://CRAN.R-project.org/package=imola)
[![cranlogs](https://cranlogs.r-pkg.org/badges/imola)](https://CRAN.R-project.org/package=imola)
[![total](https://cranlogs.r-pkg.org/badges/grand-total/imola)](https://CRAN.R-project.org/package=imola)
<!-- badges: end -->

Bridging the gap between R/shiny and CSS layouts (grid and Flexbox)!

If you're familiar with CSS, you might have felt by now that layouts in shiny can be very awkward to set up.

While R/shiny does give you access to bootstrap's row and column system, these do have some limitation and changing layouts usually requires having to rebuild a large portion of the UI. As web development improved, this system also showed to not always be the most flexible, specially when trying to replicate a design or mockup not using this 12 column system, making it challenging to achieve without a lot of additional custom styling.

Imola (named after the first city ever to be given a technical blueprint by Leonardo da Vinci) aims at giving you more layout creation options directly in R/shiny, without the hassle of having to create custom CSS every time.

With imola you can easily leverage typical CSS layouts (grid and Flexbox) directly in your UI functions, including media breakpoints to fit different screen sizes and devices.

You can also save your favorite layouts to use later via a templating system that allows you to define a layout, name it, and simply use it in as many elements as you need. If creating isn't your thing, imola also comes with a built in collection of templates traditionally used web layouts, making it even easier to spice up your dashboards!

# installation
1 - Install the package:
<!--
from CRAN:
```R
install.packages('imola')
``` -->

from github:
```R
devtools::install_github('pedrocoutinhosilva/imola')
```

2 - Include the library into your project:
```R
# global.R
library(imola)
```

You are now ready to go! Check the usage section for some examples and more information on how to use the diferent grid and flex functions to get started!

# Usage

The bread and butter of imola are the `gridPanel()` and `flexPanel()` functions. These allow you to replace any HTML tag that serves as a container (`div`, `section`, `main`, `nav`, ...) with a tag that uses one of the specific css layout systems (grid or flexbox).

If you are not familiar with these layout systems, I definitely recommend [this article](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Relationship_of_Grid_Layout) to get you up to speed.

The basic difference between CSS Grid Layout and CSS Flexbox Layout is that flexbox was designed for layout in one dimension - either a row or a column. Grid was designed for two-dimensional layout - rows, and columns at the same time. This means if you want fine control over columns and rows, you should aim at using grid, if you only care about one of the dimensions, flex will most likely work just fine.

## Grid
The grid family focuses on providing support for the CSS Grid standard. In imola you can find 2 functions that allow you to create a new grid component. `gridPanel()` and `gridPage()`. `gridPage()` is simply a wrapper for `gridPanel()` that allows you to create a page UI element without the need of using any of the built in shiny functions.

If you're interested in more information about the full array of options in the CSS grid standard from the CSS side, I recommend [this article](https://css-tricks.com/snippets/css/complete-guide-grid/) to get you started.

## Flex
The flex family focuses on providing support for the CSS flexbox standard. In imola you can find 2 functions that allow you to create a new grid component. `flexPanel()` and `flexPage()`. Similar to what happens in the grid family, `flexPage()` is simply a wrapper for `flexPanel()` that allows you to create a page UI element without the need of using any of the built in shiny functions.

If you're interested in more information about the full array of options in the CSS flexbox standard from the CSS side, I recommend [this article](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) to get you started.

# Breakpoints
Breakpoints are a way to adjust your layouts to different screen sizes. As the screen size gets larger or smaller, it is often required to adjust the position or size of the different elements in a page to make sure things dont appear broken or out of place.

Depending on the CSS framework that is being used in your project, different systems are in place to allow this to be a bit more automated, if you are familiar with base shiny, you might have even used these systems without realizing, by using the `fluidRow()` function. `fluidRow()` will trigger layout changes to your `columns` at specific screen sizes, based on [bootstrap 3](https://shiny.rstudio.com/articles/layout-guide.html) breakpoints (The base CSS framework in shiny).

While the `fluidRow()` solution is quite easy to use, it also comes with many constrains and does not allow for a very fine control of these layout changes. If complex enough layouts, you might even be required to write additional CSS to add new behavior or specific elements or screen sizes.

Imola takes a slightly different approach to breakpoints; Out of the box it uses the same breakpoints as base shiny (bootstrap 3) and for each function named attribute you are able to pass either a value for that attribute or a named list of different values for different breakpoints.

As a practical example, lets say we have the following grid areas in a gridPanel():

```R
# as a gridPanel() argument
areas = c(
  "area1 area1 area1",
  "area2 area3 area3",
  "area2 area3 area3"
)
# or
areas = list(
  c("area1", "area1", "area1"),
  c("area2", "area3", "area3"),
  c("area2", "area3", "area3")
)
```
This grid contains 3 areas (`area1`, `area2`, `area3`), with `area2` clearly serving a sidebar. Viewing this grid on a small screen could lead to a very small sidebar, so one solution is to modify this grid specifically for a breakpoint that targets mobile.

As mentioned before, shiny and imola, by default use the bootstrap 3 breakpoint system, that contains a few different breakpoints:

![Screenshot](man/figures/bootstrap3-breakpoints.png)

We can see what names imola expects for each of these using either `activeBreakpoints()` or checking the option directly with `getOption("imola.breakpoints")$bootstrap`

In out case, we want to target small devices, so we target these via `xs`, and build our grid argument as a named list instead.
```R
# as a gridPanel() argument
areas = list(
  default = c(
    "area1 area1 area1",
    "area2 area3 area3",
    "area2 area3 area3"
  ),
  xs = c(
    "area1",
    "area2",
    "area3"
  )
)
```
NOTE: Imola reserves the special `default` name for values that are used by default, outside of any given breakpoint boundaries (`default` is the rule, breakpoints overwrite `default` for specific screen sizes).

# Templates
Very often during development it is also common that multiple elements share the same layout. In order to easily reuse any layout you create, imola also includes a simple template engine.

In order to save a template you can use the `registerTemplate()` function. This function requires a 'type' of template (grid or flex), a name to identify the template later, any named arguments that can be passed to the gridPanel or flexPanel function (depending on the type) and, optionally, a breakpoint_system to use if you plan on adding any responsive attributes to the template (If no breakpoint_system is given, the current active system is used to register the template).

After a template being registered, you can then simple fill in the `template` argument on any of the panel or page functions with the name of the template. You can also adjust the template for a specific panel by providing any named arguments in addition to the template name. Any named argument that exists in the template will be orewriten by the named argument on the function call.

Lets say we would like to save our previous areas as a template, and use it. We could use registerTemplate():

```R
#in global.R
registerTemplate("grid", "mytemplate",
  areas = list(
    default = c(
      "area1 area1 area1",
      "area2 area3 area3",
      "area2 area3 area3"
    ),
    xs = c(
      "area1",
      "area2",
      "area3"
    )
  )
)
```

We can then use this template to create multiple grid panels in our application:
```R
#in ui.R
gridPanel(
  id = "somePanel"
  template = "mytemplate"
  area1 = div("area 1 content"),
  area2 = div("area 2 content"),
  area3 = div("area 3 content"),
)

gridPanel(
  id = "anotherPanel"
  template = "mytemplate"
  area1 = div("different area 1 content"),
  area2 = div("different area 2 content"),
  area3 = div("different area 3 content"),
)
```

You can register as many templates as you want, but keep in mind that each type + name combo must be unique. You can also remove templates using `unregisterTemplate()` if needed. For a full list of registered templates, you can use `listTemplates()`.

By default imola also includes some ready to use templates these will also be listed under `listTemplates()`
