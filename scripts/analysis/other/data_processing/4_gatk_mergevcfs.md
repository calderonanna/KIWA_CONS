## gatk MergeVCFs
```bash
nano $scripts/gatk_MergeVCFs.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=3GB
#SBATCH --time=2:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=gatk_MergeVCFs
#SBATCH --output=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
allsites="/storage/group/zps5164/default/abc6435/KIWA_CONS/data/gatk/allsites_vcf"
vcfs=$(realpath $allsites/*.vcf.gz | sed 's/^/-I /')

#MergeVcfs
gatk MergeVcfs \
    $vcfs \
    -O $allsites/cKIWA_allsites.vcf.gz
```
