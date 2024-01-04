# quaxNat

Estimating the regeneration potential using large scale inventory information.

## Description
Regeneration datasets, especially those pertaining to rare tree species, often exhibit a substantial number of zero values. Due to the unquantifiable nature of the various factors influencing or hindering natural regeneration, a quantile regression approach, following the methodology of Axer et al. (2021), was implemented within the R package quaxNat.This quantile regression method quantifies the impact of the distance to the nearest seed source on natural regeneration densities not considering other influencing variables.
The function quax we provide in that R package is a simple implementation of the quantile estimation. It presents a rather naive approach to quantile regression and, while likely outperformed by more sophisticated procedures such as those in the R package quantreg (Koenker et al., 2023) in general quantile regression problems, appears to work reasonably well for scaled dispersal kernels. 

## Installation
The quaxNat package is available on 
[github](https://github.com/). To install the development version, run the following code:
``` {r installDev, eval = FALSE}
install.packages(c('remotes', 'git2r', 'getPass'))

remotes::install_git("https://github.com/XYZ",
                     credentials = git2r::cred_user_pass(
					   username = getPass::getPass("Git Username"), 
                       password = getPass::getPass("Git Password")), 
                     build_vignettes = TRUE)
```

## Usage
This section illustrates the use of the quax function with step-by-step explanations of the computation.
``` {r installDev, eval = FALSE}

# Load quaxnat package:
library(quaxNat)

# Load and check data
load("/data/quax_data.RData")

```
After loading the quaxNat package, we are now ready to apply the quax function, which we do for the .999th quantile and five dispersal kernels implemented in quaxNat.

```
# Estimate regeneration potential based on various dispersal kernels:
tau <- 0.995
f1 <- quax(oak_regen ~ distance_oak, data, tau=tau, fun=k_t)
f2 <- quax(oak_regen ~ distance_oak, data, tau=tau, fun=k_weibull)
f3 <- quax(oak_regen ~ distance_oak, data, tau=tau, fun=k_lognormal)
f4 <- quax(oak_regen ~ distance_oak, data, tau=tau, fun=k_power)
f5 <- quax(oak_regen ~ distance_oak, data, tau=tau,
           fun=k_exponential_power)
f <- list(`Spatial t`=f1, Weibull=f2, Lognormal=f3, Power=f4,
          `Exp. Power`=f5)

```
The results of the quantile regression for the different dispersal kernels can be visualised and compared.

```
# Plot regeneration density as a function of the distance to
# the nearest seed tree:
plot(oak_regen ~ distance_oak, data, xlim=c(0,1500), cex=.8)

# Pick some colors:
col <- hcl.colors(length(f), palette="Dynamic")

# Add estimated functions to diagram:
for (i in seq_along(f)){
  curve(f[[i]](x), add=TRUE, n=10000, col=col[i], lwd=2)}

# Add legend:
legend("topright", title=paste0(tau," quantile"),
       legend=names(f), lty=1, lwd=2, col=col)

```
The summary function provides a summary of the estimated parameters of the dispersion functions and the goodness of fit.

```
# Compare quality of fits:
sapply(f, summary)

```


.

## Authors and contributors
* [Maximilian Axer](mailto:maximilian.axer@nw.fva.de) _(main author of the package)_
* [Robert Schlicht](mailto:robert.schlicht@tu-dresden.de) _(statistical models)_
* [Robert Nuske](mailto:robert.nuske@nw.fva.de) _(help with package development)_
* [Thorsten Zeppenfeld](mailto:thorsten.zeppenfeld@nw-fva.de) _(climate data)_

## License
For open source projects, say how it is licensed.

## Project status
If you have run out of energy or time for your project, put a note at the top of the README saying that development has slowed down or stopped completely. Someone may choose to fork your project or volunteer to step in as a maintainer or owner, allowing your project to keep going. You can also make an explicit request for maintainers.
