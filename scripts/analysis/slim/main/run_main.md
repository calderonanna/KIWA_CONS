# Run Main Simulations

## Create Results File
```bash
#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"
out="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/slim/main"

echo "cycle,N,Nm,Nf,g,pi,mean_fitness,mean_froh,B,het,m2_ind,m3_ind,m4_ind,m5_ind,m6_ind,R,Ka,scenario" > $out/main_31250_results.txt

echo "cycle,N,Nm,Nf,g,pi,mean_fitness,mean_froh,B,het,m2_ind,m3_ind,m4_ind,m5_ind,m6_ind,R,Ka,scenario" > $out/main_62500_results.txt

echo "cycle,N,Nm,Nf,g,pi,mean_fitness,mean_froh,B,het,m2_ind,m3_ind,m4_ind,m5_ind,m6_ind,R,Ka,scenario" > $out/main_125000_results.txt
```

## Ka=31250
```bash
nano $scripts/run_main_31250.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=40GB
#SBATCH --time=300:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=run_main_31250
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"

#BAU
for i in {1..50}; do 
    cp $scripts/main_31250.slim $scripts/main_31250_BAU_R${i}.slim
    sed -i "s/Xscenario/BAU/g" $scripts/main_31250_BAU_R${i}.slim
    sed -i "s/XK_cons/9924/g" $scripts/main_31250_BAU_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_31250_BAU_R${i}.slim
    $slim -p $scripts/main_31250_BAU_R${i}.slim
    rm -rf $scripts/main_31250_BAU_R${i}.slim
done

#-25%AHYm
for i in {1..50}; do 
    cp $scripts/main_31250.slim $scripts/main_31250_-25%AHYm_R${i}.slim
    sed -i "s/Xscenario/-25%AHYm/g" $scripts/main_31250_-25%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_31250_-25%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_31250_-25%AHYm_R${i}.slim
    $slim -p $scripts/main_31250_-25%AHYm_R${i}.slim
    rm -rf $scripts/main_31250_-25%AHYm_R${i}.slim
done

#-50%AHYm
for i in {1..50}; do 
    cp $scripts/main_31250.slim $scripts/main_31250_-50%AHYm_R${i}.slim
    sed -i "s/Xscenario/-50%AHYm/g" $scripts/main_31250_-50%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_31250_-50%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_31250_-50%AHYm_R${i}.slim
    $slim -p $scripts/main_31250_-50%AHYm_R${i}.slim
    rm -rf $scripts/main_31250_-50%AHYm_R${i}.slim
done

#-75%AHYm
for i in {1..50}; do 
    cp $scripts/main_31250.slim $scripts/main_31250_-75%AHYm_R${i}.slim
    sed -i "s/Xscenario/-75%AHYm/g" $scripts/main_31250_-75%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_31250_-75%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_31250_-75%AHYm_R${i}.slim
    $slim -p $scripts/main_31250_-75%AHYm_R${i}.slim
    rm -rf $scripts/main_31250_-75%AHYm_R${i}.slim
done
```

## Ka=62500
```bash
nano $scripts/run_main_62500.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=40GB
#SBATCH --time=300:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=run_main_62500
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"

#BAU
for i in {1..50}; do 
    cp $scripts/main_62500.slim $scripts/main_62500_BAU_R${i}.slim
    sed -i "s/Xscenario/BAU/g" $scripts/main_62500_BAU_R${i}.slim
    sed -i "s/XK_cons/9924/g" $scripts/main_62500_BAU_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_62500_BAU_R${i}.slim
    $slim -p $scripts/main_62500_BAU_R${i}.slim
    rm -rf $scripts/main_62500_BAU_R${i}.slim
done

#-25%AHYm
for i in {1..50}; do 
    cp $scripts/main_62500.slim $scripts/main_62500_-25%AHYm_R${i}.slim
    sed -i "s/Xscenario/-25%AHYm/g" $scripts/main_62500_-25%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_62500_-25%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_62500_-25%AHYm_R${i}.slim
    $slim -p $scripts/main_62500_-25%AHYm_R${i}.slim
    rm -rf $scripts/main_62500_-25%AHYm_R${i}.slim
done

#-50%AHYm
for i in {1..50}; do 
    cp $scripts/main_62500.slim $scripts/main_62500_-50%AHYm_R${i}.slim
    sed -i "s/Xscenario/-50%AHYm/g" $scripts/main_62500_-50%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_62500_-50%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_62500_-50%AHYm_R${i}.slim
    $slim -p $scripts/main_62500_-50%AHYm_R${i}.slim
    rm -rf $scripts/main_62500_-50%AHYm_R${i}.slim
done

#-75%AHYm
for i in {1..50}; do 
    cp $scripts/main_62500.slim $scripts/main_62500_-75%AHYm_R${i}.slim
    sed -i "s/Xscenario/-75%AHYm/g" $scripts/main_62500_-75%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_62500_-75%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_62500_-75%AHYm_R${i}.slim
    $slim -p $scripts/main_62500_-75%AHYm_R${i}.slim
    rm -rf $scripts/main_62500_-75%AHYm_R${i}.slim
done
```

## Ka=125000
```bash
nano $scripts/run_main_125000.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --mem=40GB
#SBATCH --time=300:00:00
#SBATCH --account=dut374_sc_default
#SBATCH --job-name=run_main_125000
#SBATCH --error=/storage/group/zps5164/default/abc6435/KIWA_CONS/err_out/%x.%j.out

#Set Variables
scripts="/storage/group/zps5164/default/abc6435/KIWA_CONS/scripts"
slim="/storage/group/zps5164/default/bin/SLiM/build/slim"

#BAU
for i in {1..50}; do 
    cp $scripts/main_125000.slim $scripts/main_125000_BAU_R${i}.slim
    sed -i "s/Xscenario/BAU/g" $scripts/main_125000_BAU_R${i}.slim
    sed -i "s/XK_cons/9924/g" $scripts/main_125000_BAU_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_125000_BAU_R${i}.slim
    $slim -p $scripts/main_125000_BAU_R${i}.slim
    rm -rf $scripts/main_125000_BAU_R${i}.slim
done

#-25%AHYm
for i in {1..50}; do 
    cp $scripts/main_125000.slim $scripts/main_125000_-25%AHYm_R${i}.slim
    sed -i "s/Xscenario/-25%AHYm/g" $scripts/main_125000_-25%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_125000_-25%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_125000_-25%AHYm_R${i}.slim
    $slim -p $scripts/main_125000_-25%AHYm_R${i}.slim
    rm -rf $scripts/main_125000_-25%AHYm_R${i}.slim
done

#-50%AHYm
for i in {1..50}; do 
    cp $scripts/main_125000.slim $scripts/main_125000_-50%AHYm_R${i}.slim
    sed -i "s/Xscenario/-50%AHYm/g" $scripts/main_125000_-50%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_125000_-50%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_125000_-50%AHYm_R${i}.slim
    $slim -p $scripts/main_125000_-50%AHYm_R${i}.slim
    rm -rf $scripts/main_125000_-50%AHYm_R${i}.slim
done

#-75%AHYm
for i in {1..50}; do 
    cp $scripts/main_125000.slim $scripts/main_125000_-75%AHYm_R${i}.slim
    sed -i "s/Xscenario/-75%AHYm/g" $scripts/main_125000_-75%AHYm_R${i}.slim
    sed -i "s/XK_cons/7443/g" $scripts/main_125000_-75%AHYm_R${i}.slim
    sed -i "s/XR/R${i}/g" $scripts/main_125000_-75%AHYm_R${i}.slim
    $slim -p $scripts/main_125000_-75%AHYm_R${i}.slim
    rm -rf $scripts/main_125000_-75%AHYm_R${i}.slim
done
```