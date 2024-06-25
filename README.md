# Pipeline for the creation of minimal metabolic networks from
# Genome-Scale models of metabolism

This pipeline requires the presence of a running version of Gurobi optimizer accesible from MATLAB
(i.e. MATLAB should be able to successfully call the function gurobi()).

This pipeline has been tested on MATLAB R2020a and Gurobi verion 8 and 9).

Copyright: ...

License: ...

## How to use

The pipeline is launched via the script present in the folder (scriptRunsingle.m)
The optimization is launched and continues through a set number of generations (see parameters), while trying to reduce the size of the metabolic networks simulating an increasing number of genes KOs.

### Parameters

- ***restart***: to be set as true only if the optimization could not be completed and you want to start from the last generation obtained (default: false);
- ***pop***: the size of the population considered for each generations,
    i.e. the number of concurrent strains considered and to be minimised by the algorithm (default: 100);
- ***gen***: number of generations for which the pipeline is run. It should be at least equal to 2-fold the number of potential genes KOs (default: 5000);
- ***ncores***: number of cores to be used by the Parallel Computing Toolbox of MATLAB (recommended). If not available, set equal to 0 (default: 4);
- ***model***: mat filename of the FBA model to be used (located in the 'model' folder)
- ***optMode***: the optimization used (FBA only);
- ***flagEssGenes***: set as true if the model contains the field 'essentialGenes' and you want to exclude these genes from the minimization (default: true);
- ***flagEssCouples***: true if the model contains the field 'essentialGenes' and you want to avoid the KOs of an essential couple of genes during the minimization (default: false);
- ***flagCOBRA***: true if the model is in COBRA format; the pipeline use models with renamed fields: lb as vmin, ub as vmax, c as f and rules as OptPts; they will be automatically renamed if needed (default: false);
- ***maxred***: the maximum allowed reduction in the simulated Growth Rate after a gene KO, otherwise the KO is not accepted (default: 1%);

### Post Process
During the optimization, the pipeline save some files in a folder named 'solutions*NAME OF THE MODEL*'
When the optimization ends (i.e. it reaches the maximum number of generations), a post processing procedure is launched to summarize the results and save new useful files.

It basically extract the minimal metabolic networks obtained and save them in a single file.
Informations on fluxes for these minimal networks, the most important genes among them and a few other files are saved in the output folder.
It also deletes the file for the single generations created during the optimization.
The post processing could be time and resource demanding if a lot of different networks are obtained during the optimization.

## Output
At the end of the post process in the output folder ('solutions*NAME OF THE MODEL*') will be saved these files

- ***fbamodel***: the model as it was used;
- ***GDMOobj***: the struct defined as class used during the optimization (useful also for restarting an optimization);
- ***GenesAnTabs***: three or four tables summarizing the post processing results on genes:
    - tab contains the genes always present in the minimal networks _(NED)_;
	- tab2 contains the genes present in some fo the minimal networks and their frequencies;
    - tab3 contains the genes that are never presenti in the minimal networks;
    - tab4 contains the essential genes (if present and if flagEssGenes was set to true.
- ***matrReac***: a n x n triangular matrix, where n is the number of minimal metabolic networks obtained. Each element is the fraction of shared active reactions bewteen two different minimal networks.
- ***minimalSolutions***: the processed minimal metabolic networks saved as a binary matrix. Each row is a different network, while each column is for a gene in the model; a value equal to 1 represents a KO and a 0 a gene kept in the minimal metabolic network.
The last two columns are for the predicted GR and the total number of KO in each strain;
- ***minimalSolutionsOld***: same as minimal Solutions, but before extracting unique results;
- ***parsMatrReac***: same as matrReac, but using the _parsimonious FBA_ for more stable results;
- ***solutions***: a heavy file containing all the populations obtained during the optimization at each generation (formatted as minimalSolutions);
- ***totalChromosome***: all the networks considered during the optimization combined in a single table ;
- ***totalFront***: the non-dominated networks (in terms of maximizing the GR and minimizing the number of active genes) constituting the _Pareto-front_;
- ***vMinSol***: a matrix containing all the reactions fluxes for each minimal network as predicted by FBA (minimal solutions x reactions in the model)
- ***vMinSolPars***: same as vMinSol, but using parsimonious FBA for predictions.
