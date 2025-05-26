# Censored ADMET prediction and QSAR

## Introduction

All assays of drug properties have a region of sensitiviy beyond which, the most that can be said about its value is that, it is above or below a certain threshold. See for example the [Kd values for Imatinib](https://www.ebi.ac.uk/chembl/explore/activities/STATE_ID:3GT55C4FuMMklUm7DQBXGA%3D%3D), many these values (for different targets) can only be recorded as e.g., `>10000 nM`. 

Incorporating those values into a prediction or QSAR model is difficult because the true value is not known, which makes loss functions like the mean square error, or similar, inaccurate.  This problem is also prevalent pyschological settings, where scores are manufactured to lie between [0, 100]. This may not express the true extent of the latent factor and thus you will see accumulations of responses around 0 or 100. It's also prevalent in biomedical settings where the noise and other restrictions inherent to organism level measurements limits the sensitivity of the assays. 

One approach is to extend an ordinal response model, which typically models the outcome as a few set levels (e.g., <=1, 2, >=3), to include 100s of levels which represent a fine grained representation of the outcome (see [Liu et al., 2017](https://onlinelibrary.wiley.com/doi/10.1002/sim.7433)). I have used this in the analysis of a Randomised Control Trial [here](https://journals.sagepub.com/doi/10.1177/1461444821993800).  In practice this means treating the unique values of the response, along with the sensitivity limits, as the edges of intervals which make up the ordinal response.  


## Deep ordinal multitask regression using Spacecutter

I've based my approach for modelling ADMET properties on the [Spacecutter](https://github.com/RobertArbon/spacecutter/tree/mtl) package. I extended the package to include an approximate multi-task objective, along with morgan fingerprints as the features.  

## Polaris competition

I used this Polaris competition `/polaris` (see [here](https://polarishub.io/competitions/asap-discovery/antiviral-drug-discovery-2025)) but was not successful due to a number of factors: 
- poor multitask loss: my simple weighted averaging of the individual losses resulted in uneven training for each endpoint.
- insufficient data - I didn't extend my training set which proved successful for other groups
- poor hyperparameter selection metric: I used the MAE to optimize things like network shape which did not differentiate between many different hypeparameters.  

## Roadmap
1. Use a single task model to model a easy benchmark case. Use this to iron out kinks in model. 
2. Use single task model model on polaris data - follow successful entries' methods of data agumentation. 
3. Better multitask loss based on better weighting of individual losses, or even better, a true multitask loss e.g., based on [mvord](https://cran.r-project.org/web/packages/mvord/vignettes/vignette_mvord.pdf) in R. 
4. Adapt this to a suitable graph model. 

