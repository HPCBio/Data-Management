build-lists: true
footer: HPCBio, 2016
slidenumbers: true

# Data analysis

**Chris Fields**
Data Carpentry Workshop
May 19, 2016

---

# Sequence quality check

Next step is to actually grab some data and run a few analyses

---

## Example: RNA-Seq

* Read sequence report from sequencing center 
* Obtain/download sequence data from sequencing center
* Check quality of data and trim low quality bases from ends

---

## Example: RNA-Seq

* Usually the sequencing center will give send you a spreadsheet (can find this on wiki) with the sequencing data files
* Contains information about the file names, the login and password information, and instructions on how to download the files 

---

## Example: RNA-Seq

To run analyses on the cluster you likely need to move data from one location to another

* Move your sequencing data from the Biotechnology Center to the Biocluster
   * Globus is most direct route
   * Upload to Biocluster using an SFTP client
        * Filezilla, Cyberduck, WinSCP…

---

## Exercise

* Today we will be using data that has already been downloaded to Biocluster
* We will then use Filezilla to transfer the html file generated by FASTQC to your work machine

---

## Example Yeast Dataset

* 12 samples
* 2x2 factorial design
* Two yeast strains: wild type & mutant 
* Two treatments: drug & control
* 3 replicates each

---

## Uncompressing data

* Usually the sequencing center will give you data as a compressed file, or several compressed files
* The compression methods used are “tar” and “gzip” simultaneously
* Extension will be either “.tar.gz” or “.tgz”

---

## Exercise: Uncompressing data

1. Log in to the biocluster via Putty
2. Use a worker node by doing an interactive qsub for the classroom queue (``qsub -I -q classroom``)
3. Make a new directory in ``dc-analysis/data`` called ``raw-data``
4. Copy ``/home/classroom/hpcbio/sp15-rnaseq/yeast_data.tar.gz`` to that ``raw-data`` directory
5. ``cd`` into the ``raw-data`` directory
6. Un-tar yeast data using: ``tar –xvf yeast_data.tar.gz``

---

new files should now be present in the raw-data directory

* ``yeast_1_50K.fastq.gz``
* ``yeast_1_50K.fastq.gz``

Any guesses about the compression program used to compressed these, based on the names?

---

## Exercise

* What is your working directory right now?
* How will you navigate back to your home directory?
Make a new directory called fastqc-runs in your home directory.
* We want to run the program fastqc on these new data; how can you find out if there is a module available for fastqc on the biocluster?

---

## FASTQC

* How do my newly obtained data look?
* Check for overall data quality. FastQC is a great tool that enables the quality assessment. 
* In addition to the quality of each sequenced base, it will give you an idea of
  * Presence of, and abundance of contaminating sequences.
  * Average read length
  * GC content

**NOTE** – FastQC is good, but it is very strict and will not hesitate to call your dataset bad on one of the many metrics it tests the raw data for. Use logic and read the explanation for why and if it is acceptable.


---

## FASTQC

* What do I do when FastQC calls my data poor?
  * Poor quality at the ends can be remedied 
    * “quality trimmers” like ``trimmomatic``, ``fastx-toolkit``, etc.
  * Left-over adapter sequences in the reads can be removed
    * “adapter trimmers” like ``trimmomatic``, ``cutadapt``. 

**Always** check for and trim adapters as a matter of routine.  However, the RJC Biotech Center is starting to perform this step 

* We need to amend these issues so we get the best possible alignment
* Once the trimmers have been used, it is best to rerun the data through FastQC to check the resulting data.


---

## Exercise: Run FASTQC

Make sure you are running an interactive job!  What directory are you in now?

```bash
$ qsub -I -q classroom
```

---

Change into your ``results`` directory.  (Question: why here?)

```bash
cd dc_workshop/results
```

Make a new directory based on what you learned from the first session (**Hint** : it should be something like ``2016-05-19-FASTQC-raw-data``).  ``cd`` into that folder.

---

## Symbolic links

Our data is in ``raw-data``.  We don't want to copy the data if possible.  You can run analyses directly on your raw data usng the full or relative paths, but it sometimes is easier to create shortcuts.  This is accomplished using ``ln``.  You will almost always want to create a symbolic link, as it can be removed without harming the original file.

```
$ ln -s ../../data/raw-data/*.fastq.gz .
$ ls -la
total 128
lrwxrwxrwx 1 cjfields cjfields 41 May 18 14:34 yeast_12_50K.fastq.gz -> ../../data/raw-data/yeast_12_50K.fastq.gz
lrwxrwxrwx 1 cjfields cjfields 40 May 18 14:28 yeast_1_50K.fastq.gz -> ../../data/raw-data/yeast_1_50K.fastq.gz
```

---

```bash
$ module load fastqc/0.11.4
```

--- 

## Transferring FASTQC results

1. Create a new folder on the Desktop called rnaseq-workshop
2. Open Filezilla
3. Go to the Site Manager

---

* Create a new folder on the Desktop called rnaseq-workshop
* Open Filezilla
* Go to the Site Manager
* Create a new site
* Log in to biocluster

---

* In the “Host:” field type biocluster.igb.uiuc.edu
  * Leave “Port:” empty
* Make sure you select SFTP from the “Protocol:” pull-down menu
* Change “Logon Type:” to Normal
* Enter your username and password
* Leave “Account:” empty
* Click Connect

---

* Navigate to the fastqc-runs directory on the right panel
* Navigate to rnaseq-workshop on the left panel
* Transfer (by dragging) yeast_1_50K_fastqc.html to your computer

---

Go to the rnaseq-workshop folder on your desktop
Open the .html file by double clicking on it

---

* Run FastQC on the remaining file (``yeast_12_50K.fastq``)
* Transfer the resulting html file to your computer
* How does the overall quality compare between the 2 yeast samples?

---

## Trimming data

* Why?
  * To remove sequencing artifacts 
* What?
  * Adapter sequences
  * Low quality bases
* How?
  * Trimmomatic  (can handle paired-end reads)
  * FASTX-toolkit (includes many other handy tools as well)

---

* Make a new directory in your home directory called trimming/
*  Look at the contents of /home/apps/trimmomatic/trimmomatic-0.33/adapters/
*  Which one should we copy?
*  Copy over the correct one to trimming/ directory & name it myAdapters.fa
*  Look at contents of myAdapters.fa

---

```bash
$ cp /home/classroom/hpcbio/sp15-rnaseq/$ script_templates/trim_yeast_1_50k-TEMPLATE.sh ~/dc_workshop/src/
$ ls ~/dc_workshop/src/
```

---

```bash
#!/bin/bash
-phred33 \
-trimlog yeast_1_50K.trimlog.txt \
TRAILING:28 \
LEADING:28 \
MINLEN:30

```

---

* Change the email address to yours
* Add an extra command at the bottom that will run FASTQC on the resulting trimmed file

---

```
$ exit   # Get on the head node!
```

---

## What if I have 50 files?

* You could edit & run this script 50 times
  * This can be done with a loop within ``bash`` 
* OR you could use a job array
  *  ``#PBS -t 1-50`` <- run this script 50 times
  *  ``#PBS -t 1-50%10`` <- run this script 50 times but only run 10 jobs at a time
* Allows you to be nice and not use too many nodes at once
* Create list that contains names of all the files you want to run 
* Recommend listing file names without extensions


```bash
#!/bin/bash
-phred33 \
-trimlog ${line}.trimlog.txt \
TRAILING:28 \
LEADING:28 \
MINLEN:30

```

