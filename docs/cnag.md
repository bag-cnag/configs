# nf-core/configs: Centro Nacional de Análisis Genómico (CNAG)


To use, run the pipeline with `-profile cnag`. This will download and launch the [`cnag.config`](../conf/cnag.config) which has been pre-configured with a setup suitable for the CNAG cluster. Using this profile, a docker image containing all of the required software will be downloaded, and converted to a Singularity image before execution of the pipeline.

```bash
# Launch a nf-core pipeline with the cnag profile
$ nextflow run nf-core/<PIPELINE> -profile cnag [...]
```

# Running Nextflow workflow

We suggest launching Nextflow from your project directory using an `sbatch` job with minimal resources. The script below provides and execution example that can be run with `sbatch example_script.sh`

```
#!/bin/bash
#SBATCH --job-name=<JOB_NAME>
#SBATCH --partition=genD
#SBATCH --qos=eternal
#SBATCH --mem 2G
#SBATCH --cpus-per-task=1

## Load Nextflow environment module
module purge
module load Nextflow/${VERSION}

# Set Java memory options to prevent excessive RAM usage
export NXF_OPTS="-Xms500M -Xmx2G"

# Disable ANSI logging for cleaner log files
export NXF_ANSI_LOG=false

nextflow run \
nf-core/<PIPELINE> \
-profile cnag \
-params-file <YOUR-PARAM-FILE>
--outdir results

```

# Resource Management

## Java Virtual Machine Memory

Nextflow runs on the Java Virtual Machine, which can consume excessive memory if not constrained. Always set memory limits:

```bash
export NXF_OPTS="-Xms500M -Xmx2G"
```

- `-Xms500M`: Initial heap size (minimum memory)
- `-Xmx2G`: Maximum heap size (prevents runaway memory usage)

For large pipelines, increase to `-Xmx4G` or `-Xmx8G` and the `mem` argument in the sbatch script respectively.

## Work Directory Management

The work directory (`-w` parameter) stores temporary files and can grow very large.

The `scratch = true` configuration automatically uses node-local storage, reducing network load and overall size of work directory. However, some files remain in the work directory.

For that reason, it is recommended to add the following your submission script after the nextflow command:

```bash
# Capture exit code
EXIT_CODE=$?

# Cleanup on success
if [ ${EXIT_CODE} -eq 0 ]; then
    echo "Pipeline completed successfully at $(date)"
    echo "Cleaning up work directory..."

    # Keep the work directory for 7 days, then delete
    # (allows for resume if needed in the short term)
    echo "rm -rf ${WORK_DIR}" | at now + 7 days

    echo "Work directory scheduled for deletion in 7 days"
else
    echo "Pipeline failed with exit code ${EXIT_CODE} at $(date)"
    echo "Work directory preserved for debugging: ${WORK_DIR}"
fi

echo "Logs available at: logs/nextflow-${SLURM_JOB_ID}.out"
exit ${EXIT_CODE}

```

# Advanced Configuration

## Disabling Scratch for Specific Processes

Some storage-intensive processes may exceed the compute node's local disk capacity. To disable scratch for specific processes, create a custom config:

```groovy
// disable_scratch.config
process {
    withName: 'NFCORE_RNASEQ:RNASEQ:ALIGN_STAR:STAR_ALIGN' {
        scratch = false
    }

    // Can use regex patterns to match multiple processes
    withName: 'NFCORE_RNASEQ:RNASEQ:.*:STAR_.*' {
        scratch = false
    }

    // Or multiple specific processes
    withName: 'PROCESS_A|PROCESS_B|PROCESS_C' {
        scratch = false
    }
}
```

Apply the custom config:
```bash
nextflow run nf-core/<PIPELINE> \
  -profile cnag \
  -c disable_scratch.config \
  [...]
```


## Multiple Custom Configs

Chain multiple configuration files:

```bash
nextflow run nf-core/<PIPELINE> \
  -profile cnag \
  -c custom_genome.config,disable_scratch.config \
  [...]
```



### IGENOMES

A local copy of the iGenomes resource has been made available on CNAG so you should be able to run the pipeline against any reference available in the `igenomes.config` specific to the nf-core pipeline.
You can do this by simply using the `--genome <GENOME_ID>` parameter.


> Note: You will need an account to use the HPC cluster on CNAG in order to run the pipeline. If in doubt contact IT.



> Note: Nextflow will need to submit the jobs via the SLURM job scheduler to the HPC cluster and as such the commands above will have to be executed on one of the login nodes. If in doubt contact IT.

