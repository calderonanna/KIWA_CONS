# Run Burn-in
`burnin_31250.slim` assumes an ancestral population of 31,250 individuals.
`burnin_62500.slim` assumes an ancestral population of 62,500 individuals. 
`burnin_125000.slim` assumes an ancestral population of 125,000 individuals.

## Burn-in 31250
```bash
nano $scripts/run_burnin_31250.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=100GB
#SBATCH --time=300:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=run_burnin_31250
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/slim/burnin"

#Prep Results File
echo "cycle,N,Nm,Nf,g,pi,mean_fitness,mean_froh,B,het,m1_ind,m2_ind,m3_ind,m4_ind,m5_ind" > $out/burnin_31250_results.txt

#Run SLiM script
$slim -p $scripts/burnin_31250.slim
```

## Burn-in 62500
```bash
nano $scripts/run_burnin_62500.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=100GB
#SBATCH --time=300:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=run_burnin_62500
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/slim/burnin"

#Prep Results File
echo "cycle,N,Nm,Nf,g,pi,mean_fitness,mean_froh,B,het,m1_ind,m2_ind,m3_ind,m4_ind,m5_ind" > $out/burnin_62500_results.txt

#Run SLiM script
$slim -p $scripts/burnin_62500.slim
```

## Burn-in 125000
```bash
nano $scripts/run_burnin_125000.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=100GB
#SBATCH --time=300:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=run_burnin_125000
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/slim/burnin"

#Prep Results File
echo "cycle,N,Nm,Nf,g,pi,mean_fitness,mean_froh,B,het,m1_ind,m2_ind,m3_ind,m4_ind,m5_ind" > $out/burnin_125000_results.txt

#Run SLiM script
$slim -p $scripts/burnin_125000.slim
```