# Haplotype Caller

```bash
for i in `cat $scripts/cKIWA_IDS.txt`; do
    cat<<EOT > $scripts/haplotypecaller_${i}.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=20GB
#SBATCH --ntasks=2
#SBATCH --time=100:00:00
#SBATCH --account=dut374_sc_default 
#SBATCH --job-name=haplotypecaller_${i}.bash
#SBATCH --output=~/SzpiechLab/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=~/SzpiechLab/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set Variables
gatk="~/SzpiechLab/abc6435/KIWA_CONS/data/gatk"
bam="~/SzpiechLab/abc6435/KIWA_CONS/data/bam"
ref="~/SzpiechLab/abc6435/KIWA_CONS/data/mywa_reference/mywagenomev2.1.fa"
scripts="~/SzpiechLab/abc6435/KIWA_CONS/scripts"

#Index bams
cd \$bam
samtools index \$bam/${i}_sorted_marked.bam

#Run HaplotypeCaller
gatk HaplotypeCaller \\
    -R \$ref \\
    -I \$bam/${i}_sorted_marked.bam \\
    -ERC GVCF \\
    -O \$gatk/gvcf/${i}.g.vcf \\
    --native-pair-hmm-threads 2 \\
    >& \$gatk/log/${i}gvcf.log
EOT
done