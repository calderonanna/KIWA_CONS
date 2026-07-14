# VCF2SMC

## Create Script
```bash
nano $scripts/vcf2smc_bootstrap_0.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=10GB
#SBATCH --time=2:00:00
#SBATCH --account=open
#SBATCH --partition=standard
#SBATCH --job-name=vcf2smc_bootstrap_0
#SBATCH --error=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.err
#SBATCH --output=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.out


#Set Variables 
bin="/storage/home/abc6435/SzpiechLab/bin"
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KROH/scripts"
work="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smc++/bootstrap"
vcf="bootstrap_0.vcf.gz"

bcftools query -f \
    '%CHROM' \
    $vcf | \
    uniq \
    >> $work/bootstrap_0_autochrs.txt

#Run vcf2smc
for i in `cat $work/bootstrap_0_autochrs.txt`; do
    singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
        smc++ vcf2smc \
        $work/$vcf \
        $work/bootstrap_0_${i}.smc.gz \
        ${i} \
        BTNW:SAMN16913449,SAMN16913450,SAMN16913451,SAMN29939256,SAMN29939257,SAMN29939258,SAMN29939260,SAMN29939261,SAMN29939263,SAMN29939264,SAMN29939268,SAMN29939270,SAMN29939273,SAMN29939274,SAMN29939275,SAMN29939276,SAMN29939278,SAMN29939279; 
    done

rm -rf $work/bootstrap_0_autochrs.txt
rm -rf $work/bootstrap_0.vcf.gz 
rm -rf $work/bootstrap_0.vcf.gz.tbi

#Submit scripts
work="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smc++/bootstrap"
n_rep=100

for rep in $(seq 1 $n_rep); do
    cp $scripts/vcf2smc_bootstrap_0.bash $scripts/vcf2smc_bootstrap_${rep}.bash
    sed -i "s/bootstrap_0/bootstrap_${rep}/g" $scripts/vcf2smc_bootstrap_${rep}.bash
    sbatch $scripts/vcf2smc_bootstrap_${rep}.bash;
done