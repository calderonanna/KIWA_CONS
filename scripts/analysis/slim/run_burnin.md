# Run Burn-in
`burnin_31250.slim` assumes an ancestral population of 31,250 individuals.
`burnin_62500.slim` assumes an ancestral population of 62,500 individuals. 
`burnin_125000.slim` assumes an ancestral population of 125,000 individuals.

## Burn-in 31250
```bash
nano $scripts/run_burnin_31250.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=150GB
#SBATCH --time=300:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=run_burnin_31250
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"

#Run SLiM script
$slim -p $scripts/burnin_31250.slim
```

## Burn-in 62500
```bash
nano $scripts/run_burnin_62500.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=200GB
#SBATCH --time=300:00:00
#SBATCH --account=zps5164_cr_default
#SBATCH --partition=himem
#SBATCH --job-name=run_burnin_62500
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"

#Run SLiM script
$slim -p $scripts/burnin_62500.slim
```

## Burn-in 12500
```bash
nano $scripts/run_burnin_125000.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=250GB
#SBATCH --time=300:00:00
#SBATCH --account=zps5164_cr_default
#SBATCH --partition=himem
#SBATCH --job-name=run_burnin_125000
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"

#Run SLiM script
$slim -p $scripts/burnin_125000.slim
```