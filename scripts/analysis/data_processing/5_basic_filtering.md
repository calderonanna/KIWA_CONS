# Basic Filtering
```bash
nano $scripts/filter_vcf.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=20GB
#SBATCH --time=100:00:00
#SBATCH --account=dut374_hc_default
#SBATCH --job-name=basic_filtering
#SBATCH --output=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set Variables
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
allsites="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/gatk/allsites_vcf"
mywa_folder="/storage/home/abc6435/ToewsLab/mywa_genome_2/final_assembly"
chrs=$(paste -sd, $scripts/autochrs.txt)

# #Filter autosomes
# bcftools view $allsites/cKIWA_allsites.vcf.gz -r "$chrs" -Oz -o $allsites/cKIWA_allsites_auto.vcf.gz
# bcftools index -t $allsites/cKIWA_allsites_auto.vcf.gz

#Filter Site Depth at 5th and 95th Percentiles
bcftools filter -e 'INFO/DP<X || INFO/DP>Y' -S . -Oz -o $allsites/cKIWA_allsites_auto_DP.vcf.gz $allsites/cKIWA_allsites_auto.vcf.gz
bcftools index -t $allsites/cKIWA_allsites_auto_DP.vcf.gz

#Filter Site Quality
bcftools filter -e 'QUAL<50' -S . -Oz -o $allsites/cKIWA_allsites_auto_DP_QUAL.vcf.gz $allsites/cKIWA_allsites_auto_DP.vcf.gz
bcftools index -t $allsites/cKIWA_allsites_auto_DP_QUAL.vcf.gz

#Exclude Multiallelic Sites
bcftools view -M2 $allsites/cKIWA_allsites_auto_DP_QUAL.vcf.gz -Oz -o $allsites/cKIWA_allsites_auto_DP_QUAL_M2.vcf.gz
bcftools index -t $allsites/cKIWA_allsites_auto_DP_QUAL_M2.vcf.gz

#Remove Indels, Spanning Deletions, and Structural Variants while simultaneously retaining invariant sites
bcftools view -i 'STRLEN(REF)==1 && (ALT=="." || (STRLEN(ALT)==1 && ALT!="*"))' $allsites/cKIWA_allsites_auto_DP_QUAL_M2.vcf.gz -Oz -o $allsites/cKIWA_allsites_auto_DP_QUAL_M2_bi.vcf.gz
bcftools index -t $allsites/cKIWA_allsites_auto_DP_QUAL_M2_bi.vcf.gz

#Missing Data
bcftools view -e 'F_MISSING>.2' $allsites/cKIWA_allsites_auto_DP_QUAL_M2_bi.vcf.gz -Oz -o $allsites/cKIWA_allsites_auto_DP_QUAL_M2_bi_fmiss.vcf.gz
bcftools index -t $allsites/cKIWA_allsites_auto_DP_QUAL_M2_bi_fmiss.vcf.gz
```


