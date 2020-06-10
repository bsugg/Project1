Project 1
================
Brian Sugg
6/12/2020

  - [Introduction to JSON Data](#introduction-to-json-data)
      - [Background](#background)
      - [Reading JSON DATA into R](#reading-json-data-into-r)
          - [Package 1 - rjson:](#package-1---rjson)
          - [Package 2 - RJSONIO:](#package-2---rjsonio)
          - [Package 3 - jsonlite:](#package-3---jsonlite)
      - [Package for this Vignette](#package-for-this-vignette)
  - [NHL Franchise API](#nhl-franchise-api)
      - [Background](#background-1)
      - [Establishing API Connection](#establishing-api-connection)
      - [Exploratory Data Analysis](#exploratory-data-analysis)
          - [The Data](#the-data)
          - [Numeric Summaries](#numeric-summaries)
          - [Visuals](#visuals)

# Introduction to JSON Data

## Background

JSON stands for JavaScript Object Notation and is simply a method for
writing out data objects using the JavaScript language for quick and
efficient transmission between servers and browsers. JavaScript is a
lightweight programming language with less syntax notation that makes it
easy to read, write, and store data. The lightweight structure of JSON
makes it a great way to quickly and efficiently send large amounts of
data with minimal wait time and storage size.

JSON is commonly used when creating and consuming data via API
connections. It can ingest text strings, numbers, boolean values, `null`
values, arrays, and objects into long strings of text that get delivered
to a client for processing. An example from the [Mozilla
Developers](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/JSON)
webpage helps illustrate how data describing a superhero, “Molecule
Man”, would look in JavaScript format within a JSON file:

`{"name":"Molecule Man","age": 29,"secretIdentity":"Dan
Jukes","powers":["Radiation resistance","Turning tiny","Radiation
blast"]}`

This file can be received and unpackaged by a browser for parsing back
into a consumable format, such as:

<img src="images/moleculeMan.PNG" width="30%" />

More information on JSON including its history and usage can be found in
the [Wikipedia](https://en.wikipedia.org/wiki/JSON) community. The
following sections will look further into how JSON files can be
unpackaged and parsed into R, including an example connection to an API
for the National Hockey League (NHL).

## Reading JSON DATA into R

There are several packages available for reading JSON data into R. Some
of these packages, such as `rjson`, have been around for a few years
providing functionality as a predecessor for future packages to improve
upon. Three major packages will be discussed further below with links to
their respective documentation in the Comprehensive R Archive Network
(CRAN). They include:

  - `rjson`  
  - `RJSONIO`  
  - `jsonlite`

In the spirit of open source development, the evolution of other
packages for converting between JSON and R still continues today. This
is evident in the recent release version of the
[`tidyjson`](https://cran.r-project.org/package=tidyjson) package on
31-May-2020.

### Package 1 - rjson:

The `rjson` package was an early effort in converting data between JSON
and R. Its two primary functions are:

  - `toJSON()` for converting R objects to JSON format  
  - `fromJSON()` for converting from JSON format into R objects

In early release versions, `rjson` was reportedly too slow for
converting large R objects into JSON format. The need for a faster
conversion would eventually lead to the creation of the `RJSONIO`
package.

*[Link](https://cran.r-project.org/package=rjson) to CRAN documentation
for a more exhaustive list of `rjson` functions and their arguments.*

### Package 2 - RJSONIO:

Building upon the functionality of `rjson`, the `RJSONIO` package offers
faster conversions and additional functions for troubleshooting the
parsing process. Both `fromJSON()` and `toJSON()` are enhanced and still
available, and new functions introduced, such as:

  - `isValidJSON()` for validating JSON format prior to parsing, which
    helps when troublehsooting errors  
  - `readJSONStream()` that optimizes system memory and processing for
    the ingestion of streaming JSON content

Overall, `RJSONIO` has additional options for the generation and
processing of JSON content, while sharing a similar look and feel of
`rjson`.

*[Link](https://cran.r-project.org/package=RJSONIO) to CRAN
documentation for a more exhaustive list of `RJSONIO` functions and
their arguments.*

### Package 3 - jsonlite:

Originally a fork of the `RJSONIO` package, the `jsonlite` package is
one of the newer options available for parsing JSON data in R. It has
been designed and optimized for interaction with web APIs. The core
functions of `fromJSON()` and `toJSON()` remain, however, they have been
rewritten for better consistency when converting between JSON and R.
Other functions for `jsonlite` include:

  - `validate()` for validating the structure of JSON content (similar
    to `isValidJSON()` in `RJSONIO`)  
  - `prettify()` adds indentation to JSON strings for better
    readability  
  - `minify()` for removing all indendation and whitespace  
  - `stream_in()` and `stream_out()` for handling streaming JSON
    connections

Overall, `jsonlite` is a fast JSON parser and useful for the conversion
of JSON script into nested lists in R. Given its speed and high
performance, `jsonlite` is a powerful package for anyone interested in
JSON to R conversions, particularly when APIs are involved.

*[Link](https://cran.r-project.org/package=jsonlite) to CRAN
documentation for a more exhaustive list of `jsonlite` functions and
their arguments.*

## Package for this Vignette

Given its optimization for parsing JSON script from APIs, the `jsonlite`
package is used in this vignette when communicating with the API for NHL
records. Its fast speed and ease of use when coupled with the `httr`
package makes it a preferred candidate for converting JSON into R
objects.

In the following “NHL Franchise API” section, the `GET()` and
`content()` functions from `httr` will be used to call a URL and return
a JSON text string. The `fromJSON()` function from `jsonlite` will then
be utilized for parsing the raw text into a R list object. Finally, the
data table from the R list object will be pulled and converted into a
tibble for data analysis.

# NHL Franchise API

## Background

The professional hockey leaague across the United States and Canada is
the National Hockey League (NHL), which provides an API for public
consumption. The NHL offers almost no official documentation for this
connection and its available endpoints. To help navigate this API, open
source documentation from this
[GitLab](https://gitlab.com/dword4/nhlapi/-/blob/master/records-api.md)
will be used to understand what data is available, relevant URL
addresses, and how to filter on certain attributes in the API query.

The following sections will focus primarily on retrieving data for NHL
franchises, players (goalies and skaters), and season stats. Some
numerical summaries will be presented, along with the exploration of
analytical summaries containing visuals of various plots.

## Establishing API Connection

The base URL for the NHL API is `https://records.nhl.com/site/api` which
can be called with different extensions such as `/franchise` to retrieve
data from a specific endpoint, or additionally with a Franchise ID such
as `26` (Carolina Hurricanes) to return data filtered on specific
franchises.

Two functions below have been created for calling the NHL API:

  - `nhlTable(tableName)` calls the provided endpoint from the user,
    parses, and returns a tibble of that endpoint.  
  - `nhlTableWithFranchiseId(tableName,franId)` calls the provided
    endpoint and the desired Franchise ID from the user, parses, and
    returns a tibble of that endpoint filtered on the respective
    franchise.

These functions will be used to call the following endpoints, creating
tibbles for each:

  - `/franchise` to create table `fran`  
  - `/franchise-team-totals` to create table `franTot`  
  - `/franchise-season-records` to create table `franSeason`
      - Filtered on Franchise ID `26` for the Carolina Hurricanes  
  - `/franchise-goalie-records` to create table `franGoal`
      - Filtered on Franchise ID `26` for the Carolina Hurricanes  
  - `/franchise-skater-records` to create table `franSkate`
      - Filtered on Franchise ID `26` for the Carolina Hurricanes

These tables will be used in the upcoming section on “Exploratory Data
Analysis”.

``` r
nhlTable <- function(tableName) {
    baseURL <- "https://records.nhl.com/site/api"
    # GET() used to retrieve information in raw form, content() transforms
    # raw into JSON text for conversion with fromJSON().
    queryGET <- GET(paste0(baseURL, tableName))
    queryText <- content(queryGET, "text")
    queryJSON <- fromJSON(queryText, flatten = TRUE)
    # Reformat data from the returned list into a usable structure, a
    # tibble.
    queryList <- as.list(queryJSON)
    queryTbl <- as_tibble(queryList[[1]])
}
nhlTableWithFranchiseId <- function(tableName, franId) {
    baseURL <- "https://records.nhl.com/site/api"
    # GET() used to retrieve information in raw form, content() transforms
    # raw into JSON text for conversion with fromJSON().
    queryGET <- GET(paste0(baseURL, tableName, "?cayenneExp=franchiseId=", 
        franId))
    queryText <- content(queryGET, "text")
    queryJSON <- fromJSON(queryText, flatten = TRUE)
    # Reformat data from the returned list into a usable structure, a
    # tibble.
    queryList <- as.list(queryJSON)
    queryTbl <- as_tibble(queryList[[1]])
}
# Function calls for 5 different endpoints, with 3 calling
# franchiseId=26 for data specific to the Carolina Hurricanes:
fran <- nhlTable("/franchise")
fran
```

    ## # A tibble: 38 x 6
    ##       id firstSeasonId lastSeasonId mostRecentTeamId teamCommonName
    ##    <int>         <int>        <int>            <int> <chr>         
    ##  1     1      19171918           NA                8 Canadiens     
    ##  2     2      19171918     19171918               41 Wanderers     
    ##  3     3      19171918     19341935               45 Eagles        
    ##  4     4      19191920     19241925               37 Tigers        
    ##  5     5      19171918           NA               10 Maple Leafs   
    ##  6     6      19241925           NA                6 Bruins        
    ##  7     7      19241925     19371938               43 Maroons       
    ##  8     8      19251926     19411942               51 Americans     
    ##  9     9      19251926     19301931               39 Quakers       
    ## 10    10      19261927           NA                3 Rangers       
    ## # ... with 28 more rows, and 1 more variable: teamPlaceName <chr>

``` r
franTot <- nhlTable("/franchise-team-totals")
franTot
```

    ## # A tibble: 104 x 30
    ##       id activeFranchise firstSeasonId franchiseId gameTypeId gamesPlayed
    ##    <int>           <int>         <int>       <int>      <int>       <int>
    ##  1     1               1      19821983          23          2        2937
    ##  2     2               1      19821983          23          3         257
    ##  3     3               1      19721973          22          2        3732
    ##  4     4               1      19721973          22          3         272
    ##  5     5               1      19261927          10          2        6504
    ##  6     6               1      19261927          10          3         515
    ##  7     7               1      19671968          16          3         433
    ##  8     8               1      19671968          16          2        4115
    ##  9     9               1      19671968          17          2        4115
    ## 10    10               1      19671968          17          3         381
    ## # ... with 94 more rows, and 24 more variables: goalsAgainst <int>,
    ## #   goalsFor <int>, homeLosses <int>, homeOvertimeLosses <int>, homeTies <int>,
    ## #   homeWins <int>, lastSeasonId <int>, losses <int>, overtimeLosses <int>,
    ## #   penaltyMinutes <int>, pointPctg <dbl>, points <int>, roadLosses <int>,
    ## #   roadOvertimeLosses <int>, roadTies <int>, roadWins <int>,
    ## #   shootoutLosses <int>, shootoutWins <int>, shutouts <int>, teamId <int>,
    ## #   teamName <chr>, ties <int>, triCode <chr>, wins <int>

``` r
franSeason <- nhlTableWithFranchiseId("/franchise-season-records", 26)
franSeason
```

    ## # A tibble: 1 x 57
    ##      id fewestGoals fewestGoalsAgai~ fewestGoalsAgai~ fewestGoalsSeas~
    ##   <int>       <int>            <int> <chr>            <chr>           
    ## 1    12         171              202 1998-99 (82)     2002-03 (82)    
    ## # ... with 52 more variables: fewestLosses <int>, fewestLossesSeasons <chr>,
    ## #   fewestPoints <int>, fewestPointsSeasons <chr>, fewestTies <int>,
    ## #   fewestTiesSeasons <chr>, fewestWins <int>, fewestWinsSeasons <chr>,
    ## #   franchiseId <int>, franchiseName <chr>, homeLossStreak <int>,
    ## #   homeLossStreakDates <chr>, homePointStreak <int>,
    ## #   homePointStreakDates <chr>, homeWinStreak <int>, homeWinStreakDates <chr>,
    ## #   homeWinlessStreak <int>, homeWinlessStreakDates <chr>, lossStreak <int>,
    ## #   lossStreakDates <chr>, mostGameGoals <int>, mostGameGoalsDates <chr>,
    ## #   mostGoals <int>, mostGoalsAgainst <int>, mostGoalsAgainstSeasons <chr>,
    ## #   mostGoalsSeasons <chr>, mostLosses <int>, mostLossesSeasons <chr>,
    ## #   mostPenaltyMinutes <int>, mostPenaltyMinutesSeasons <chr>,
    ## #   mostPoints <int>, mostPointsSeasons <chr>, mostShutouts <int>,
    ## #   mostShutoutsSeasons <chr>, mostTies <int>, mostTiesSeasons <chr>,
    ## #   mostWins <int>, mostWinsSeasons <chr>, pointStreak <int>,
    ## #   pointStreakDates <chr>, roadLossStreak <int>, roadLossStreakDates <chr>,
    ## #   roadPointStreak <int>, roadPointStreakDates <chr>, roadWinStreak <int>,
    ## #   roadWinStreakDates <chr>, roadWinlessStreak <int>,
    ## #   roadWinlessStreakDates <chr>, winStreak <int>, winStreakDates <chr>,
    ## #   winlessStreak <lgl>, winlessStreakDates <lgl>

``` r
franGoal <- nhlTableWithFranchiseId("/franchise-goalie-records", 26)
franGoal
```

    ## # A tibble: 38 x 29
    ##       id activePlayer firstName franchiseId franchiseName gameTypeId gamesPlayed
    ##    <int> <lgl>        <chr>           <int> <chr>              <int>       <int>
    ##  1   277 FALSE        Cam                26 Carolina Hur~          2         668
    ##  2   310 FALSE        Arturs             26 Carolina Hur~          2         309
    ##  3   336 FALSE        Tom                26 Carolina Hur~          2          34
    ##  4   363 FALSE        Richard            26 Carolina Hur~          2           6
    ##  5   369 FALSE        Sean               26 Carolina Hur~          2         256
    ##  6   411 FALSE        Mark               26 Carolina Hur~          2           3
    ##  7   425 FALSE        John               26 Carolina Hur~          2         122
    ##  8   430 FALSE        Mario              26 Carolina Hur~          2          23
    ##  9   470 FALSE        Pat                26 Carolina Hur~          2           5
    ## 10   490 FALSE        Mike               26 Carolina Hur~          2         252
    ## # ... with 28 more rows, and 22 more variables: lastName <chr>, losses <int>,
    ## #   mostGoalsAgainstDates <chr>, mostGoalsAgainstOneGame <int>,
    ## #   mostSavesDates <chr>, mostSavesOneGame <int>, mostShotsAgainstDates <chr>,
    ## #   mostShotsAgainstOneGame <int>, mostShutoutsOneSeason <int>,
    ## #   mostShutoutsSeasonIds <chr>, mostWinsOneSeason <int>,
    ## #   mostWinsSeasonIds <chr>, overtimeLosses <int>, playerId <int>,
    ## #   positionCode <chr>, rookieGamesPlayed <int>, rookieShutouts <int>,
    ## #   rookieWins <int>, seasons <int>, shutouts <int>, ties <int>, wins <int>

``` r
franSkate <- nhlTableWithFranchiseId("/franchise-skater-records", 26)
franSkate
```

    ## # A tibble: 478 x 30
    ##       id activePlayer assists firstName franchiseId franchiseName gameTypeId
    ##    <int> <lgl>          <int> <chr>           <int> <chr>              <int>
    ##  1 16900 FALSE            793 Ron                26 Carolina Hur~          2
    ##  2 17018 FALSE            294 Kevin              26 Carolina Hur~          2
    ##  3 17055 FALSE            158 Blaine             26 Carolina Hur~          2
    ##  4 17090 FALSE            126 Mike               26 Carolina Hur~          2
    ##  5 17111 FALSE             79 Torrie             26 Carolina Hur~          2
    ##  6 17156 FALSE            211 Pat                26 Carolina Hur~          2
    ##  7 17170 FALSE            147 Mark               26 Carolina Hur~          2
    ##  8 17237 FALSE             11 Thommy             26 Carolina Hur~          2
    ##  9 17239 FALSE              0 Jim                26 Carolina Hur~          2
    ## 10 17257 FALSE             13 Greg               26 Carolina Hur~          2
    ## # ... with 468 more rows, and 23 more variables: gamesPlayed <int>,
    ## #   goals <int>, lastName <chr>, mostAssistsGameDates <chr>,
    ## #   mostAssistsOneGame <int>, mostAssistsOneSeason <int>,
    ## #   mostAssistsSeasonIds <chr>, mostGoalsGameDates <chr>,
    ## #   mostGoalsOneGame <int>, mostGoalsOneSeason <int>, mostGoalsSeasonIds <chr>,
    ## #   mostPenaltyMinutesOneSeason <int>, mostPenaltyMinutesSeasonIds <chr>,
    ## #   mostPointsGameDates <chr>, mostPointsOneGame <int>,
    ## #   mostPointsOneSeason <int>, mostPointsSeasonIds <chr>, penaltyMinutes <int>,
    ## #   playerId <int>, points <int>, positionCode <chr>, rookiePoints <int>,
    ## #   seasons <int>

## Exploratory Data Analysis

### The Data

Check for any needed formatting, create a new variable as required
(think teamName from franchise table).

### Numeric Summaries

Provide contingecy tables and numeric summaries as required.

### Visuals

Create some plots (bar, box, scatter) with discussion of observations
for each.
