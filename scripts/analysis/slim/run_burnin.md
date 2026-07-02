# Run Burn-in

```bash
nano $scripts/run_burnin.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=600GB
#SBATCH --time=300:00:00
#SBATCH --account=zps5164_cr_default
#SBATCH --partition=himem
#SBATCH --job-name=run_burnin
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"

#Run SLiM script
$slim -p $scripts/burnin.slim