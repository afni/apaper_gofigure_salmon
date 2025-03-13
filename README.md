# apaper_gofigure_salmon
A set of AFNI-based 
([Cox, 1996](https://pubmed.ncbi.nlm.nih.gov/10571921/)) 
processing scripts for the publicly available
salmon FMRI data. This set of data and scripts were used 
in Ex. 4 of the "Go Figure" draft.

The `scripts/` directory contains a full task-based FMRI processing 
command through regression and quality control (QC) using `afni_proc.py` 
([Reynolds et al., 2024](https://doi.org/10.1162/imag_a_00347)). 
They also contain scripts for running 
cluster-based FWE adjustment, via `3dClustSim`, and image-creating 
commands with `@chauffeur_afni`.

The data for running these scripts directly is freely available:  
https://osf.io/n4a37/

The input data is in BIDS-ish format: the directory structure and
naming conventions are BIDS-like for clarity and ease of
deciphering. The scripts don't require a full BIDS validation to
be used, though.

-----------------------------------------------------------------------------

These scripts can be run on any system in which AFNI is
installed. They are written in a way to be generally extensible
to other datasets. They are also structured so that they can be
run on either a desktop or make use of slurm-based workload
managers (e.g., for swarming on a high performance computing
cluster). So, there is more than "just" FMRI processing commands
there, but hopefully this also means they are useful for
developing further processing workflows.

The scripts come in pairs, with a `do_*.tcsh` file that contains
the actual processing commands for one subject and a `run_*.tcsh`
file that manages processing to be able to loop over multiple
subjects and sessions (and swarm, if possible, as well). For most 
processing (esp. at a group level) users would execute the `run_*.tcsh`
script. But it is possible to use the `do_*.tcsh` scripts, as well,
simply providing the necessary command line arguments (here, subject and
session IDs).

Each script is named like: `do_AA_BBBB.tcsh`, where "AA" is a 2-digit code,
simply used to help keep the scripts in order. Numbering need not be 
consecutive. "BBBB" is a short descriptive text label, to let humans know
what is happening, like "ap" if the main work is to run `afni_proc.py`, or
"ssw" is someone is running `sswarper2`, etc.

The processing done in this demo currently:

**do_21_ap.tcsh, run_21_ap.tcsh**  
Run `afni_proc.py` on the unprocessed data. This does full single
subject FMRI processing (here, on task-based data) through regression
modeling. This also generates a useful HTML for quality control, which
can be most usefully viewed interactively via:
    `open_apqc.py -infiles QC_PATH/index.html`
... where `QC_PATH` just stands for the path to the QC directory, which
is located in the results directory from afni_proc.py processing. See
[Reynolds et al., (2023)](https://www.frontiersin.org/articles/10.3389/fnins.2022.1073800/full/) 
and [Taylor et al., (2024)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11382598/) 
for more details.

**do_50_clust.tcsh, run_50_clust.tcsh**  
Run `3dClustSim` to get cluster-based FWE adjustment tables for
clusterizing results. This uses 3dFWHMx to estimate the spatial
relatedness of noise with the mixed autocorrelation function 
(mixed ACF; see [Cox et al., 2017](https://pubmed.ncbi.nlm.nih.gov/28398812/)). 
There is some additional pre-work done
to generate a more filled-in mask that fully spans the input dset (which
is a non-typical salmon dataset here).  
It also runs `@chauffeur_afni` to generate images used in the paper from
a script. This is useful for automatically generating figure images,
systematically creating them. This command even includes 3dClusterize
commands and controls using transparent thresholding and outlining of
the most significant results.
