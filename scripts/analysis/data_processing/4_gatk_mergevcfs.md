## gatk MergeVCFs
```bash
nano $scripts/gatk_MergeVCFs.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=3GB
#SBATCH --time=2:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=gatk_MergeVCFs
#SBATCH --output=~/SzpiechLab/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=~/SzpiechLab/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set Variables
scripts="~/SzpiechLab/abc6435/KIWA_CONS/scripts"
allsites="~/SzpiechLab/abc6435/KIWA_CONS/data/gatk/allsites_vcf"
vcfs=$(realpath $allsites/*.vcf.gz | sed 's/^/-I /')

#MergeVcfs
gatk MergeVcfs \
    $vcfs \
    -O $allsites/cKIWA_allsites.vcf.gz
```