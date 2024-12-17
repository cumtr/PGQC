# PGQC

**P**an**G**enome **Q**uality **C**ontrol (**PGQC**) aims to measure pangenome graph quality using single-copy and universal K-mers.  
We assume that single-copy K-mers found in an assembly and universally present across all assemblies are orthologous, and should be found uniquely and in their full length in the nodes of the graph.

We thus propose simple metrics to describe a pangenome graph based on the proportion of single-copy and universal K-mers composing the assemblies that are:
- _**Unique**_: Present only once and in full length in one of the nodes of the graph.
- _**Duplicated**_: Present multiple times in the graph.
- _**Collapsed**_: Fragmented over different nodes due to the aggregation of non-orthologous sequences.

The pipeline relies on [KMC](https://github.com/refresh-bio/KMC) to identify single-copy K-mers shared by all the assemblies (i.e., universal) composing a pangenome graph.

---

## 1. Installation

### Optional: Create a Dedicated Environment

This step is not mandatory, but PGQC requires KMC to be installed and available in your `$PATH`. Creating a dedicated environment is a convenient way to ensure no interference with other software.

```
# Using mamba 
mamba create -n PGQC-env bioconda::kmc=3.2.4
mamba activate PGQC-env

# Using conda
# conda create -n PGQC-env bioconda::kmc=3.2.4
# conda activate PGQC-env
```
### clone the directory

Download PGQC

```
git clone https://github.com/cumtr/PGQC.git
```

### test run

To test PGQC, run :

```
cd PGQC
chmod +x ./PGQC
./PGQC
```

This command should print the help line for PGQC:

`Usage: ./PGQC -p <panGenome> -a <path/to/assemblies/> -o <outputDir/outputBasename> -t <tempDir> (-k <kmer_size> -v)`

---

## 2. Run PGQC

To run, PGQC require four informations :
- **`-p`** Point to the graph in `.gfa` format
- **`-a`** Point to the directory where all the assemblies that compose the graph are stored (PGQC assumes all the files is this directory than ends with `.fasta` are the assemblies to consider)
- **`-o`** Gives to PGQC the path + basename of the output file
- **`-t`** set the temp directory for PGQC to write temporary files

two other optionnal parameters can be provided : 
- **`-k`** is used to give to PGQC the kmer size to use. defalt value is 100.
- **`-v`** state for the level of verbose. adding this flag (no values expected) make PGQC really verbose. mostly useful for debugging.

A typical command would be :
```
# mamba activate PGQC-env
./PGQC -p ./MyPanGenomeGraph.gfa -a ./InputAssemblies/ -o ./OutputPGQC/MyPanGenomeGraph.PGQC -t ./TEMP/ -k 100
```
This command would produce five disctinct files :

- `./OutputPGQC/MyPanGenomeGraph.PGQC.stats.txt` : Contains counts of _**single-copy and universal**_ K-mers, _**unique**_ K-mers, _**duplicated**_ K-mers, and _**collapsed**_ K-mers in the graph.

the four other files report the exact sequence of the kmers for the different categories :

- `./OutputPGQC/MyPanGenomeGraph.PGQC.all.txt`: List of all _**single-copy and universal**_ K-mers.
- `./OutputPGQC/MyPanGenomeGraph.PGQC.unique.txt`: List of _**unique**_ K-mers.
- `./OutputPGQC/MyPanGenomeGraph.PGQC.duplicated.txt`: List of _**duplicated**_ K-mers.
- `./OutputPGQC/MyPanGenomeGraph.PGQC.collapsed.txt`: List of _**collapsed**_ K-mers.


## Companion scripts

PGQC comes with two companion scripts.

**`scripts/GFA2HaploFasta.bash`**

this script is usefull to extract the assemblies from a graph. **Before using it, make sure the graph was not trimmed in any way and contian the full assemblies** (row output from pggb or mograph cactus for exaple).

`Usage: ./scripts/GFA2HaploFasta.bash -p <panGenome.gfa> -t <tempDir> -o <outDir> -@ <threads>`

this script requires samtools and odgi to be present in you path. you can install them in the environement using : 
`mamba install -n PGQC-env bioconda::samtools=1.21 bioconda::odgi=0.9.0`

**`scripts/makeTriangularPlot.R`** (TODO)

this script uses R to make a triangular plot for a given a `.stats.txt` output file from PGQC.


