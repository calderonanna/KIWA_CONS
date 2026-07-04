# gatk GenomicsDBImport 
https://gatk.broadinstitute.org/hc/en-us/articles/360036883491-GenomicsDBImport
https://hpc.nih.gov/training/gatk_tutorial/genomics-db-import.html

To make this run faster, I will be running GATK scripts on invdidual chromosomes. GenomicsDBImport takes many samples and combines them by genomic position. Then once combined, it stores them in a GenomicsDB (database). `GenomicsDBImport` is kind of like `CombineGVCFs` in that they both combine gvcfs, but the differene is in how they do it. `CombineGVCFs` merges all samples into one multi-sample gVCF and it is appropriate for about a dozen samples. `GenomicsDBImport` produces a queryable database, can be faster and more efficient for hundreds of samples. 

## gatk GenomicsDBImport
```bash
for i in $(cat $scripts/autochrs.txt); do
    cat<<EOT > $scripts/gatk_GenomicsDBI_cKIWA${i}.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=3GB
#SBATCH --time=24:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=gatk_GenomicsDBI_cKIWA${i}
#SBATCH --output=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set Variables
scripts="for i in $(cat $scripts/autochrs.txt); do
    cat<<EOT > $scripts/gatk_GenomicsDBI_cKIWA${i}.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=3GB
#SBATCH --time=24:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=gatk_GenomicsDBI_cKIWA${i}
#SBATCH --output=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.err

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
ref="/storage/group/zps5164/default/abc6435/KIWA_CONS/data/mywa_reference/mywagenomev2.1.fa"
allsites="/storage/group/zps5164/default/abc6435/KIWA_CONS/data/gatk/allsites_vcf"
samples=\$(realpath /storage/group/zps5164/default/abc6435/KIWA_CONS/data/gatk/gvcf/*.g.vcf.gz | sed 's/^/--variant /')

#GenomicsDBImport
gatk --java-options "-Xmx10g" GenomicsDBImport \\
    \$samples \\
    --genomicsdb-workspace-path \$allsites/${i} \\
     -L ${i}
EOT
done

#Submit Scripts
for i in $(cat $scripts/autochrs.txt); do
    sbatch $scripts/gatk_GenomicsDBI_cKIWA${i}.bash
done"
ref="/storage/group/zps5164/default/abc6435/KIWA_CONS/data/mywa_reference/mywagenomev2.1.fa"
allsites="/storage/group/zps5164/default/abc6435/KIWA_CONS/data/gatk/allsites_vcf"
samples=\$(realpath /storage/group/zps5164/default/abc6435/KIWA_CONS/data/gatk/gvcf/*.g.vcf.gz | sed 's/^/--variant /')

#GenomicsDBImport
gatk --java-options "-Xmx10g" GenomicsDBImport \\
    \$samples \\
    --genomicsdb-workspace-path \$allsites/${i} \\
     -L ${i}
EOT
done

#Submit Scripts
for i in $(cat $scripts/autochrs.txt); do
    sbatch $scripts/gatk_GenomicsDBI_cKIWA${i}.bash
done
```
