# nf-core/configs: Centro Nacional de Análisis Genómico (CNAG)

All nf-core pipelines have been successfully configured for use on the HPC cluster at [Centro Nacional de Análisis Genómico (CNAG)](https://www.cnag.eu/).

To use, run the pipeline with `-profile cnag`. This will download and launch the [`cnag.config`](../conf/cnag.config) which has been pre-configured with a setup suitable for the CNAG cluster. Using this profile, a docker image containing all of the required software will be downloaded, and converted to a Singularity image before execution of the pipeline.

```bash
# Launch a nf-core pipeline with the crg profile
$ nextflow run nf-core/<PIPELINE> -profile cnag [...]
```

We suggest launching Nextflow from your project directory using an `sbatch` job with 1 CPU and 2–5 GB memory. You may also specify the job duration depending on your workflow requirements.

```bash
#!/bin/bash
#SBATCH --partition=general
#SBATCH --mem 2GB
#SBATCH -t 2:00:00
#SBATCH -c 1
```


Within the same job script, load Nextflow via the CNAG environment module system:

```bash
## Load Nextflow environment module
module purge
module load Nextflow/${VERSION}
```

For more information about running nextflow on HPC clusters check these highly informative blog posts
- [5 Nextflow Tips for HPC Users](https://seqera.io/blog/5_tips_for_hpc_users/)
- [Five more tips for Nextflow user on HPC](https://seqera.io/blog/5-more-tips-for-nextflow-user-on-hpc/)


After a sucessful run, please, consider deleting the working directory to avoid file duplication.




### IGENOMES

A local copy of the iGenomes resource has been made available on PROFILE CLUSTER so you should be able to run the pipeline against any reference available in the `igenomes.config` specific to the nf-core pipeline.
You can do this by simply using the `--genome <GENOME_ID>` parameter.


> Note: You will need an account to use the HPC cluster on CNAG in order to run the pipeline. If in doubt contact IT.



> Note: Nextflow will need to submit the jobs via the SLURM job scheduler to the HPC cluster and as such the commands above will have to be executed on one of the login nodes. If in doubt contact IT.

