# landscapeR
A landscape simulator for R. 
This package is aimed at simulating categorical landscapes on actual geographical realms, starting from either empty landscapes or landscapes provided by the user (e.g. land use maps). The purpose is to provide a tool to tweak or create the landscape while retaining a high degree of control on its features, without the hassle of specifying each location attribute. In this it differs from other tools which generate null or neutral landscape in a theorethical space. All basic GIS operations are handled by the [raster](https://cran.r-project.org/web/packages/raster/index.html) package.

To install, open a R session and select 'landscapeR' from the packages list, or type in the console:
```r
install.packages("landscapeR")
```

Alternatively to install from source:
- download the source file [landscapeR_1.0.0.tar.gz](https://cran.r-project.org/src/contrib/landscapeR_1.0.0.tar.gz) to the R working directory (or any other directory)
- start an R session
- run the following commands in the console:
``` r
## Install the required packages 
install.packages("raster", repos="http://cran.uk.r-project.org/", dependencies=T, clean=T)
install.packages("fastmatch", repos="http://cran.uk.r-project.org/", dependencies=T, clean=T)

## Install landscapeR (full path to the file, if not in the R working directory)
install.packages("~/landscapeR_1.0.0.tar.gz", repos = NULL, type="source")

## Load package
library(landscapeR)
```

Here it follows a set of examples, using landscapeR functions to generate various landscape configurations. 
Similar examples are showed in the [vignette](http://htmlpreview.github.com/?https://github.com/dariomasante/landscapeR/blob/master/landscapeR.html).
Let's start loading the required packages and making an empty landscape (by transforming a matrix into a geographical obkect):
```{r, message=FALSE, warning=FALSE}
library(landscapeR)

## Create an empty landscape
library(raster)
m = matrix(0, 33, 33)
r = raster(m, xmn=0, xmx=10, ymn=0, ymx=10)
```

### `makePatch`
This is the basic function to create a single patch. For instance:
```{r, eval=FALSE}
rr = makePatch(r, size=500, rast=TRUE)
plot(rr)
```

Some more features can be specified about the patch. For example, the following will create a patch with value 3, starting from the centre cell of the raster:
```{r}
patchSize = 500
newVal = 3
centre = 545
rr = makePatch(r, patchSize, centre, val=newVal, rast=TRUE)
plot(rr)
```

Forbidden cells can be specified by value, so the patch will occupy only the allowed background. The following will generate a new patch with value 5 and size 100 inside the existing patch:
```{r, warning=FALSE}
rr = makePatch(rr, 100, bgr=newVal, rast=TRUE, val=5)
plot(rr)
```

### `makeClass`
`makeClass` generates a group of patches, as specified by its arguments. Example:
```{r, warning=FALSE}
num = 5
size = 15
rr = makeClass(r, num, size)
plot(rr)
```

Patches are allowed to be contiguous, so they may appear as a single patch in those instances:
```{r, warning=FALSE}
num = 75
size = 10
rr = makeClass(r, num, size)
plot(rr)
```

Each patch size and seed starting position can be specified as well:
```{r}
num = 5
size = c(1,5,10,20,50)
pts = c(1, 33, 1089, 1057, 545)
rr = makeClass(r, num, size, pts)
plot(rr)
```

### `expandClass`
Expand (and shrinks) classes starting from an existing landscape. Building on the previous:
```{r}
rr = expandClass(rr, 1, 250)
plot(rr)
```

This function can be used to mimic shapes, by providing a skeleton:
```{r}
m[,17] = 1
r = raster(m, xmn=0, xmx=10, ymn=0, ymx=10)
plot(r)
rr = expandClass(r, 1, 200)
plot(rr)
```

