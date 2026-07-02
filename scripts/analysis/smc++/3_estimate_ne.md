# Estimate Ne

```bash
nano $scripts/smc++_estimate.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=100GB
#SBATCH --time=30:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=smc++_estimate
#SBATCH --error=/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/err_out/%x.%j.err
#SBATCH --output=/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables 
bin="/storage/home/abc6435/SzpiechLab/bin"
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
work="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smcpp"
results="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smcpp/results"

#virens
singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
    smc++ estimate \
    -o $results/virens \
    4.6e-9 \
    --polarization-error 0.5 \
    $work/chr*_virens.smc.gz
    

#waynei
singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
    smc++ estimate \
    -o $results/waynei \
    4.6e-9 \
    --polarization-error 0.5 \
    $work/chr*_waynei.smc.gz
```