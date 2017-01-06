Overview of ThermoAlign
================================================
ThermoAlign is a genome-aware oligonucleotide design algorithm embedded within a distributable tool designed for targeted resequencing. ThermoAlign's applications range from basic PCR primer pair design to the design of multiplexed primer pairs constituting amplicon tiling paths. Prior SNP and indel information can be provided to facilitate the design of oligonucleotides across conserved sequences. Using a reference genome sequence, BLAST is used to perform a genome-wide searches for sequences similar to each candidate primer. For each off-target hit the melting temperature (Tm) is estimated. To obtain accurate estimates of the Tm, the local alignment of each BLAST hit is used as a seed to create full-length primer-template alignments -- <i>thermoalignments</i> -- from which the Tm is computed. Oligonucleotides with sufficiently greater melting temperatures than all of its predicted off-target binding sites are identified and reported. A directed graph analysis (shortest path algorithm) is used to identify the minimum number of primer pairs forming an amplicon tiling path providing the greatest coverage across a target region.

Licensing and access
================================================
* ThermoAlign is released under a GNU GPLv3 open source license. 
* The source code is available on GitHub: https://github.com/drmaize/ThermoAlign/tree/master/TA_codes
* No need to install the dependencies; Docker images can be accessed to run ThermoAlign on any platform (see: <a href="#docker">below</a>)
* Run parameters are defined in a parameters.py file: https://github.com/drmaize/ThermoAlign/blob/master/TA_codes/parameters.py
* A shell script can be used to run all components: https://github.com/drmaize/ThermoAlign/blob/master/TA_codes/pipeline.sh
* Alternatively, each component of ThermoAlign can be run separately in the following order:
1) TRS.py; 2) UOD.py; 3) PSE.py; 4) PPS.py

Simple Run Case for a ThermoAlign Docker Image
================================================
TA_1.0.0_s is a Docker image containing a small set of sample files that can be used to test ThermoAlign. After installing <a href="https://docs.docker.com/engine/installation/">Docker</a>, run the following command:

    # Command 1:
    docker run -t -i drmaize/thermoalign:TA_1.0.0_s /bin/bash           

![alt tag](https://github.com/drmaize/ThermoAlign/blob/master/images/docker_screen_shot.png)

This will pull TA_1.0.0_s from docker hub, generate a new ThermoAlign container in your local machine and open the container where commands can be executed. If the image is already present in your local system, Docker will skip the download step.

    # Command 2: move to TA_codes directory
    cd TA_codes/
    
    # Command 3: perform one-time preprocessing of vcf files in "../sample_vcf" directory
    python vcf_conversion.py
    
Standard default parameters are preset, but users can modify the parameters.py file to adjust design parameters in each module of ThermoAlign. The the Docker images include a vim text editor so that users may modify the parameters file. Have a look at the parameters file using the following command:

    # Command 4 (optional): the vim editor can be used to modify design parameters 
    # type "i" to insert/modify values; use the "Esc" keyboard button followed by ":x" to save and quit.
    vim parameters.py
    
Once you've exited from vim, ThermoAlign can be run.
    
    # Command 5: run ThermoAlign
    ./pipeline.sh

![alt tag](https://github.com/drmaize/ThermoAlign/blob/master/images/docker_screen_shot_2.png)

    # Command 6: view what's inside of the TA_codes folder
    # This now includes a folder with the prefix "TA_" followed job id information (time stamp info).
    # The TA-pre-fixed folder contains all of the output from the ThermoAlign run.
    ls
    
Hopefully the run was a success! The output files from each modules are explained <a href="#output">here</a>. If you are having trouble or something seems wrong, please feel free to contact <a href="mailto:ffrancis@udel.edu>Felix Francis</a> or <a href="mailto:rjw@udel.edu>Randy Wisser</a>.

The next steps show you how to copy the ThermoAlign results from the Docker container to a folder on your machine. For this you will need the container id and the folder name of the ThermoAlign output. The container id is the id following "root@." in your terminal. For example, if your terminal shows "root@44a8a1fe2bff:", 44a8a1fe2bff is your container id. Command 6 showed us the name of the folder containin the ThermoAlign output

    # Command 7: exit Docker
    exit

    # Command 8: copy output files from the container to your desired local directory
    # This command has the following structure:
    docker cp <containerId>:/TA_codes/<resultfolder> /local/path/target
    
    # Here is an example of the actual command, which needs to be edited to reflect details on your machine.
    docker cp 44a8a1fe2bff:/TA_codes/TA_2016-09-16T16_24_05_130340 /Users/ffx/Documents/ThermoAlign_results
    # replace "44a8a1fe2bff" with the docker contaiener id on your machine; 
    # replace "TA_2016-09-16T16_24_05_130340" with the directory name of the output folder;
    # replace "/Users/ffx/Documents/TA_results" with the path to where you want to copy the output to.

<h1 id="docker">
ThermoAlign is Dockerized
</h1>
<a href="https://www.docker.com/">Docker</a> is an efficient way to port ThermoAlign across systems and operating systems. The following docker images are available for deployment of ThermoAlign.

* (i) TA_1.0.0_s is a sample run version containing a small set of sample files that can be used to test ThermoAlign
* (ii) TA_1.0.0_d is a general distributable version which requires user supplied files (reference sequence and variant data)
* (iii) TA_1.0.0_Zm3 is a maize-ready version containing all components required for running ThermoAlign as described by Francis et al. #######.

Note: for optimum performance with large and highly repetitive genomes such as maize, it may be better to run the source code natively, with each of the required dependencies installed on your local machine or cluster. 

Installing Docker
================================================
Docker installation can be done by visiting the official Docker installation page at: https://docs.docker.com/engine/installation/

Docker commands are described at https://sites.google.com/site/felixfranciersite/blogs/docker or https://docs.docker.com/engine/reference/commandline/.

Three docker images are available:

    # Option 1: container with a sample genome and sample polymorphism file, for quick testing of ThermoAlign
    docker run -t -i drmaize/thermoalign:TA_1.0.0_s /bin/bash
                            
    # Option 2: container without any reference genome or polymorphism file (user needs to supply these files)
    docker run -t -i drmaize/thermoalign:TA_1.0.0_d /bin/bash
                      
    # Option 3: maize-ready version
    docker run -t -i drmaize/thermoalign:TA_1.0.0_Zm3 /bin/bash
    
On a cluster using the qsub batch manager, Docker containers may be run in interactive mode:
    
    qsub -I -V -N intrctv -l nodes=1:ppn=5
    
and then,
    
    docker run -t -i drmaize/thermoalign:TA_1.0.0_s /bin/bash
    
Be aware that the available memory for running these docker containers should be greater than the combined size of the whole genome and variant files. The number of processors requested (ppn) should match or not exceed the number of threads requested for BLAST in the parameters.py file.

Format for external whole genome and variant files:
================================================

### _Input chromosme_
The reference sequence for each chromosome needs to be separated into separate files and named as follows: 
chr1.fasta, chr2.fasta etc

For each sequence, the fasta header should be of the following format:
>chromosome:assembly_ver:chr#:start_pos:end_pos:#sequences

For example:
>chromosome:AGPv3:13:1:7261561:1

###  _Input variant vcf file_ 
A vcf file (v4.0 or v4.1) based on the same coordinate system of the reference genome sequence may be optionally used for polymorphism-aware primer design

The variant vcf files for each chromosome needs to be separated into separate files and named as follows:
chr1.vcf, chr2.vcf etc

<h1 id="output">
Output Files
</h1>

All output files will be saved in a directory named based on the timestamp of TA_year-month-date-time format (for example :TA_2016-09-13T09_05_00_545957).

Each output file name will start with the same timestamp. 

###  _The TRS module:_

An output file with detailed summary statistics from the run:
TA_2016-09-13T09_05_00_545957_run_summary.txt

Variant masked locus sequence in fasta format
TA_2016-09-13T09_05_00_545957_3_33000_2001_VariantMasked.fasta

###  _The UOD module:_

All possible primers (+ and -strands) for the given UOD paramters (.fasta format):
TA_2016-09-13T09_05_00_545957_UOD_out1_1.fasta

All possible primers (+ and -strands) for the given UOD paramters (.txt format).
TA_2016-09-13T09_05_00_545957_UOD_out1_2.txt

<i>.fasta</i> file of + strand primers:
TA_2016-09-13T09_05_00_545957_UOD_out2_1.fasta

<i>.fasta</i> file of - strand primers:
TA_2016-09-13T09_05_00_545957_UOD_out2_2.fasta

<i>.fasta</i> file of + strand primers, with primer location, length and Tm information
TA_2016-09-13T09_05_00_545957_UOD_out3_1.fasta

<i>.fasta</i> file of - strand primers, with primer location, length and Tm information
TA_2016-09-13T09_05_00_545957_UOD_out3_2.fasta

<i>.fasta</i> file of + & - strand primers
TA_2016-09-13T09_05_00_545957_UOD_out4_1.fasta

###  _The PSE module:_

TA_2016-09-13T09_05_00_545957_PSE_out1_1.csv                        : .csv file with all possible thermoalignment information for + strand primers

TA_2016-09-13T09_05_00_545957_PSE_out2_1.csv                        : .csv file with all possible thermoalignment information for - strand primers

TA_2016-09-13T09_05_00_545957_PSE_out3_1.csv                        : .csv file with the Max_misprime_Tm and primer feature information for + and - primers


###  _The PPS module:_

TA_2016-09-13T09_05_00_545957_primer_pairs_info.txt                 : A .txt file containing all possible primer pairs for the user defined parameters and their relevant information

TA_2016-09-13T09_05_00_545957_primer_pairs_order.txt                : Primer names, sequences and their Tm for ordering

TA_2016-09-13T09_05_00_545957_multiplx_input_set1_1.txt, TA_2016-09-13T09_05_00_545957_multiplx_input_set2_1.txt: 
contains the non-overlapping primer pair combinations from minimal tiling paths. These are used for checking multiplex compatibility

TA_2016-09-13T09_05_00_545957_multiplex_groups_set1_1.txt, TA_2016-09-13T09_05_00_545957_multiplex_groups_set2_1.txt: .txt file listing names of multiplex compatible groups

TA_2016-09-13T09_05_00_545957_bed_separate_tracks_selected_oligos.bed: .bed formatted files of the primers for further analysis and visualization.

TA_2016-09-13T09_05_00_545957_multiplx_pooled_output.txt            : .txt file with the list of primer pairs grouped as multiplex compatible sets. All primers in each set may be used in a single reaction.

Advanced Use
================================================
ThermoAlign can be executed via Python on a local cluster independent of Docker. This requires installation of the following components. The versions listed are those distributed in the Docker container. Newer versions of these components have not been tested with ThermoAlign.
* Linux/Unix
* [Python  2.7](http://python.org/)
* [NumPy   1.9.2](http://www.numpy.org/)
* [pandas  0.18.1](http://pandas.pydata.org/)
* [Cython  0.24](http://cython.org/)
* [primer3-py  0.5.1](https://pypi.python.org/pypi/primer3-py)
* [BLAST   2.2.31+](http://blast.ncbi.nlm.nih.gov/Blast.cgi)
* [MultiPLX    2.0](http://bioinfo.ut.ee/download/dl.php?file=24)
* [networkx    1.11](https://networkx.github.io/)




   
                                    ##### END OF README #####    

