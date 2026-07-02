# DP Percentiles
Calculates the 5th and 95th percentiles of site depth

```bash
nano $scripts/DP_percentiles.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=150GB
#SBATCH --time=300:00:00
#SBATCH --account=zps5164_cr_default
#SBATCH --partition=himem
#SBATCH --job-name=DP_percentiles
#SBATCH --output=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set variables
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
allsites="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/vcf/allsites"
vcf="btnw_allsites_renamed_auto.vcf.gz"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/seqstats"
scratch="/scratch/abc6435"

#Extract and sort DP
bcftools query -f '%DP\n' $allsites/$vcf \
| grep -v '^\.$' \
| sort -T $scratch -S 50G -n > $scratch/dp_sorted.txt

#Calculate 5th/95th Percentiles
awk '
{
    a[NR]=$1
}
END{
    p5=int(0.05*NR)
    p95=int(0.95*NR)

    if(p5<1)p5=1
    if(p95<1)p95=1

    print "N =", NR
    print "5th percentile =", a[p5]
    print "95th percentile =", a[p95]
}' $scratch/dp_sorted.txt > $out/dp_5_95.txt

#Clean Up 
#rm -rf $scratch/dp_sorted.txt
