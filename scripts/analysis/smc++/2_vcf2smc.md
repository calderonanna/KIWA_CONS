# vcf2smc
vcf2smc can only be run on a single contig or chromosome. Also the default behavior is to set the first sample as the "distinguished lineage" and the others as "undistinguished". The distinguished individual provides the lineage that smc explicity models along the genome whiel the remaining samples contribute to allele frequency. Which invidual is chosen as the distinguished lineag has a small but statistically negligible effect on the results (see https://github.com/popgenmethods/smcpp/issues/18). 

## virens
```bash
nano $scripts/vcf2smc_virens.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=20GB
#SBATCH --time=30:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=vcf2smc_virens
#SBATCH --error=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.err
#SBATCH --output=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.out

#Set Variables 
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
bin="/storage/home/abc6435/SzpiechLab/bin"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smcpp"
mask="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/mosdepth/uncallable"
data="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/vcf/allsites"
vcf="btnw_allsites_renamed_auto.vcf.gz"

#Run vcf2smc
for i in `cat $scripts/autochrs.txt`; do
    singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
        smc++ vcf2smc \
        --mask $mask/${i}_mask.bed.gz \
        $data/$vcf \
        $out/${i}_virens.smc.gz \
        ${i} \
        virens:SAMN16913449,SAMN16913450,SAMN16913451,SAMN29939256,SAMN29939257,SAMN29939258,SAMN29939260,SAMN29939261,SAMN29939263,SAMN29939273,SAMN29939274,SAMN29939275,SAMN29939276,SAMN29939278,SAMN29939279 
done
```
## waynei
```bash
nano $scripts/vcf2smc_waynei.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=20GB
#SBATCH --time=30:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=vcf2smc_waynei
#SBATCH --error=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.err
#SBATCH --output=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.out

#Set Variables 
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
bin="/storage/home/abc6435/SzpiechLab/bin"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smcpp"
mask="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/mosdepth/uncallable"
data="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/vcf/allsites"
vcf="btnw_allsites_renamed_auto.vcf.gz"

#Run vcf2smc
for i in `cat $scripts/autochrs.txt`; do
    singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
        smc++ vcf2smc \
        --mask $mask/${i}_mask.bed.gz \
        $data/$vcf \
        $out/${i}_waynei.smc.gz \
        ${i} \
        waynei:SAMN29939264,SAMN29939268,SAMN29939270
done
```
## joint populations
since I want to infer the divergence time between populations, I generated joint SMC files that include both of the populations. 
```bash
nano $scripts/vcf2smc_joint.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=20GB
#SBATCH --time=30:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=vcf2smc_joint
#SBATCH --error=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.err
#SBATCH --output=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.out

#Set Variables 
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
bin="/storage/home/abc6435/SzpiechLab/bin"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smcpp"
mask="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/mosdepth/uncallable"
data="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/vcf/allsites"
vcf="btnw_allsites_renamed_auto.vcf.gz"

#Run vcf2smc
for i in `cat $scripts/autochrs.txt`; do
    singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
        smc++ vcf2smc \
        --mask $mask/${i}_mask.bed.gz \
        $data/$vcf \
        $out/${i}_joint.smc.gz \
        ${i} \
        virens:SAMN16913449,SAMN16913450,SAMN16913451,SAMN29939256,SAMN29939257,SAMN29939258,SAMN29939260,SAMN29939261,SAMN29939263,SAMN29939273,SAMN29939274,SAMN29939275,SAMN29939276,SAMN29939278,SAMN29939279 \
        waynei:SAMN29939264,SAMN29939268,SAMN29939270;
done
```