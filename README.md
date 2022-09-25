# contamMix
Contamination estimation from mitochondrial ancient DNA

This repository contains code to generate a R package ("contamMix") that implements a mixture-model used to estimation contamination in ancient  mitochondrial DNA using MCMC.  The model is detailed in supplementary information 5 ("Likelihood-based method for contamination estimation") of the following paper, which remains the best citation if using this method in a publication:

Fu Q, Mittnik A, Johnson PLF, Bos K, Lari M, Bollongino R, Sun C, Giemsch L, Schmitz R, Burger J, Ronchitelli AM, Martini F, Cremonesi RG, Svoboda J, Bauer P, Caramelli D, Castellano S, Reich D, Pääbo S, and Krause J. (2013). A revised timescale for human evolution based on ancient mitochondrial genomes. Curr Biol, 23:553-9. [doi] 

### Source code context

Code was maintained in a svn repository and shared upon request until Sept 2022, when it was imported into this public GitHub repository.  The R code calls a perl script to pre-process the data (exec/sam2mn.pl) and then calls a C entrypoint to a Gibbs sampler written in C++ (src/contam_gibbs.cpp).  R itself is only used for visualization and to hold the other code together.

### Usage

From the directory above this repository, you can build a R package with the command

	R CMD build contamMix

The resulting R "source" package then needs to be installed into your R installation (note the filename generated by the above command will depends on the version in the DESCRIPTION file):

	R CMD INSTALL contamMix_1.0-10.1.tar.gz

Contamination estimation:
   1) Generate a FASTA-formatted multiple alignment of potential
contaminants + authentic (consensus) sequence.  Two possible programs for
making this alignment are mafft and muscle.

   2) Align your reads to your inferred consensus sequence, perhaps
using bwa.

   3) Run "estimate.R" (included in the contamMix package under the
"exec" subdirectory, so installed wherever the R package was installed). Ex:

	/usr/local/lib/R/site-library/contamMix/exec/estimate.R --samFn data.bam --malnFn aln.fa --figure data_fig

If you run "estimate.R" without any options, it will display a usage
summary.  Under the hood, estimate.R really just calls four functions
from the package: loadSAM, runChains, print.contamMix, and
plot.contamMix. You can also use these functions directly; the package
includes help pages for each.

## Caveats:
The method's key assumption is that contamination is <50%, and thus that the
consensus reflects the authentic sequence.  The consensus sequence
should not have ambiguity characters at positions that are variable in
your data -- contamMix ignores sites with ambiguity characters, so this
would be effectively throwing out your informative sites .  As always
with MCMC, achieving convergence can be fiddly.  I suggest always
graphically checking for convergence in addition to looking at the
Gelman-Rubin diagnostic.  For some datasets, I have had to tweak the
"alpha" hyperparameter (this is an option to estimate.R).
