# Readme Main Simulation
Explains all the components of the main simulations as well as justifications for each parameter. 

## Setup 
This block sets up a non Wright Fisher Model and enables separate sex along with constant and global variables as follows:
- `bi_end`: defines the ending cycle of of the burn-in so that the main simulation picks up on the following cycle. 
- `scenario` and `K_cons`: varies between future conservation management based on expected reduction in number of adult males
![alt text](<../../../../diagrams/Screenshot 2026-07-23 at 1.40.24 PM.png>)
- `sim_end`: Defines the cycle at which to end the simulation
- `R`: Replicate number
- `Ka`: The ancestral K {31250, 62500, 125000}

```c
//SETUP
initializeSLiMModelType("nonWF");
initializeSex();

//CONSERVATION SCENARIOS AND REPLICATES
//Define burn-in end cycle, sim-end cycle, replicate, and Ka
defineConstant("bi_end", 10000);
defineConstant("scenario", "BAU");
defineConstant("K_cons", 9924);
defineConstant("sim_end", 2050);
defineConstant("R", "R1");
defineConstant("Ka", 31250);
```
## DEMOGRAPHICS
Defines the demographic attributes of this population based on census data of singing males and mean-reverting K.

- `phi`: The degree of autocorrelation for the OU Process
- `sigma`: The variation in environmental change
- `K_mean`: The average K which the OU will pull the long-term K of ~9924
- `logK`: The natural log of K_mean
- `census_year`: Time frame when formal census counts took place.
- `K_series`: Defined as the estimated population size during bottleneck years and calculated from census surveys of singing males as:

 $$
K_t = AHYm_t + AHYf_t + HY_t \quad \forall t \in [1951,2025]
 $$

 Where:
 - $AHYm_t$ : Number of singing males.
 - $AHYf_t = \frac{AHYm_t}{SexRatio}$
 - $HY_t = \frac{AHYm_t + AHYf_t}{AgeRatio}$

- $SexRatio=1.37$ : an emergent property resulting from skewed sex-based mortality, but also empirically supported in Meyers 2010 (https://research.fs.usda.gov/feis/species-reviews/seki)

 - $AgeRatio\ \epsilon\ \{1.15, 3.5\}$ : emergent properties resulting from each having 3.3 fledglings (citation) without cowbird parasitism and 1 fledgling with cowbird parasitism (Kelly and DeCapita 1982)

 - `*_mort`: Sex and age-based mortality probabilities (Bocetti et al. 2002), which are used to create a life history table for each sex with a maximum age of 12 years. 

```c
//DEMOGRAPHICS
defineConstant("phi", 0.9);
defineConstant("sigma", 0.1);
defineGlobal("K_mean", 9924);
defineGlobal("logK", log(K_mean));
defineGlobal("census_year", c(seq(bi_end+1951,bi_end+2025, by=1)));
defineGlobal("K_series", c(
	960.8508863, 960.8508863, 960.8508863, 960.8508863, 960.8508863,
	960.8508863, 960.8508863, 960.8508863, 960.8508863, 960.8508863,
	1116.544317, 1116.544317, 1116.544317, 1116.544317, 1116.544317,
	1116.544317, 1116.544317, 1116.544317, 1116.544317, 1116.544317,
	1116.544317, 650.0764837, 646.8422723, 698.5896541, 540.1132974,
	578.9238337, 646.8422723, 708.2922882, 633.9054268, 679.1843859,
	785.9133608, 750.3370359, 669.4817518, 695.3554427, 695.3554427,
	698.5896541, 679.1843859, 540.1132974, 669.4817518, 685.6528086,
	863.5344335, 1125.505554, 1283.981911, 1568.59251, 2047.255792,
	2474.171692, 2241.308474, 2364.208505, 2603.540146, 2923.727071,
	2881.682323, 3509.119327, 3402.390352, 3887.522056, 4359.716915,
	4586.111711, 4802.803872, 5520.798794, 5831.283085, 5905.669946,
	5731.022533, 5912.138369, 6759.501745, 6539.575373, 7095.859727,
	7652.144081, 7370.767693, 7089.391304, 7175.097905, 7175.097905,
	7175.097905, 7260.804507, 6091.637099, 6091.637099, 6091.637099
	));
	
	//Define Constants Life History Table (Bocetti et al. 2002)
	mhy_mort=0.54;
	fhy_mort=0.63;
	mahy_mort=0.35;
	fahy_mort=0.38;
	defineConstant("mmort", c(mhy_mort,mahy_mort,mahy_mort,mahy_mort,mahy_mort,mahy_mort,
		mahy_mort,mahy_mort,mahy_mort,mahy_mort,mahy_mort,mahy_mort,mahy_mort,1.0));
	defineConstant("fmort", c(fhy_mort,fahy_mort,fahy_mort,fahy_mort,fahy_mort,fahy_mort,
		fahy_mort,fahy_mort,fahy_mort,fahy_mort,fahy_mort,fahy_mort,fahy_mort,1.0));
```
## GENETICS
Samples mutations from a gamma distribution estimated from human data by Kim et al. 2017. Further `initializeMutationType()` uses an hs mixture model where weakly deleterious mutations are additive and become more recessive with decreasing selection coefficients; this hs relationship was directly obtained from Kyriazis et al. 2025. Mutation Proportion were obtained by taking the area under the curve from the gamma distribution and then augmented with 99.5% non-lethal mutations and 0.05% lethal mutations as in Kyriazis et al. 2025. Notice that I am not simulating neutral mutations to reduced computation overhead. Last, I simulate a single chromosome based on our reference genome and import exonic regions based on our reference annotations. This chromosome recombines at a rate of 3e-8 per site per generation (citation) and has a mutation rate of 4e-9 (citation)
```c
	//GENETICS
	//Discrete Deleterious DFE (SLiM5 Manual, pg.789, Kyriazis et al. 2025, and Kim et al.2017)
	initializeMutationType("m1", 0.45, "s", "do x=rgamma(1,-0.01314833,0.186); while (x < -0.001); return x;"); //weak
	initializeMutationType("m2", 0.2, "s", "do x=rgamma(1,-0.01314833,0.186); while (x < -0.01 | x >= -0.001); return x;"); //moderate
	initializeMutationType("m3", 0.05, "s", "do x=rgamma(1,-0.01314833,0.186); while (x < -0.1 | x >= -0.01); return x;"); //strong
	initializeMutationType("m4", 0.0, "s", "do x=rgamma(1,-0.01314833,0.186); while (x >= -0.1); return x;"); // semi-lethal
	initializeMutationType("m5", 0.0, "f", -1.0); //lethal
	
	//Mutation Proportions (99.5% nonlethals, 0.5% lethal)
	P_weak = 0.491*0.995;
	P_moderate = 0.247*0.995;
	P_strong = 0.236*0.995;
	P_semilethal = 0.026*0.995;
	P_lethal = 0.005;
	initializeGenomicElementType("g1", c(m1,m2,m3,m4,m5), c(P_weak, P_moderate, P_strong, P_semilethal, P_lethal));

    //Import Chr1 Structure
	path = "~/Desktop/KIWA_CONS/data/slim/genome_structure/";
	chr = 1;
	length = 114063801;
	initializeChromosome(id=chr, length=length);
	
	exons = readFile(path + "exon_chr" + chr + ".txt");
	for (exon in exons) {
		components = strsplit(exon, "\t");
		initializeGenomicElement(g1, asInteger(components[0])-1, asInteger(components[1])-1);
	}
	
	//Set Recombination Rate and Mutation Rate
	initializeRecombinationRate(3e-8);
	initializeMutationRate(4e-9);
```
## REPRODUCTION
**Age-Limited Reproduction & Age-Related Pairing Success**

Limits reproduction to individuals greater than or equal to 1 year old (similar to an AHY) and employs an age-assortative pairing for males. Specifically, I separate males into two groups: second-year-males (SYM) and after-second-year-males (ASYM). SYM are those males in their second year of life or 1 year old in simulation time. ASYM are those males in their second year of life or greater; that is, they are 2-12 years old. There is evidence to suggest that males *Setophaga* warblers are more likely to find a mate when they are older. In Golden Cheeked Warblers, Jukkala 2022 found that ASYM are 99.4% more likely to successfully pair, while SYM are 86% likely to pair successfully. To implement this assorative age-related pairing rates, I sample 99.4% ASYM 86% of SYM and combine them into a list of breeding males: `m`. For females, as long as they are of reproductive age, they each get sampled with equal probability. To choose pairs, I shuffle breeding females and breeding males and randomly pair them with one important caveat: the females get sampled without replacement and the males get sampled with replacement. Biologically, this means that males have a chance of having more than one female. My justification for doing this is that empirical observations suggest a small degree of polygamy in the population but a subject that is not well-researched. 

**Fledglings Per Pair With and Without Parasitism**

Cowbird parasitism had a big impact on reproductive success. It was estimated that each pair fledged one or no nestlings at all during 1931-1971 (Kelly and DeCapita 1982) while in non-parasitism years the average number of fledglings produced per pair was around ~3.3. I implement these findings in this section by simulating a single fledgling per individuals between the years of 1931-1971. During non-parasite years, pairs produced an average of 3.3 fledglings with some small variation (0.16). 
```c
reproduction(){
	//Age-Limited Reproduction & Age-Related Pairing Success (Jukkala 2022, pg.36)
	f = p1.individuals[p1.individuals.sex == "F" & p1.individuals.age >= 1];
	asym = (p1.individuals[p1.individuals.sex == "M" & p1.individuals.age >= 2]);
	sym = (p1.individuals[p1.individuals.sex == "M" & p1.individuals.age == 1]);
	asym_breeders = sample(asym, asInteger(round(size(asym) * 0.994)));
	sym_breeders = sample(sym, asInteger(round(size(sym) * 0.86)));
	m = c(asym_breeders, sym_breeders);
	shuf_f = sample(f, size(f), replace=F);
	shuf_m = sample(m, size(m), replace=T);
	n = min(size(shuf_f), size(shuf_m));
	
	//Fledglings Per Pair With and Without Parasitism
	if (sim.cycle > (bi_end+1931) & sim.cycle < (bi_end+1971)){
		for (i in seq(0, n - 1)){
			mom = shuf_f[i];
			dad = shuf_m[i];
			p1.addCrossed(mom, dad, count = 1);
		}
	}

	else {
		for (i in seq(0, n - 1)){
			baseline_fledglings = max(0,asInteger(round(rnorm(n = 1, mean = 3.3, sd = 0.16))));
			mom = shuf_f[i];
			dad = shuf_m[i];
			p1.addCrossed(mom, dad, count = baseline_fledglings);
		}
	}
	
	self.active = 0;
}
```
## IMPORT BURN-IN
This simply takes the burn-in full simulation output and imports it so that the main simulation can pick up where the burn-in ended. I have three different burn-ins which differ by ancestral carrying capacity: $K_a\ \epsilon\{31250, 62500, 125000\}$
```c
bi_end early() {
	sim.readFromPopulationFile("~/Desktop/burnin_31250.txt");
}
```

## RUN SIMULATION
- **Sex and Age Based Individual Fitness Scaling**: 
Individual fitnesses are scaled based on their sex and age which have different mortality rates

- **K Bottleneck Years (1951-2025 Census data)**: 
This section adjusts the carrying capacity which is based on formal census surveys of singing males and adjusted to include adult females and hatch-year birds. See `DEMOGRAHICS` for specifics.  

- **K Future Conservation Planning**: 
This section implements future conservation management from 2025 until the end of the simulation (`sim_end`). `K_cons` will be one of four different conservation scenario; see `SETUP` section for specifics.

- **K Pre-Bottleneck Years**: 
After the burn-in but before the bottleneck (`bi_end`-`bi_end+1950`), I assume a constant mean carrying capacity of 9924. This block is just saying that if it is not a decline year, then  K_mean = 9924

- **K-based Population Fintess Scaling (Ornstein-Uhlenbeck Discrete); (Kyriazis et al. 2021)**: 
Like Kyriazis et al. 2021, I implement a fluctuating carrying capacity. The Ornstein-Uhlenbeck process describes a mean-reverting process.

$$
logK_{t+1}​=logK_{mean}(1−\phi) + \phi logK_{t} + \epsilon_{t}
$$
$$
where;\ \phi=0.9,\ \epsilon=rnorm(n=1,\mu=0,\sigma=0.1)
$$
- $logK_{mean}(1-\phi)$: is the degree of autocorrelation, that is retains a portion ($1-\phi$ or 10%) of the previous value. 
- $\phi logK$: is the mean reverting component. So it retains $\phi$ or 90% of the long term mean (so 90% of 9924)
- $\epsilon$: is the degree of environmental stochasticity, which is determined by a standard deviation, $\sigma$.
```c
//RUN SIMULAION
early() {
	
	//Sex and Age Based Individual Fitness Scaling
	males = p1.individuals[p1.individuals.sex=="M"];
	females = p1.individuals[p1.individuals.sex=="F"];
	m_ages = p1.individuals[p1.individuals.sex=="M"].age;
	f_ages = p1.individuals[p1.individuals.sex=="F"].age;
	m_mort = mmort[m_ages];
	f_mort = fmort[f_ages];
	m_surv = 1-m_mort;
	f_surv = 1-f_mort;
	males.fitnessScaling = m_surv;
	females.fitnessScaling = f_surv;
	
	//K Bottleneck Years (1951-2025 Census data)
	decline_year = F;
	for (i in seq(0, size(census_year) - 1)) {
		if (sim.cycle == census_year[i]) {
			K_mean = K_series[i];
			logK = log(K_mean);
			decline_year = T;
			break;
		}
	}
	
	//K Future Conservation Planning
	if (sim.cycle > (bi_end+2025)) {
		K_mean = K_cons;
		logK = log(K_mean);
	}
	
	//K Pre-Bottleneck Years
	else if (decline_year) {
		logK = log(K_mean);
	}
	
	//K-based Population Fintess Scaling (Ornstein-Uhlenbeck Discrete); (Kyriazis et al. 2021)
	logK = (1-phi) * log(K_mean) + phi * logK + rnorm(n=1, mean=0, sd=sigma);
	K = exp(logK);
	p1.fitnessScaling = K/(p1.individualCount * mean(c(m_surv, f_surv)));
}
```
## EXPORT DATA
I export data between 1900 through the end of the simulation. First, I export generation times to varify that the mean generation time is around 2 years. This is basically a check point for me which indicates that I'm replicating the basic biology of the population. Next, I obtain an average number of deleterious mutation per individual per mutation type. Overall, I export the following stats at every cycle: Number of individuals, number of males, number of females, generation time, mean fitness, mean FROH, inbreeding load, heterozygosity, deleterious mutations per individuals, replicate number, ancestral carrying capacity, and the conservation scenario
```c
(bi_end+1900):(bi_end+sim_end) late() {
	
	//Generation Times
	repro_m = p1.individuals[p1.individuals.sex=="M" & p1.individuals.age>=1].age;
	repro_f = p1.individuals[p1.individuals.sex=="F" & p1.individuals.age>=1].age;
	
	//Mutation Counts
	n_m1 = integer(size(p1.individuals));
	n_m2 = integer(size(p1.individuals));
	n_m3 = integer(size(p1.individuals));
	n_m4 = integer(size(p1.individuals));
	n_m5 = integer(size(p1.individuals));
	for (ind in seq(0,size(p1.individuals)-1)){
		n_m1[ind] = p1.individuals[ind].countOfMutationsOfType(m1);
		n_m2[ind] = p1.individuals[ind].countOfMutationsOfType(m2);
		n_m3[ind] = p1.individuals[ind].countOfMutationsOfType(m3);
		n_m4[ind] = p1.individuals[ind].countOfMutationsOfType(m4);
		n_m5[ind] = p1.individuals[ind].countOfMutationsOfType(m5);
	};
	
	writeFile("~/Desktop/main_31250_results.txt",
		sim.cycle + "," +
		size(p1.individuals) + "," +
		size(p1.individuals[p1.individuals.sex == "M" & p1.individuals.age >= 1]) + "," +
		size(p1.individuals[p1.individuals.sex == "F" & p1.individuals.age >= 1]) + "," +
		mean(c(repro_m, repro_f)) + "," +
		calcPi(p1.haplosomes) + "," +
		mean(p1.cachedFitness(NULL)) + "," +
		calcMeanFroh(p1.individuals) + "," +
		calcInbreedingLoad(p1.haplosomes) + "," +
		calcHeterozygosity(p1.haplosomes)+ "," +
		mean(n_m1) + "," +
		mean(n_m2) + "," +
		mean(n_m3) + "," +
		mean(n_m4) + "," +
		mean(n_m5) + "," +
		R + "," +
		Ka + "," +
		scenario,
		append=T);
}
```
## END SIMULATION
Instructs the simulation to end at a specific cycle OR if the population goes extinct
```c
//END SIMULATION
late (){
	
	//End Sim After Year 2500 Or If Extinction Occurs.
	if (sim.cycle > (bi_end+sim_end) | p1.individualCount < 10){
		sim.simulationFinished();
	}
}
```