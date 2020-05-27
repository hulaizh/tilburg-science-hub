% Key building blocks of efficient workflows
% [Hannes Datta](https://hannesdatta.com)
% May 29, 2020

# Overview

## Goals of this module

*Understand the [key building blocks of efficient workflows](http://tilburgsciencehub.com/workflow)*

- Think of your project as a pipeline
- Learn about the key components of a pipeline
- Discuss directory structures
- Automate your pipeline

<!--- Gradual implementation-->

# Pipelines

## Definition

*pipeline* {n.}: refers to the (usually automized) __steps necessary to build a project__; *workflow* used interchangeably

> - Examples: steps necessary to prepare data, run model, produce tables and figures

::: notes

- __What steps does your project consist of?__
- __Which steps did you already automize?__

- STAGES are the broader steps

:::

## Example: Academic paper

    - Stage 1: Prepare dataset for analysis
        - Step 1a: download raw data
        - Step 1b: clean data
        - Step 1c: aggregate data
    - Stage 2: Run model on a dataset
        - Step 2a: various variable configurations
        - Step 2b: select best fitting model
    - Stage 3: Produce tables and figures for the paper
        - Step 3a: load result of best-fitting model
        - Step 3b: produce output table

## Drafting a pipeline

![Directed acyclic graph (DAG)](./images/make_flowchart.png)

. . .

- white boxes (inputs or outputs; concurrently!)
- red boxes (transformation by rules)

## Benefits of pipelines

- write clearer source code: smaller code chunks
- obtain results faster: automation (redo); change inputs & check outputs
- increase transparency and collaboration: work in different parts of pipeline
- software stack: full flexibility

# Components of a pipeline

## Definition

*pipeline components* {n}: refer to a project's building blocks, consisting of source code, data, generated files, and notes.

## (1) Source code

inputs [data, arguments] --> transformation --> output [datasets, log files, images]

. . .

- Examples:
    - load data from web, save locally as CSV
    - clustering: argument (set *k* for k-means); load matrix of tag words, cluster with *k*, save IDs & cluster IDs to csv
    - load results, compute fit statistics, output to .txt file
    - ...

::: notes

- responsible to **transform inputs into outputs**

:::

## (2) Raw data

- Data *stored remotely*
    - File-based systems (e.g., S3, FTP, Dropbox, Drive)
    - Databases (e.g., MongoDB, MySQL)
    - Get'em all: use our get-data tool!

- Those needed are downloaded *locally* (!)

- [Documented with `readme.txt`](http://tilburgsciencehub.com/workflow/documenting-data)!

## (3) Generated files

- These are the *outputs* of your source code.
- Think about cleaned data sets, results of analysis, etc.
- If *final* --> "output"
- If *temporary* --> "temp"
- If used for auditing --> "audit"

## (4) Notes

- Documentation, literature, PDFs, ...
- Keep in separate folder, with potentially subfolders

## Exploration

- Let's explore those components in practice...
- E.g., view [structure of my Spotify paper](http://tilburgsciencehub.com/workflow/structure_spotify_2018.html)

__What's different from what I just told you?__

::: notes

1. Describe the directory structure – where did I store what?
2. What’s the use of the main directories in the project? (raw, derived, analysis) – why do I do it that way?
3. What’s that server directory doing?!

"project" centric - "wipeable"

"submodule centric" - self-contained, highly portable

:::

## Conceptual framework

![](images/workflow.png){ width=800px }

::: notes

- pipeline = logical steps in building project
- components = nuclear units

- why?
  - full portability across colleagues, computers
  - reproducibility and transparency (nothing manually edited)

:::

# Data Management and Directory Structure

## Guiding principles

- Others should understand pipeline, merely by looking at file/directory structure
- Each step in pipeline is self-contained ("portability")
- Project is versioned & backed-up (more later)

## Let's share your experience and firm practices

- How __do you__ keep files/directories tidy?
- How __do you__ ensure portability?
- How __do you__ version & back-up your project?

::: notes

-	Be prepared to show the directory of a recent project you’re working on – be able to explain that structure (you don’t need to revise the structure before class!)

:::

## How to store raw data

- Suggestions are [here](http://tilburgsciencehub.com/workflow/directories/)
    - CSV is more stable than XLSX, SAV, ...
    - Databases will shut down, so take copies

- Transparent directory structure with [`readme.txt`](http://tilburgsciencehub.com/workflow/documenting-data) in each folder
  ```
  \data\website_A\...
  \data\company_B\...
  \data\data_provider_C\2019-11-04\...
  \data\data_provider_C\2020-03-01\...
  ```

## Project directory structure (1)

- Three main folders: `src`, `gen`, and `data`
- Subfolders represent pipeline stages (e.g., `data-prep`, `analysis`, `paper`)

```
\src\data-prep       <- SOURCE CODE
\src\analysis
\src\paper

\gen\data-prep\      <- GENERATED FILES
\gen\analysis\
\gen\paper\

\data\website_A\...  <- RAW DATA
\data\company_B\...
```

## Project directory structure (2)

- Keep generated files separate: `input`, `output`, `temp`, `audit` [notes](http://tilburgsciencehub.com/workflow/directories/#summary), [download template](http://tilburgsciencehub.com/workflow/dir_structure.zip))

```
\src\data-prep       <- SOURCE CODE
\src\analysis
\src\paper
\gen\data-prep\input <- GENERATED FILES
\gen\data-prep\output
\gen\data-prep\temp
\gen\data-prep\audit
\gen\analysis\...
\gen\paper\...
\data\website_A\...  <- RAW DATA
\data\company_B\...
```

## Enabling portability (1)

- Output of one pipeline stage (e.g., `\gen\data-prep\output`) can be input to
another pipeline stage (e.g., `\gen\analysis\input`)

## Enabling portability (2)

- Potential solution *violating portability*
    - Code in `\src\analysis` reads directly from
`\gen\data-prep\output`
    - Fine if you work on your own

## Enabling portability (3)

- Better solution *ensuring portability*
    - Code in `\src\data-prep` moves `\gen\data-prep\output` to file exchange (e.g., S3, local server)
    - Code in `\src\analysis` downloads data from file exchange, puts it into `\gen\analysis\input`
    - Much better when working with others

# Automation

## Build tools

- Build tools introduce "recipes" of how to make stuff
    - *target* __what__ needs to be built (e.g., a file)
    - *source(s)* what is __required__ to execute the build?
    - *execution* how is the build to be __executed/run__?

## Use cases

- Build only what *needs* to be built
- Quickly move code to another computer (e.g., the cloud)
- "Loop" over different model specifications
- Nightly builds

## Example recipes

Let's generate some example recipes by writing __pseudo code__...

```
target: source(s)
    execution command
```

## Make

- Installed on Mac, Linux; installable on Windows
- Documentation [here](http://tilburgsciencehub.com/workflow/automation/)
    - `make` runs entire workflow
    - `make -n` does a dry-run

## Relative paths

- Use relative paths
    - NOT absolute: `c:\projects\jads\data\myfile.csv`
    - Reference is `src` directory
    - Exercise
      - Open terminal
      - Navigate directory with `cd` and `dir` (Win) or `ls` (Mac)

## What to automate?

- Automate everything that *can* be automated

# Documentation

## Documenting code and pipeline

- include one for main project ([template here](http://tilburgsciencehub.com/workflow/documenting-code/))
- include one for each stage of your pipeline
    - `makefile` is a good substitute for that documentation

## Documenting (raw) data

- Import for newly generated raw data *and* any generated output data
- Let's go through sections of [this template](http://tilburgsciencehub.com/workflow/documenting-data/)

# Versioning and collaboration on GitHub

## Versioning the old way

- Use dates in file names, initials for authors

```
cleandata_022113.do
cleandata_022613.do
regressions.log
cleandata_022113a.do
cleandata_022613_jms.do
regressions_022413.do
regressions_022713_mg.do
regressions_022413.log
```

::: notes


- Good
    - Facilitates comparison
    - Facilitates undoing, rolling back
- Bad
    - Which log file comes from which regression?
    - Which version of `cleandata` was used to make data for `regressions`?
    - No software firm does it like that!

:::

## Versioning the new way

- Make use of software tools like Git(Hub)
    - Git is locally installed, open-source
    - GitHub is Git in the cloud; many other providers available

- Git saves *changes* rather than snapshots
- Git allows you to work *in parallel*, with others


## Git workcycle (1)

![Git workcycle](./images/git_workflow.png)

## Git workcycle (2)

- Several times a day
  - `git status`: What has changed?
  - `git add`: staging
  - `git commit -m "message"`: commit ("save") + note to your future self

- Sync at the end of the day
  - `git push`: push to server (e.g., GitHub)
  - `git pull`: get updates from server

- [More on TSH](http://tilburgsciencehub.com/workflow/versioning)
- [Wonderful cheatsheet](https://education.github.com/git-cheat-sheet-education.pdf)


## Collaboration

- Find a project to collaborate on
- Fork repository
  - Create branch for each feature
  - Perform a workcycle
- Create pull request
  - Owner of main repository will review, then integrate changes

## Project management

- Issues
- Project board
- Milestones

# Housekeeping

## Programming 101

- Have clear variable names
- Use loops where possible
    - e.g., variable operationalization
    - e.g., estimation on different slices of the data
- Modularization of functions
    - e.g., make your own module for commonly used functions
    - e.g., modularize functions itself
- Use of assrts
    - e.g., `stopifnot()` in R, `assert` in Python

## Tidying up

- Delete what can be deleted
- Tests for portability
    - locally: restart PC, rerun
    - locally: wipe `gen`, rerun
    - other computer: run `make`
    - other computer, different OS: run `make`
    - fix, fix, fix


    ::: note

    On own computer
    Wipe all temp and output files
    Restart your PC
    Run makefile

    Does it reproduce the results?

    On other computer (I use a virtual PC in the cloud)
    Install software as instructed in readme
    Run makefile

    Does it reproduce the results?


    :::

## Checklist

We've compiled a [housekeeping checklist here](http://tilburgsciencehub.com/workflow/checklist/)