# Filter VCF
Includes only biallelic snps, includes only autosomal sites. Sites with less than 50 OR greater than 158 DP to missing. Also sets sites with QUAL less than 50 to missing. 

```bash
nano $scripts/filter_vcf.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=20GB
#SBATCH --time=100:00:00
#SBATCH --account=dut374_hc_default
#SBATCH --job-name=filter_vcf
#SBATCH --output=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set Variables
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
allsites="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/gatk/allsites_vcf"
snps="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/gatk/snps_vcf"
chrs=$(paste -sd, $scripts/autochrs.txt)

#Include biallelic SNPS only
bcftools view -M2 -m2 -v snps $allsites/cKIWA_allsites.vcf.gz -Oz -o $snps/cKIWA_snps_bi.vcf.gz
bcftools index -t $snps/cKIWA_snps_bi.vcf.gz

#Filter autosomes
bcftools view $snps/cKIWA_snps_bi.vcf.gz -r "$chrs" -Oz -o $snps/cKIWA_snps_bi_autosomes.vcf.gz
bcftools index -t $snps/cKIWA_snps_bi_autosomes.vcf.gz

#Filter Site Depth at 5th and 95th Percentiles
bcftools filter -e 'INFO/DP<80 || INFO/DP>158' -S . -Oz -o $snps/cKIWA_snps_bi_autosomes_DP.vcf.gz $snps/cKIWA_snps_bi_autosomes.vcf.gz
bcftools index -t $snps/cKIWA_snps_bi_autosomes_DP.vcf.gz

#Filter Site Quality
bcftools filter -e 'QUAL<50' -S . -Oz -o $snps/cKIWA_snps_bi_autosomes_DP_QUAL.vcf.gz $snps/cKIWA_snps_bi_autosomes_DP.vcf.gz
bcftools index -t $snps/cKIWA_snps_bi_autosomes_DP_QUAL.vcf.gz
```


