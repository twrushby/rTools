# Using renv to create reproducible environments for R projects

Reference: [renv for R projects](https://rstudio.github.io/renv/index.html)

## What is renv?

An environment manager for R projects. Meaning that it organises the package dependencies within an R project, recording the versions of each package used in analysis and allowing simply transport of projects from one computer to another.

This is achieved through the creation of package 'snapshots' which can be (re)installed (or 'restored') on different computers with one simple command.

`renv` provides an alternative solution to our [workflow/loadLibraries function](https://git.soton.ac.uk/SERG/workflow/-/blob/master/R/loadLibraries.R) and can tackle package persistence problems when [using RStudio within the University SVE](https://git.soton.ac.uk/SERG/workflow/-/blob/master/howTo/sve.md).

Advantages of using `renv` over `woRkflow::loadLibraries()` is that `renv` automatically scans the code in a project to compile a list of packages used. `renv::snapshot()` also stores information on package versions. Nice!

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

The initialisation command ensures that any time the project is opened, a check is performed to ensure that the `renv` package is installed on the system and that the package is loaded to give access to the `renv::restore()` command (see 'Restore' below).

The use of `renv` is confirmed on opening a project by feedback in the console, for example:

```
* Project 'H:/SVE/git.soton/rtools' loaded. [renv 0.15.2]
```

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

The `renv::restore()` command allows a previous snapshot of packages (including package versions) to be installed.

At [SERG](https://energy.soton.ac.uk) we like to work in a collaborative manner ... this often means sharing analysis and code across platforms. To that end we need packages/libraries used within RStudio in any piece of analysis to be restored easily on another system. 

The `renv` package allows all of the packages used in a specific project to be (re)installed using a single command - very useful for porting projects across different computers/contributors.

## Loading a project on a different machine

If the computer on which you open the intialised project does not have the `renv` package installed, the feedback in the console should indicate that it has been installed and loaded ...

```
# Bootstrapping renv 0.15.2 --------------------------------------------------
* Downloading renv 0.15.2 ... OK (downloaded binary)
* Installing renv 0.15.2 ... Done!
* Successfully installed and loaded renv 0.15.2.
* Project '~/SotonGitLab/Personal/rtools' loaded. [renv 0.15.2]
```

If the version of R differs on the new computer, this will be flagged ...

```
Warning message:
This project is configured to use R version '4.1.1', but '4.0.3' is currently being used. 
* The project library is out of sync with the lockfile.
* Use `renv::restore()` to install packages recorded in the lockfile.
> 
```

Running the `renv::restore()` command will generate a list of packages that will be installed and/or updated. For example, moving from the University's SVE to my own laptop generated the following:

```
> renv::restore()
The following package(s) will be updated:

# CRAN ===============================
- MASS          [7.3-53 -> 7.3-54]
- Matrix        [1.2-18 -> 1.3-4]
- R6            [2.5.0 -> 2.5.1]
- cli           [2.1.0 -> 3.1.1]
- colorspace    [1.4-1 -> 2.0-2]
- crayon        [1.3.4 -> 1.4.2]
- digest        [0.6.27 -> 0.6.29]
- ellipsis      [0.3.1 -> 0.3.2]
- fansi         [0.4.1 -> 1.0.2]
- farver        [2.0.3 -> 2.1.0]
- fastmap       [1.0.1 -> 1.1.0]
- glue          [1.4.2 -> 1.6.1]
- highr         [0.8 -> 0.9]
- htmltools     [0.5.0 -> 0.5.2]
- isoband       [0.2.2 -> 0.2.5]
- jsonlite      [1.7.1 -> 1.7.3]
- knitr         [1.30 -> 1.37]
- lattice       [0.20-41 -> 0.20-44]
- lifecycle     [0.2.0 -> 1.0.1]
- magrittr      [1.5 -> 2.0.2]
- mgcv          [1.8-33 -> 1.8-36]
- nlme          [3.1-149 -> 3.1-152]
- pillar        [1.4.6 -> 1.7.0]
- rlang         [0.4.10 -> 1.0.0]
- rmarkdown     [2.5 -> 2.11]
- stringi       [1.5.3 -> 1.7.6]
- tibble        [3.0.4 -> 3.1.6]
- tinytex       [0.27 -> 0.36]
- utf8          [1.1.4 -> 1.2.2]
- vctrs         [0.3.4 -> 0.3.8]
- viridisLite   [0.3.0 -> 0.4.0]
- withr         [2.3.0 -> 2.4.3]
- xfun          [0.19 -> 0.29]
- yaml          [2.2.1 -> 2.2.2]
- jquerylib     [* -> 0.1.4]

Do you want to proceed? [y/N]:
```

The list shows the packages in the lockfile that would be updated by the restore command. Note the prompt for confirmation to proceed with package updates.



