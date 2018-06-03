# C3POa
Computational pipeline for calling consensi on R2C2 nanopore data.

### Dependencies ###
- [Python 3.6](https://www.python.org/downloads/)
- [NumPy 1.13.3](https://scipy.org/install.html)
- [poa v1.0.0 Revision: 1.2.2.9](https://github.com/tanghaibao/bio-pipeline)
- [EMBOSS water: watHerON v8](https://users.soe.ucsc.edu/~rvolden/C3POa/EMBOSS-6.6.0_v8.tar.gz)
- [minimap2 2.7-r654](https://github.com/lh3/minimap2)
- [racon](https://github.com/isovic/racon)
- [blat source](https://users.soe.ucsc.edu/~kent/src/blatSrc35.zip) or [blat executable](http://hgdownload.soe.ucsc.edu/admin/exe/)

To install dependencies, use setup.sh.  
setup.sh will download and make all of the packages that you need to run C3POa (except Python, NumPy and blat).  
You don't need to have these in your PATH, but if you don't, you'll need to use a [config file](example_config).
```bash
chmod +x setup.sh
./setup.sh
```

To install NumPy, you can go [here](https://scipy.org/install.html).  
Otherwise, you can use your computer's package manager (apt-get, dnf, brew, etc.) to install.  
Pip3 is another option for NumPy installation.  
Example:
```bash
sudo dnf install python3-numpy
```
or
```bash
pip3 install numpy
```

For blat, there are a couple options. You can build from [source](https://users.soe.ucsc.edu/~kent/src/blatSrc35.zip) or you can get an [executable](http://hgdownload.soe.ucsc.edu/admin/exe/). Please follow the documentation in the blat readme for make instructions.

### Usage ###
After resolving all of the dependencies, you can run C3POa with python.

### C3POa_preprocessing.py ### 

Takes raw 1D nanopore R2C2 reads in fastq format, removes low quality and short reads and then finds splint sequences in those reads using BLAT. It then adds the position of the splint to the read name and generates separate folders each containing a fastq file containing 4000 raw reads. 

Options (All required)
  -i raw reads in fastq format
  -o path where a splint_reads folder with the output files will be generated
  -q only reads above this average quality will be retained (9 is recommended)
  -l only read longer than this number will be retained (1000 recommended)
  -s sequence of DNA splint used in R2C2 protocol in fasta format. Sequence name must contained the string 'Splint'
  -c config file containing path to BLAT binary (format example: blat[tab]/path/to/blat)

```bash
python3 C3POa_preprocessing.py -i raw_reads.fastq -o output_path -q quality_cutoff -l read_length_cutoff -s Splint_sequence.fasta
```

### C3POa.py ###

Takes fastq output produced by C3POa_preprocessing.py and generates consensus sequences in fasta format and a subread sequences in fastq format.

  -p Directory to which all temporary files as well as final output file will be written. Defaults to your current directory.
  -m path to NUC.4.4.mat file (included in repository) 
  -c config file containing paths to poa, racon, water, blat, and minimap2. (format example poa[tab]/path/to/poa)
  -o name (without path) of fasta file that the consensus gets written to.
                        Defaults to R2C2_Consensus.fasta
  -r fastq file that contains reads processed by C3POa_preprocessing.py


```bash
python3 C3POa.py --reads reads.fastq [--path PATH] [--matrix MATRIX] [--config CONFIG] [--output OUTPUT] [--figure FIGURE]
```

### C3POa_postprocessing.py ### 

Trimms and reorients consensus sequences produced by C3POa.py

```bash
python3 C3POa.py --r reads.fastq -p out_path -m /path/to/NUC.4.4.mat -c config_file -output  [--figure FIGURE]
```


