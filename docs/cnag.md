# nf-core/configs: Centro Nacional de Análisis Genómico (CNAG)


To use, run the pipeline with `-profile cnag`. This will download and launch the [`cnag.config`](../conf/cnag.config) which has been pre-configured with a setup suitable for the CNAG cluster. Using this profile, a docker image containing all of the required software will be downloaded, and converted to a Singularity image before execution of the pipeline.

```bash
# Launch a nf-core pipeline with the crg profile
$ nextflow run nf-core/<PIPELINE> -profile cnag [...]
```

# Running Nextflow workflow

We suggest launching Nextflow from your project directory using an `sbatch` job with minimal resources. The script below provides and execution example that can be run with `sbatch example_script.sh`

```
#!/bin/bash
#SBATCH -J job_name
#SBATCH --partition=genD
#SBATCH --qos=eternal
#SBATCH --mem 2G
#SBATCH -c 1

## Load Nextflow environment module
module purge
module load Nextflow/${VERSION}

export NXF_ANSI_LOG=false

nextflow run \
nf-core/<PIPELINE> \
-profile cnag \
-w /work

```

# Managing RAM allocation

Nextflow runtime runs on top of Java virtual machine which, by design, tries to allocate as much memory as is available. In big pipelines, this can be a problem. To avoid excessive RAM allocation, specify the maximum amount of memory that can be used by the Java VM using the -Xms and -Xmx Java flags like the above example.

```
export NXF_OPTS="-Xms500M -Xmx2G"
```




### IGENOMES

A local copy of the iGenomes resource has been made available on PROFILE CLUSTER so you should be able to run the pipeline against any reference available in the `igenomes.config` specific to the nf-core pipeline.
You can do this by simply using the `--genome <GENOME_ID>` parameter.


> Note: You will need an account to use the HPC cluster on CNAG in order to run the pipeline. If in doubt contact IT.



> Note: Nextflow will need to submit the jobs via the SLURM job scheduler to the HPC cluster and as such the commands above will have to be executed on one of the login nodes. If in doubt contact IT.

