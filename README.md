# VexDesign
A script that autonomously designs a vaccine. Authored by Sari Sabban on 31-May-2017 (sari.sabban@gmail.com).

## Requirements:
1. Make sure you install [PyRosetta](http://www.pyrosetta.org) as the website describes.
2. Use the following commands (in GNU/Linux) to install all nessesary programs and Python libraries for this script to run successfully:

`sudo apt update && sudo apt install python-beautifulsoup4 python-biopython python-lxml pymol DSSP gnuplot -y`

## How To Use:
1. Use the following command to run the script:

`python3 VaxDesign.py PDBID RCHAIN CHAIN FROM TO`

* PDBID = The protein's [Protein Data Bank](https://www.rcsb.org) identification name
* RCHAIN = The chain where your receptor resides within the protein .pdb file
* CHAIN = The chain where your target site resides (not part of the receptor) within the protein .pdb file
* FROM = The start of your target site
* TO = The end of your target site

Example:

`python3 VaxDesign.py 2y7q A B 420 429`

2. Calculation time is about 12 hours on a normal desktop computer for each structure.
3. Access to the internet is a requirement since the script will be sending and retrieving data from some servers.
4. Use this [Rosetta Abinitio](https://github.com/sarisabban/RosettaAbinitio) script to simulate the folding of the final designed vaccine's protein structure. An HPC (High Preformance Computer) and the original C++ [Rosetta](https://www.rosettacommons.org/) are required for this step.

## Description
This script autonomously designs a vaccine from a user specified target site. This is not artificial intellegance, you cannot just ask the the script to design "A" vaccine, you must understand what target site you want to develop antibodies against (make a liturature search and understand your disease and target site), then supply this target site to the script to build a protein structure around it so the final protein can be used as a vaccine. You must have prior understanding of Bioinformatics and Immunology in order to be able to understand what site to target and to supply it to the script. Once you identify a target site, the script will take it and run a long protocol, without the need for you to intervene, that will result in an ideal protein structure displaying your target site in its original 3D cofiguration. Thus the protien, theoretically, can be used as a vaccine against this site, and hopefully neutralise the disease you are researching. Everytime you run this script a different final protien structure will be generated, this is important to keep in mind, because if you want to generate many different structures to test or to use as boosts you can simply run the same target site again and you will end up with a different final structure.

This script has been last tested to work well with PyRosetta 4 Release 147 and using Python 3.5. If you use this script on a newer PyRosetta or Python version and it fails please notify me of the error type to get it updated.

Here is a [video](youtube.com/) that explains how to select a target site, how the script functions, and what results you sould get. If I did not make a video yet, bug me until I make one.

The script's protocol is as follows:
1. Build Scaffold. --> STILL UNDER DEVELOPMENT --> I am having lots of trouble with De Novo Design (I have a very long temporary work around)
2. Isolate motif.
3. Isolate receptor.
4. Graft motif onto scaffold.
5. Sequence design the structure around the motif.
6. Generate fragments for Rosetta Abinitio folding simulation.
7. If average fragment RMSD is higher than 2Å repeat steps 5 and 6.

Output files are as follows:

|    | File Name               | Description                                                                                  |
|----|-------------------------|----------------------------------------------------------------------------------------------|
| 1  | DeNovo.pdb              | Scaffold structure                                                                           |
| 2  | motif.pdb	       | Original requested motif                                                                     |
| 3  | receptor.pdb            | Original receptor that binds motif                                                           |
| 4  | grafted.pdb             | Grafted motif to De Novo structure                                                           |
| 5  | structure.pdb           | Sequence designed structure                                                                  |
| 6  | structure.fasta         | Fasta of Rosetta Designed structure                                                          |
| 7  | frags.200.3mers         | 3-mer fragment of sequence designed structure from the Robetta server                        |
| 8  | frags.200.9mers         | 9-mer fragment of sequence designed structure from the Robetta server                        |
| 9  | pre.psipred.ss2         | PSIPRED secondary structure prediction of sequence designed structure from the Robetta server|
| 10 | plot_frag.pdf           | Plot of the fragment quality RMSD vs Position                                                |
| 11 | RMSDvsPosition.dat      | plot_frag.pdf's data                                                                         |
| 12 | FragmentAverageRMSD.dat | Average RMSD of the fragments                                                                |

## Simple Design
If you have a strucutre that you want to target, but this structure is less than 150 amino acids and is a ridgid structure (mainly helices and sheets and very little loops), you might get away with just re-designing the structure itself, while keeping the target site(s) conserved, without having to do anything complicated like isolate the motif and graft it onto a scaffold protein (as in the previous script). The code for this protocol is very simple just use the following command:

`python3 Simple.py PDBID CHAIN NO_DESIGN_POSITIONS`

* PDBID = The protein's [Protein Data Bank](https://www.rcsb.org) identification name
* CHAIN = The chain of your target structure that you want to design
* NO_DESIGN_POSITIONS = The amino acid positions that you want to conserve (not re-design), i.e: your motif(s)

Example:

`python Simple.py 3WD5 A 19 20 21 22 23 24 65 66 67 68 140 141 143 144 145 146 147 148`

The script's protocol is as follows:
1. Get the user defined protein structure and isolate the defined chain
2. Sequence design the structure (but not the user defined conserved amino acids). If the NO_DESIGN_POSITIONS is left empty the entire protein will be designed (good as a negative control)

The script will not access the Robetta server nor setup any of the Abinitio folding simulation files, it is meant to be used with more manual work from the user than automate the work work. Calculation time is about 3 hours.

Output files are as follows:

|    | File Name               | Description                                                                                  |
|----|-------------------------|----------------------------------------------------------------------------------------------|
| 1  | Original.pdb            | The original non-designed structure (for comparisons)                                        |
| 2  | Designed.pdb	       | The designed structure                                                                       |
