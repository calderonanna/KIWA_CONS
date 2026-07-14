# Create Bootstraps
Unfortunately, there is no built in bootstrapping feature in smc++ (at least not one I'm aware of). To simulate the bootstrapping feature from older smc++ version, I first split my vcf into 1M Non-overlapping Windows. Then, I sampled windows (with replacement) until the total length of the genome is roughly equal to the original genome length (autsomal size is 938,591,657 bases)

## Split VCF 
```bash
nano $scripts_folder/split_vcf_smc++.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=20GB
#SBATCH --time=48:00:00
#SBATCH --account=open
#SBATCH --partition=himem
#SBATCH --job-name=vcf2smc
#SBATCH --error=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.err
#SBATCH --output=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.out

#Set Variables 
bin="/storage/home/abc6435/SzpiechLab/bin"
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
work="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smc++/bootstrap"
vcf="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/vcf/btnw_filtered_chrsrenamed_auto.vcf.gz"

#Obtain Chromosome Lengths
bcftools view -h \
    $vcf | \
    grep "=chr" \
    > $scripts/chrlengths.txt

sed -i 's/##contig=<ID=//g' $scripts/chrlengths.txt
sed -i 's/,length=/ /g' $scripts/chrlengths.txt
sed -i 's/>//g' $scripts/chrlengths.txt
sed -i '$d' $scripts/chrlengths.txt
sed -i 's/ /\t/g' $scripts/chrlengths.txt

#Make 1MB Windows For Each Chromosome
bedtools makewindows -g \
    $scripts/chrlengths.txt \
    -w 1000000 \
    > $work/windows.bed

#Extract Windows From VCF
while read chrom start end; do
    bcftools view -r \
        ${chrom}:${start}-${end} $vcf \
        -Oz -o ${work}/${chrom}_${start}_${end}.vcf.gz
done < $work/windows.bed
```

## Create Bootstraps
```bash
nano $scripts/bootstrap_vcf_smc++.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=20GB
#SBATCH --time=48:00:00
#SBATCH --account=open
#SBATCH --partition=himem
#SBATCH --job-name=bootstrap_vcf_smc++
#SBATCH --error=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.err
#SBATCH --output=/storage/home/abc6435/SzpiechLab/abc6435/KROH/err_out/%x.%j.out

#Set Variables 
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
work="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smc++/bootstrap"
vcf="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/vcf/btnw_filtered_chrsrenamed_auto.vcf.gz"
n_win=938
n_rep=100

for rep in $(seq 1 $n_rep); do
    #Randomly choose 938 1MB windows
    find $work/*gz -type f | shuf -n $n_win > $work/vcf_list_${rep}.txt
    #Sort Windows
    for chr in `cat $scripts/autochrs.txt`; do
        grep "/${chr}_" $work/vcf_list_${rep}.txt \
        >> $work/temp_${rep};
    done
    mv -f $work/temp_${rep} $work/vcf_list_${rep}.txt

    #Concatenate windows into a vcf
    bcftools concat -f \
        $work/vcf_list_${rep}.txt -Ou | \
        bcftools sort \
        -Oz -o $work/bootstrap_${rep}.vcf.gz
    tabix $work/bootstrap_${rep}.vcf.gz
    rm -rf $work/vcf_list_${rep}.txt;
done
```

