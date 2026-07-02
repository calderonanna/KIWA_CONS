# Basic Filtering
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
allsites="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/vcf/allsites"
mywa_folder="/storage/home/abc6435/ToewsLab/mywa_genome_2/final_assembly"
chrs=$(paste -sd, $scripts/autochrs.txt)

# #Rename Contigs
# bcftools annotate --rename-chrs $scripts/rename_chrs.txt -Oz -o $allsites/btnw_allsites_renamed.vcf.gz $allsites/btnw_allsites.vcf.gz
# bcftools index -t $allsites/btnw_allsites_renamed.vcf.gz

# #Filter autosomes
# bcftools view $allsites/btnw_allsites_renamed.vcf.gz -r "$chrs" -Oz -o $allsites/btnw_allsites_renamed_auto.vcf.gz
# bcftools index -t $allsites/btnw_allsites_renamed_auto.vcf.gz
# echo "$allsites/btnw_allsites_renamed_auto.vcf.gz" >> $allsites/nsites.txt
# bcftools view -H $allsites/btnw_allsites_renamed_auto.vcf.gz | wc -l >> $allsites/nsites.txt

#Filter Site Depth at 5th and 95th Percentiles
bcftools filter -e 'INFO/DP<217 || INFO/DP>364' -S . -Oz -o $allsites/btnw_allsites_renamed_auto_DP.vcf.gz $allsites/btnw_allsites_renamed_auto.vcf.gz
bcftools index -t $allsites/btnw_allsites_renamed_auto_DP.vcf.gz
echo "$allsites/btnw_allsites_renamed_auto_DP.vcf.gz" >> $allsites/nsites.txt
bcftools view -H $allsites/btnw_allsites_renamed_auto_DP.vcf.gz | wc -l >> $allsites/nsites.txt

#Filter Site Quality
bcftools filter -e 'QUAL<50' -S . -Oz -o $allsites/btnw_allsites_renamed_auto_DP_QUAL.vcf.gz $allsites/btnw_allsites_renamed_auto_DP.vcf.gz
bcftools index -t $allsites/btnw_allsites_renamed_auto_DP_QUAL.vcf.gz
echo "$allsites/btnw_allsites_renamed_auto_DP_QUAL.vcf.gz" >> $allsites/nsites.txt
bcftools view -H $allsites/btnw_allsites_renamed_auto_DP_QUAL.vcf.gz | wc -l >> $allsites/nsites.txt

#Exclude Multiallelic Sites
bcftools view -M2 $allsites/btnw_allsites_renamed_auto_DP_QUAL.vcf.gz -Oz -o $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2.vcf.gz
bcftools index -t $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2.vcf.gz
echo "$allsites/btnw_allsites_renamed_auto_DP_QUAL_M2.vcf.gz" >> $allsites/nsites.txt
bcftools view -H $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2.vcf.gz | wc -l >> $allsites/nsites.txt

#Remove Indels, Spanning Deletions, and Structural Variants while simultaneously retaining invariant sites
bcftools view -i 'STRLEN(REF)==1 && (ALT=="." || (STRLEN(ALT)==1 && ALT!="*"))' $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2.vcf.gz -Oz -o $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi.vcf.gz
bcftools index -t $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi.vcf.gz
echo "$allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi.vcf.gz" >> $allsites/nsites.txt
bcftools view -H $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi.vcf.gz | wc -l >> $allsites/nsites.txt

#Missing Data
bcftools view -e 'F_MISSING>.2' $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi.vcf.gz -Oz -o $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi_fmiss.vcf.gz
bcftools index -t $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi_fmiss.vcf.gz
echo "$allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi_fmiss.vcf.gz" >> $allsites/nsites.txt
bcftools view -H $allsites/btnw_allsites_renamed_auto_DP_QUAL_M2_bi_fmiss.vcf.gz | wc -l >> $allsites/nsites.txt
```


