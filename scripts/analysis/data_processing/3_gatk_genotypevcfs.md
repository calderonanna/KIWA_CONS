# gatk GenotypeGVCFs
For DFE inference with dadi, I want to include both variant and invariant sites so I included the flag `--include-non-variant-sites true`.  

```bash
for i in $(cat $scripts/autochrs.txt); do
    cat<<EOT > $scripts/gatk_GenotypeGVCFs_${i}.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=3GB
#SBATCH --time=48:00:00
#SBATCH --account=open
#SBATCH --partition=standard
#SBATCH --job-name=gatk_GenotypeGVCFs_${i}
#SBATCH --output=~/SzpiechLab/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=~/SzpiechLab/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set Variables
scripts="~/SzpiechLab/abc6435/KIWA_CONS/scripts"
ref="~/SzpiechLab/abc6435/KIWA_CONS/data/mywa_reference/mywagenomev2.1.fa"
allsites="~/SzpiechLab/abc6435/KIWA_CONS/data/gatk/allsites_vcf"

#GenotypeGVCFs
gatk --java-options "-Xmx3g" GenotypeGVCFs \\
    -R \$ref \\
    -V gendb://\$allsites/${i} \\
    -L ${i}:1+ \\
    --include-non-variant-sites true \\
    -O \$allsites/${i}.vcf.gz
EOT
done

#Submit Scripts
for i in $(cat $scripts/autochrs.txt); do
    sbatch $scripts/gatk_GenotypeGVCFs_${i}.bash
done


