
<!-- README.md is generated from README.Rmd. Please edit that file -->

# selenium

<!-- badges: start -->

[![R-CMD-check](https://github.com/ashbythorpe/selenium-r/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/ashbythorpe/selenium-r/actions/workflows/R-CMD-check.yaml)
<!-- badges: end -->

selenium is a tool for the automation of web browsers. It is a low-level
interface to the [WebDriver](https://w3c.github.io/webdriver/)
specification, and an up-to-date alternative to
[RSelenium](https://github.com/ropensci/RSelenium).

## Installation

You can install the development version of selenium from GitHub:

``` r
# install.packages("pak")
pak::pak("ashbythorpe/selenium-r")
```

However, you must also have a selenium server installed and running (see
below).

## Starting the server

A selenium instance consists of two parts: the client and the server.
The selenium package *only provides the client*. This means that you
have to start the server yourself.

To do this you must:

- Install a browser that you want to automate (e.g. Chrome, Firefox,
  Edge).
- Download [Java](https://www.java.com/).

There are many different ways to download and start the server, one of
which is provided by selenium:

``` r
library(selenium)
```

``` r
server <- selenium_server()
```

This will download the latest version of the server and start it. You
can also do this manually if you want:

1.  Download the latest `.jar` file for Selenium Server. Do this by
    navigating to the latest GitHub release page
    (<https://github.com/SeleniumHQ/selenium/releases/latest/>),
    scrolling down to the **Assets** section, and downloading the file
    named `selenium-server-standalone-<VERSION>.jar` (with `<VERSION>`
    being the latest release version).
2.  Make sure you are in the same directory as the file you downloaded.
3.  In the terminal, run
    `java -jar selenium-server-standalone-<VERSION>.jar standalone --selenium-manager true`,
    replacing `<VERSION>` with the version number that you downloaded.
    This will download any drivers you need to communicate with the
    server and the browser, and start the server.

There are a few other ways of starting Selenium Server:

- Using docker to start the server. See
  <https://github.com/SeleniumHQ/docker-selenium>. This is recommended
  in a non-interactive context (e.g. GitHub Actions).
- Using the `wdman` package to start the server from R, using
  `wdman::selenium()`. Note that at the time of writing, this package
  does not work with the latest version of Chrome.

## Starting the client

Once a server instance has started, move to R and load selenium.

``` r
library(selenium)
```

Client sessions can be started using `SeleniumSession$new()`

``` r
session <- SeleniumSession$new()
```

By default, this will connect to Firefox, but you can use the `browser`
argument to specify a different browser if you like.

``` r
session <- SeleniumSession$new()
```

Here, we use the `capabilities` argument to specify options for the
browser. Here, the `remote-debugging-port` argument to Chrome is used to
make sure the port that the browser uses does not conflict with any
others (and may be necessary if Chrome is not working by default).

``` r
session <- SeleniumSession$new(
  browser = "chrome",
  capabilities = list(
    `goog:chromeOptions` = list(
      args = list("remote-debugging-port=9222")
    )
  )
)
```

If this doesn’t work, please see the [Debugging
Selenium](https://ashbythorpe.github.io/selenium-r/articles/debugging.html)
article for more information.

<!-- TODO: Make this vignette --->

## Usage

Once the session has been successfully started, you can use the session
object to control the browser. Here, we dynamically navigate through the
R project homepage.

``` r
session$navigate("https://www.r-project.org/")
session$
  find_element(using = "css selector", value = ".row")$
  find_element(using = "css selector", value = "ul")$
  find_element(using = "css selector", value = "a")$
  click()

session$
  find_element(using = "css selector", value = ".row")$
  find_elements(using = "css selector", value = "div")[[2]]$
  find_element(using = "css selector", value = "p")$
  get_text()
#> [1] ""

session$close()
```

For a more detailed introduction to using selenium, see the [Getting
Started](https://ashbythorpe.github.io/selenium-r/articles/selenium.html)
article. <!-- TODO: Make this vignette --->

Note that selenium is low-level and mainly aimed towards developers. If
you are wanting to use browser automation for web scraping or testing,
you may want to take a look at
[selenider](https://github.com/ashbythorpe/selenider) instead.
