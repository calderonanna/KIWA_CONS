# Plot Models

```bash
#Set Variables
bin="/storage/home/abc6435/SzpiechLab/bin"
scripts="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/scripts"
results="/storage/home/abc6435/SzpiechLab/abc6435/KIWA_CONS/data/smcpp/results"

# Split 
singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
    smc++ plot \
    -g 2 \
    --csv \
    $results/split/split.pdf \
    $results/split/model.final.json

# virens 
singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
    smc++ plot \
    -g 2 \
    --csv \
    $results/virens/virens_plot.pdf \
    $results/virens/model.final.json

# waynei 
singularity exec --bind $PWD:/mnt $bin/smcpp.sif \
    smc++ plot \
    -g 2 \
    --csv \
    $results/waynei/waynei_plot.pdf \
    $results/waynei/model.final.json