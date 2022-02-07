# Using renv to create reproducible environments for R projects

Using [renv for R projects](https://rstudio.github.io/renv/index.html)

## What is renv?

An environment manager for R projects. Meaning that it organises the package dependencies within an R project, recording the versions of each package used in analysis and allowing simply transport of projects from one computer to another.

Could be a useful solution to package persistence problems when [using RStudio within the University SVE](https://git.soton.ac.uk/SERG/workflow/-/blob/master/howTo/sve.md)?

Refer to [Introduction to renv](https://rstudio.github.io/renv/articles/renv.html) by Kevin Ushey.

Using `renv` might help to make collaboration that little bit simpler. See [Collaborating with renv](https://rstudio.github.io/renv/articles/collaborating.html)

### Install

Start by installing the [renv](https://rstudio.github.io/renv/) package.

```
install.packages('renv')
```

Open your project and initialise renv to create a project specific local environment and R library.

```
renv::init()
```

If this is the first use of renv, running the init() command will generate output similar to below:

```
Welcome to renv!
It looks like this is your first time using renv. This is a one-time message,
briefly describing some of renv's functionality.

renv maintains a local cache of data on the filesystem, located at:

  - "C:/Users/twr1m15/AppData/Local/R/cache/R/renv"

This path can be customized: please see the documentation in `?renv::paths`.

renv will also write to files within the active project folder, including:

  - A folder 'renv' in the project directory, and
  - A lockfile called 'renv.lock' in the project directory.

In particular, projects using renv will normally use a private, per-project
R library, in which new packages will be installed. This project library is
isolated from other R libraries on your system.

In addition, renv will update files within your project directory, including:

  - .gitignore
  - .Rbuildignore
  - .Rprofile

Please read the introduction vignette with `vignette("renv")` for more information.
You can browse the package documentation online at https://rstudio.github.io/renv/.
```

If the project already has a lockfile the following message will be displayed ...

```
This project already has a lockfile. What would you like to do? 

1: Restore the project from the lockfile.
2: Discard the lockfile and re-initialize the project.
3: Activate the project without snapshotting or installing any packages.
4: Abort project initialization.
```

The initialisation ensures that any time the 

### Lock file

The file `renv.lock` contains a description of the state of the project's library.

For example:

```{
  "R": {
    "Version": "4.1.1",
    "Repositories": [
      {
        "Name": "CRAN",
        "URL": "https://cran.rstudio.com"
      }
    ]
  },
  "Packages": {
    "renv": {
      "Package": "renv",
      "Version": "0.15.2",
      "Source": "Repository",
      "Repository": "CRAN",
      "Hash": "206c4ef8b7ad6fb1060d69aa7b9dfe69",
      "Requirements": []
    }
  }
}
```

## Updating the lock file

When we add some code requiring another package to the repo (in this script), for example ...

```
# install.packages("ggplot2")
library(ggplot2)
```

To create simple plot using built-in dataset `cars` ...

```
ggplot(data = cars, mapping = aes(x = speed, y = dist)) +
  geom_point()
```

The package(s) can then be added to the project library by running another snapshot ...

```{r}
renv::snapshot()
```

Running renv::snapshot() updates the `renv.lock` file (and local library) with the new packages. Feedback is generated, for example (looks at the tonne of dependencies for the ggplot2 package) ...

```
The following package(s) will be updated in the lockfile:

# CRAN ===============================
- MASS           [* -> 7.3-54]
- Matrix         [* -> 1.3-4]
- R6             [* -> 2.5.1]
- RColorBrewer   [* -> 1.1-2]
- base64enc      [* -> 0.1-3]
- cli            [* -> 3.1.1]
- colorspace     [* -> 2.0-2]
- crayon         [* -> 1.4.2]
- digest         [* -> 0.6.29]
- ellipsis       [* -> 0.3.2]
- evaluate       [* -> 0.14]
- fansi          [* -> 1.0.2]
- farver         [* -> 2.1.0]
- fastmap        [* -> 1.1.0]
- ggplot2        [* -> 3.3.5]
- glue           [* -> 1.6.1]
- gtable         [* -> 0.3.0]
- highr          [* -> 0.9]
- htmltools      [* -> 0.5.2]
- isoband        [* -> 0.2.5]
- jquerylib      [* -> 0.1.4]
- jsonlite       [* -> 1.7.3]
- knitr          [* -> 1.37]
- labeling       [* -> 0.4.2]
- lattice        [* -> 0.20-44]
- lifecycle      [* -> 1.0.1]
- magrittr       [* -> 2.0.2]
- mgcv           [* -> 1.8-36]
- munsell        [* -> 0.5.0]
- nlme           [* -> 3.1-152]
- pillar         [* -> 1.7.0]
- pkgconfig      [* -> 2.0.3]
- rlang          [* -> 1.0.0]
- rmarkdown      [* -> 2.11]
- scales         [* -> 1.1.1]
- stringi        [* -> 1.7.6]
- stringr        [* -> 1.4.0]
- tibble         [* -> 3.1.6]
- tinytex        [* -> 0.36]
- utf8           [* -> 1.2.2]
- vctrs          [* -> 0.3.8]
- viridisLite    [* -> 0.4.0]
- withr          [* -> 2.4.3]
- xfun           [* -> 0.29]
- yaml           [* -> 2.2.2]
```
The packages now appear in the contents of the lock file as shown below ...

```
{
  "R": {
    "Version": "4.1.1",
    "Repositories": [
      {
        "Name": "CRAN",
        "URL": "https://cran.rstudio.com"
      }
    ]
  },
  "Packages": {
    "MASS": {
      "Package": "MASS",
      "Version": "7.3-54",
      "Source": "Repository",
      "Repository": "CRAN",
      "Hash": "0e59129db205112e3963904db67fd0dc",
      "Requirements": []
    },
    "Matrix": {
      "Package": "Matrix",
      "Version": "1.3-4",
      "Source": "Repository",
      "Repository": "CRAN",
      "Hash": "4ed05e9c9726267e4a5872e09c04587c",
      "Requirements": [
        "lattice"
      ]
    },
    "R6": {
      "Package": "R6",
      "Version": "2.5.1",
      "Source": "Repository",
      "Repository": "CRAN",
      "Hash": "470851b6d5d0ac559e9d01bb352b4021",
      "Requirements": []
    },
    "RColorBrewer": {
      "Package": "RColorBrewer",
      "Version": "1.1-2",
      "Source": "Repository",
      "Repository": "CRAN",
      "Hash": "e031418365a7f7a766181ab5a41a5716",
      "Requirements": []
    }
    
    XXXX_CURTAILED_FOR_SPACE_XXXX
  }
}
```

## Restore

An easy way to install all packages related to a specific project?

At [SERG](https://energy.soton.ac.uk) we like to work in a collaborative manner ... this often means sharing analysis and code across platforms. To that end we need packages/libraries used within RStudio in any piece of analysis to be restored easily on another system. Luckily renv gives us a useful way to do this with a single command: `restore()` (Nice!).


## Package libraries



```
