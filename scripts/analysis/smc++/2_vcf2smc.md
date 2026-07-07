# vcf2smc
vcf2smc can only be run on a single contig or chromosome. Also the default behavior is to set the first sample as the "distinguished lineage" and the others as "undistinguished". The distinguished individual provides the lineage that smc explicity models along the genome whiel the remaining samples contribute to allele frequency. Which invidual is chosen as the distinguished lineag has a small but statistically negligible effect on the results (see https://github.com/popgenmethods/smcpp/issues/18). 

--mask, -m: This specifies a BED-formatted mask file whose positions will be marked as missing data (across all samples) in the outputted SMC++ data set. This can be used to delineate large uncalled regions (e.g. centromeres) which are often omitted in VCF files; without additional information provided by --mask, there is no way to distinguish these missing regions from very long runs of homozygosity. For finer-grained control of missing data, setting individual positions and samples to the missing genotype, ./., also works fine. (The point of --mask is to save the user the trouble of emitting millions of rows of missing observations in the VCF).

```bash
nano $scripts/vcf2smc.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=20GB
#SBATCH --time=30:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=vcf2smc
#SBATCH --error=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.err
#SBATCH --output=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.out

#Set Variables 
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
bin="/storage/home/abc6435/SzpiechLab/bin"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smcpp"
mask="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/mosdepth/uncallable"
snps="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/gatk/snps_vcf"
vcf="cKIWA_snps_bi_autosomes_DP_QUAL.vcf.gz"

#Run vcf2smc
for i in `cat $scripts/autochrs.txt`; do
    singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
        smc++ vcf2smc \
        --mask $mask/${i}_mask.bed.gz \
        $snps/$vcf \
        $out/${i}.smc.gz \
        ${i} \
        cKIWA:183194841,183194861,183195304,183195312,183195321,183195326,183195332
done
```
