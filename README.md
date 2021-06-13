# Data analysis template

This template is my personal project structure highly influenced by the awesome [Cookiecutter Data Science](https://drivendata.github.io/cookiecutter-data-science/) project structure with the aim of creating something even more barebones that is more catered towards analysis and less catered towards machine learning projects. The main deliverables are report notebooks which in turn can be exported to html/pdf/png-files etc. 

To try it out for your self just install cookiecutter in your environment and run
```
cookiecutter https://github.com/jepersson/INSERT-REPOSITORY-NAME-HERE
```

## Project folder structure

```
.
├── .env
├── .gitignore
├── README.md
├── Makefile
├── environment.yml
├── matplotlibrc
├── 100-load-datasourceA.ipynb
├── 200-process-datasetA.ipynb
├── 300-visualize-aspectA.ipynb
├── 400-model-modelA.ipynb
├── 500-report-questionA.ipynb
├── eda
│   └── 001-name-explorationA.ipynb
├── data
│   ├── raw
│   │   └── datasourceA.csv
│   ├── interim
│   │   └── datasetA.pkl
│   └── processed
│       └── datasetA.parquet
├── models
│   └── modelA.pkl
└── src
    ├── _init_.py
    ├── helpers.py
    ├── load
    │   └── sourceA.py
    ├── model
    │   └── modelA.py
    ├── process
    │   └── dataset.py
    └── visualize
        └── aspectA.py
```

The project structure is built around the steps of the data analysis process as listed here below:

1. Load
2. (Pre-)Process
3. Explore and Visualize
4. Model and Predict
5. Communicate

This process is described in the numbered notebooks positioned in the project root folder from 1XX to 5XX, one for each step. There can be multiple notebooks in each step in order to keep them short and easier to follow. I that case each notebook should, to the exent possible, contain the code and analysis for one entity relevant for that step in the process (e.g.  a data source, a model, a business question).

In line with the model of the double diamond for data analysis presented [here](https://simplystatistics.org/2018/09/14/divergent-and-convergent-phases-of-data-analysis/) the project structure focus mainly on the convergent phases of analysis, i.e. the steps leading up to problem formulation and conclusions rather than the early explorative steps in the process. 

As a result the `eda` folder has very few recommendations of how it should be structure other than that it should be clear in what order and by whom a notebook was created. The notebooks in here doesn't really have to be reproducible as long as the resulting notebook in the root folder is.

As always with frameworks they should be a guidance and not a rulebook so if it doesn't make sense to follow something written here in any given scenario, just be creative. This text is to let you forget about the majority of times were this structure let's you put things in order without creating a new system every time you do so.


## Supporting Tools

This section describes some of the tools that could be used to support the project structure.

### Encapsulated environment and platform independence

Between the environment.yml, .env, and pathilib tools the goal is to make each
project possible to run on any supported platform (pathlib for paths,
environment for binaries) by any user (env creates one point for inputting user
credentials necessary for a project) while clearly containing info about the
third party code necessary to run it. The environment should be tailored for
the project in order to minimize risk of side effects from unrelated packages
in the executing users environment. 


### Managing exploration and what if analysis

For shorter explorations the eda folder can be used and is kind of smaller off
shoots and the notebooks in the folder can help giving a wider view of the
problem than the more refined notebooks in the root folder.

For longer "what if's analysises that effects the whole analytics pipeline
Using GIT is the recommended tool, utilizing branches to save multiple
branching paths of analyses on the same data without littering the master
branch and braking the first guideline for notebooks.


### Managing file dependencies and execution order with MAKE

Even when notebooks are numbered and named properly there is no easy way to get
an overview of which file is depending on what data and so on. To manage the
dependency graph of a project a tool is borrowed in from software engineering,
MAKE, as described in this [blog post](http://zmjones.com/make/). A make file makes it possible to defined and version control a definition
of an analysis dependency

```
# Makefile dependecy graph example
bash raw.csv: get data.py
python get data.py

clean.csv: clean.sh raw.csv 
source clean.sh

model.Rout: model.R clean.csv. 
R CMD BATCH model.R

plot.Rout: plot.R model.Rout 
R CMD BATCH plot.R
```

Essentially, the job that MAKE does is that it creates a common interface for
outputting analysis results from a project without having to worry about which
notebook to run in what order to get the right files. When using it together
with the other tools presented, conda and pathib, it should enable anyone with
access to the data and code repository to generate the same report as the
members of the project with a make ReportA or similar. It also simplifies your
own work by making sure that every resource affected is regenerated when data
och a notebook early in the pipeline has changed. This without forcing you to
rerun the whole project if not necessary.


## Guidelines

Below follows some guidelines for the notebooks, code, and data contained in the project.

### General notebook guidelines

Guidelines for the notebooks are borrowed from [Netflix](https://netfxtechblog.com/scheduling-notebooks-345e6c14cf6) with just a few omission for more personal freedom:

* Low Branching Factor:   
  Keep your notebooks fairly linear, if you have many conditionals or potential execution paths, it becomes hard to judge if the code is covering the desired use cases well. 

* Library Functions in Libraries:  
  If you do end up with complex functions which you might reuse or refactor independently, these are good candidates for the serc folder rather than in a notebook. 

* Short and Simple is Better:  
  A notebook which generates lots of useful outputs and visuals with a few simple cells is better than a ten page manual. This makes your notebooks more shareable, understandable, and maintainable.

#### General notebook outline

The clean notebooks in the root folder are recommended to the extent possible contain parts as presented in the below outline:

1. Title
2. Background and Purpose
3. Input and Output
4. Imports
5. (Notebook parameters)
6. Implementation
7. (Conclusion)


### General coding guidelines

For code in the project, both the code present in notebooks and in the `src` folder the following guidelines exists:

* As mentioned above notebooks are for giving context and documenting flow. Code are for implementing solutions for the problems described and given context to in the notebooks. 

* Even if the code should be readable and easy to follow there is no need for the code in isolation to show each step of the process. Leave the nitty gritty details in code modules kept in the src folder. 

* Be as transparent as possible. When creating functions try starting with a thin wrapper around the closest applicable function from a basic package in the default stack (numpy, pandas, matplotlib, etc.) and pass through anything not tweaked by your own code to the function being wrapped. Also try to, in the extent possible, to respect input and output format of the wrapped function.


### General data guidelines

* Processed data should be in tidy format to ease sharing code between projects. This means that: 
  1. Every column is a variable.
  2. Every row is an observation. 
  3. Every cell is a single value
  *[For more details see.]
  (https://cranr-project.org/web/packages/tidyr/vignettes/tidy-data.htm)*

* Raw data is immutable and can work as a common point of origin for all analysis happening further down the pipeline without fear of suffering from side effects due to out of order execution. 

* Data is never kept under version control. Instead the project should always containing the full logic going from the sources raw data to finished reports. Should there be any need for sharing data (due to computational restrictions etc.) it can be handled manually (via fileshare, physical media etc.).

