Project 1
================
Brian Sugg
6/12/2020

  - [Introduction to JSON Data](#introduction-to-json-data)
      - [Background](#background)
      - [Reading JSON DATA into R](#reading-json-data-into-r)
          - [Package 1](#package-1)
          - [Package 2](#package-2)
          - [Package 3](#package-3)
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

<img src="moleculeMan.png" width="30%" />

More information on JSON including its history and usage can be found in
the [Wikipedia](https://en.wikipedia.org/wiki/JSON) community. The
following sections will look further into how JSON files can be
unpackaged and parsed into R, including an example connection to an API
for the National Hockey League (NHL).

## Reading JSON DATA into R

Discuss the possible packages/functions that are available for reading
JSON data into R.

### Package 1

Details on available package 1 (to be named).

### Package 2

Details on available package 2 (to be named).

### Package 3

Details on available package 1 (to be named).

## Package for this Vignette

Name which package will be used in the upcoming API call to NHL records,
and why.

# NHL Franchise API

## Background

Provide info on the NHL, available tables from records.nhl.com, which
tables will be explored using the selected function.

## Establishing API Connection

Code chunk for the API and returning parse data from the selected
tables.

## Exploratory Data Analysis

### The Data

Check for any needed formatting, create a new variable as required.

### Numeric Summaries

Provide contingecy tables and numeric summaries as required.

### Visuals

Create some plots (bar, box, scatter) with discussion of observations
for each.
