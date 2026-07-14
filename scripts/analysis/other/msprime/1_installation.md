# Installation 

## Install Dadi First
```bash 
#Create Virutal Environment
python3 -m venv msprime_env
source msprime_env/bin/activate

#Install Dadi
pip install --upgrade pip
pip install msprime tskit pyslim ipykernel

#Check Install and obtain site packages folder location
pip show msprime
