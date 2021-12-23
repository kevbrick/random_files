# nf-core SSDS pipeline on Biowulf
These are general instructions how to run the SSDS pipeline on biowulf (using the nf-core version):

## Before first use:
### Create singularity cache directory (on first use):
The pipeline uses singularity. By default the singularity cache folder is set to the /home/$USER space, however on biowulf, this space is limited. Here, we make sure that sthe singularity cache is store in /data/$USER instead. 
```
mkdir -p /data/$USER/singularity/cache
echo 'export SINGULARITY_CACHEDIR="/data/'$USER'/singularity/cache"' >~/.bashrc
source ~/.bashrc
```

### Get pipeline (on first use / can also be used on occasion for updates):
The pipeline is placed in ```/home/$USER/.nextflow/21.10.5/``` by default.
Since space on /home/$USER is limited, you may wish to move this folder to /data/$USER. To do so:
```
module load nextflow/21.10.5

if [ -d "/home/$USER/.nextflow" ]; then 
    mv ~/.nextflow /data/$USER/.nextflow
}else{
    mkdir -p /data/$USER/.nextflow
}
ln -s /data/$USER/.nextflow ~/.nextflow

nextflow pull nf-core/ssds
```

### Set up genomes (on first use / or after an update):
The paths to genome files used by the pipeline are stored in :
```
/home/$USER/.nextflow/21.10.5/assets/nf-core/ssds/conf/igenomes.config
```
Unfortunately, the biowulf genomes structure is different from that expected by nf-core, so we need to overwrite this file with a customized version. 

This version contains only the mouse and human genomes:
```
curl https://raw.githubusercontent.com/kevbrick/random_files/main/igenomes_biowulf.config >/home/$USER/.nextflow/21.10.5/assets/nf-core/ssds/conf/igenomes.config
```

This version should be used by RDCO lab members:
```
curl https://raw.githubusercontent.com/kevbrick/random_files/main/igenomes_RDCO.config >/home/$USER/.nextflow/21.10.5/assets/nf-core/ssds/conf/igenomes.config
```

## Running from biowulf: 
### Set up environment:
The pipeline requires nextflow v21.10.5+, so it is important to specify the version. 
```
module load nextflow/21.10.5
module load singularity
```
### Tests:
#### Quick test
```
nextflow run nf-core/ssds -r dev -profile nihbiowulf,test
```
#### Full test
```
nextflow run nf-core/ssds -r dev -profile nihbiowulf,test_full,slurm
```
<hr>

### Samplesheet
Make a sample-sheet for your experiment (see: https://nf-co.re/ssds/dev/usage).<br>
**NOTE:** The FULL PATH of each file must be provided in the sample sheet.
### Execution:
```
nextflow run nf-core/ssds -r dev -profile nihbiowulf,slurm --input samplesheet.csv --outdir results --genome mm10
```
### sbatch example
Once the pipeline is set up, a typical sbatch script would look like:
```
#!/bin/bash
module load nextflow/21.10.5
module load singularity
nextflow run nf-core/ssds -r dev -profile nihbiowulf,slurm --input samplesheet.csv --outdir results --genome mm10
```