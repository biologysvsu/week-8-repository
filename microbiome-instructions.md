## Fork and Clone directory
- Make sure you clone it to your ocean folder.
- Make sure you `cd` into your forked repository
You should know how to do this now! `Woohoo`

## Install MultiQC
MultiQC is a bioinformatics tool that combines quality-control results from multiple sequencing samples into a single report. We will install MultiQC and FastQC using Conda on Bridges-2.
1. Load Anaconda: Anaconda allows us to create separate environments containing the software needed for our analyses.
```
module load anaconda3
```
2. Create a Conda environment: The following command creates an environment named multiqc and installs both MultiQC and FastQC.
```
conda create -n multiqc -c conda-forge -c bioconda multiqc fastqc -y
```
3. Activate the environment: Once installation is compleete, activate the environment:
```
conda activate multiqc
```
You should now see (multiqc) at the beginning of your terminal prompt.

4. Verify the installation: Check that both programs are available:
```
multiqc --version
```
```
fastqc --version
```
If both commands display version information, the installation was successful.
5. Deactivate the MultiQC Conda environment before installing the next software.
```
conda deactivate
```
## Install QIIME2

1. Load anaconda. If you are using the same terminal window from the MultiQC installation, skip this step. If you closed your terminal and logged back into Bridges-2, run:
```
module load anaconda3
```
2. Check that you do not have any environments installed
  ```
  conda env list
  ```
  - If you do not have any Conda environments created, you will likely see:
    - `base                  *  /opt/packages/anaconda3-2024.10-1'
  → Continue to Step 3.
  - If you do have an existing environment, you will see:
    - `qiime2-amplicon-2024.2     /jet/home/your-username/.conda/envs/qiime2-amplicon-2024.2`
    - `base                  *  /opt/packages/anaconda3-2024.10-1`
   → Skip to Step 5. 
3. Create a QIIME 2 environment: The installation may take several minutes. Wait until your terminal prompt reappears.

```bash
conda env create -n qiime2 --file https://raw.githubusercontent.com/qiime2/distributions/refs/heads/dev/2026.7/qiime2/released/rachis-qiime2-linux-64-conda.yml
```
4. Activate QIIME 2.

```bash
conda activate qiime2
```

Your terminal prompt should now begin with `(qiime2)`.

5. Check that QIIME 2 was installed successfully.

```bash
qiime info
```

    **(OPTIONAL) Troubleshooting: If QIIME2 Does Not Work**
    If QIIME2 does not work, there may be a Conda environment conflict.
    
    a. Check for Incomplete Installations
    ```
    ls -l /jet/home/your-username/.conda/envs/
    ```
    If you see an incomplete QIIME2 installation, proceed to the next step.
    b. Manually delete those installations
    ```
    rm -rf /jet/home/yourusername/.conda/envs/qiime2-amplicon-2024.2
    ```
    c. Clean Up Conda Cache
    ```
    conda clean --all -y
    ```
    d. Check how much disk space is being used by Conda's downloaded packages. Ideally, this should be less than 10 GB.
    ```
    du -sh ~/.conda/pkgs
    ```
    e. Reinstall QIIME2
    Repeat Steps 3-5 to reinstall QIIME2.

6. Deactivate the Conda environment for now. We will activate it again once our input files are ready.
```
conda deactivate
```

# Organize repository into folders
- Make sure your current directory `pwd` is
/ocean/projects/bio260081p/your-username
1. Create a folder by using the `mkdir` command
```
mkdir microbiomes
cd microbiomes
mkdir mock-data
```
2. cd into the newly created folder and download the data
```
cd mock-data
wget https://g-227ca.190ebd.75bc.data.globus.org/ibdmdb/raw/HMP2/16S/2018-01-08/206534.fastq.gz
wget https://g-227ca.190ebd.75bc.data.globus.org/ibdmdb/raw/HMP2/16S/2018-01-08/206536.fastq.gz
wget https://g-227ca.190ebd.75bc.data.globus.org/ibdmdb/raw/HMP2/16S/2018-01-08/206538.fastq.gz
```
3. Unzip the data. Note that sometimes when we run bioinformatics programs they can uncompress the files within the program, but other times we need to uncompress (unzip) them first.

```
gunzip *.fastq.gz
```

4. Use `less` to scroll through the file, and remember to press `q` when you want to stop looking at the file.
```
less 206534.fastq
```
## Ready to run some analysis
Request computer resources
```
interact -t 3:00:00 --ntasks-per-node=16 --mem=30G
```

## We will first perform quality control (QC) on our data
1. First we’ll be running fastqc, and to do that, we’ll first make a directory for the output to go:
```
cd .. #Goes one directory back (up one level)
mkdir fastqc_mock_out
```
2. Activate the multiqc environment (Loads fastqc and multiqc)
```
module load anaconda3
conda activate multiqc
```
3. Now we’ll run fastqc
```
fastqc -t 4 mock-data/*.fastq --outdir fastqc_mock_out
```
The arguments that we’re giving fastqc are:

- -t 4: the number of threads to use. Sometimes “threads” will be shown as --threads, --cpus, --processors, --nproc, or similar. Basically, developers of packages can call things whatever they like, but you can use the help documentation to see what options are available. We’re using 4 here because that’s the maximum that we have available. See below (htop) for how we find out about how many we have available.
- mock-data/*.fastq: the fastq files that we want to check the quality of.
- --outdir fastqc_mock_out: the folder to save the output to.

4. Next we’ll run multiqc. The name suggests it might be performing QC on multiple files, but it’s actually for combining the output together of multiple files, so we can run it like this:

```
multiqc fastqc_mock_out --filename multiqc_mock.html
```
So we’ve given as arguments:

- fastqc_mock_out: the folder that contains the fastqc output.
- --filename multiqc_mock.html: the file name to save the output as.

## Visualize QC file and answer the questions.
1. Add, Commit and Push multiqc_mock.html to your GitHub repository.
2.Download file and doeble click to open in web browser.

## Alternatively try the bridges-2 web interface locate and download your file.
https://ondemand.bridges2.psc.edu

# NOW LET'S CARRY OUT QC ON REAL DATA!
1. Create a symlink to the data like this, yup, you not always have to copy or move files around!
```
ln -s /ocean/projects/bio260081p/shared/week-7-data/raw_data .
```
2. Create a directory for the fastqc output:
```
mkdir fastqc_raw_data_out
```
3. Run fastqc
```
fastqc -t 4 raw_data/*.fastq.gz --outdir fastqc_raw_data_out
```
4. Run multiqc
```
multiqc fastqc_raw_data_out --filename multiqc_raw_data.html
```
5. Inspect the output file

## Let's run Quimme to study the microbiome
1. Create a Symlink for the metadata file from the share folder
```
ln -s /ocean/projects/bio260081p/shared/week-7-data/Blueberry_metadata_reduced.tsv .
```
2. Inspect the metadata file
```         
head Blueberry_metadata_reduced.tsv
```
- Answer questions 11 and 12

## A few formatting steps required by quimme
1. Lwt's create the following directory
```         
mkdir reads_qza
```
2. You had installed the latest version of QIIME2, so you can  activate that environment with the command below.
Deactivate multiqc first
```
conda deactivate
```
Now activate qiime2
```         
conda activate qiime2
```

3. Feed qiime the raw reads
```         
qiime tools import \
  --type SampleData[PairedEndSequencesWithQuality] \
  --input-path raw_data/ \
  --output-path reads_qza/reads.qza \
  --input-format CasavaOneEightSingleLanePerSampleDirFmt
```
- cassava format, files follow a pattern `SampleID_L001_R1_001.fastq.gz`

4. remove primer sequences from reads
```
qiime cutadapt trim-paired \
  --i-demultiplexed-sequences reads_qza/reads.qza \
  --p-cores 4 \
  --p-front-f ACGCGHNRAACCTTACC \
  --p-front-r ACGGGCRGTGWGTRCAA \
  --p-discard-untrimmed \
  --p-no-indels \
  --o-trimmed-sequences reads_qza/reads_trimmed.qza \
  --o-stats reads_qza/cutadapt_stats.qza
```
5 Visualize your data now
```         
qiime demux summarize \
  --i-data reads_qza/reads_trimmed.qza \
  --o-visualization reads_qza/reads_trimmed_summary.qzv
```
- Download file from bridges2 on demand
- OR Git add, commit and push `reads_trimmed_summary.qzv`
- Download and open in https://view.qiime2.org/

### Denoising reads
1. Join pair-end reds
```         
qiime vsearch merge-pairs \
  --i-demultiplexed-seqs reads_qza/reads_trimmed.qza \
  --output-dir reads_qza/reads_joined
```
2. Filter out low-quality reads
```         
qiime quality-filter q-score \
  --i-demux reads_qza/reads_joined/merged_sequences.qza \
  --o-filter-stats filt_stats.qza \
  --o-filtered-sequences reads_qza/reads_trimmed_joined_filt.qza
```
3. Summarize results
```         
qiime demux summarize \
  --i-data reads_qza/reads_trimmed_joined_filt.qza \
  --o-visualization reads_qza/reads_trimmed_joined_filt_summary.qzv
```
- Git add, commit and push `reads_trimmed_joined_filt.qzv` Download and open in https://view.qiime2.org/
- Answer question 13
4. Actual denoising with deblur

```         
qiime deblur denoise-16S \
  --i-demultiplexed-seqs reads_qza/reads_trimmed_joined_filt.qza \
  --p-trim-length 390 \
  --p-sample-stats \
  --p-jobs-to-start 4 \
  --p-min-reads 1 \
  --output-dir deblur_output
```
Note: This command may take about 10 minutes to run. Keep your computer awake and prevent it from going to sleep, as this may disconnect you from the HPC session and interrupt the process.

4. Summarize dublur output
```         
qiime feature-table summarize \
  --i-table deblur_output/table.qza \
  --o-summary deblur_output/deblur_table_summary.qzv \
  --o-feature-frequencies deblur_output/feature_frequencies.qza \
  --o-sample-frequencies deblur_output/sample_frequencies.qza

```
- Git add, commit and push `deblur_table_summary.qzv` Download and open in https://view.qiime2.org/

###  Run taxonomic classification
1. Create symlink to taxonomic data
```
ln -s /ocean/projects/bio260081p/shared/week-7-data/taxa .
```
2. Run taxonomic classification (Homework)
```         
qiime feature-classifier classify-sklearn \
  --i-classifier /ocean/projects/bio260081p/shared/week-7-data/SILVA_144_SSURef_NR99_weighted_classifier_full-length_plant-rhizosphere.qza \
  --i-reads deblur_output/representative_sequences.qza \
  --o-classification taxa/classification.qza \
  --p-n-jobs 4
```
### Filtering resultant table
1. Filter out rare ASVs
```         
qiime feature-table filter-features \
  --i-table deblur_output/table.qza \
  --p-min-frequency 10 \
  --p-min-samples 1 \
  --o-filtered-table deblur_output/deblur_table_filt.qza
```
- replace with desired frequency:
- --p-min-frequency 10: Keep ASVs detected at least 10 times across all 12 samples combined.
- --p-min-samples 1: Allow an ASV to remain even if it occurs in only one sample.

2. Filter out contaminant and unclassified ASVs
```         
qiime taxa filter-table \
  --i-table deblur_output/deblur_table_filt.qza \
  --i-taxonomy taxa/classification.qza \
  --p-include p__ \
  --p-exclude mitochondria,chloroplast \
  --o-filtered-table deblur_output/deblur_table_filt_contam.qza
```
3. Subset and summarize filtered table
```         
qiime feature-table summarize \
  --i-table deblur_output/deblur_table_filt_contam.qza \
  --o-summary deblur_output/deblur_table_filt_contam_summary.qzv \
  --o-feature-frequencies deblur_output/contam_feature_freq.qza \
  --o-sample-frequencies deblur_output/contam_sample_freq.qza
```
- Git add, commit and push `deblur_table_filt_contam_summary.qzv` Download and open in https://view.qiime2.org/
- Answer question 16 What is the minimum and maximum sequencing depth across all samples?

4. Copy final table to current directory
```         
cp deblur_output/deblur_table_filt_contam.qza deblur_output/deblur_table_final.qza
```
5. Produce final table summary
```         
qiime feature-table filter-seqs \
  --i-data deblur_output/representative_sequences.qza \
  --i-table deblur_output/deblur_table_final.qza  \
  --o-filtered-data deblur_output/rep_seqs_final.qza
```
```         
qiime feature-table summarize \
  --i-table deblur_output/deblur_table_final.qza \
  --o-summary deblur_output/deblur_table_final_summary.qzv \
  --o-feature-frequencies deblur_output/final_feature_freq.qza \
  --o-sample-frequencies deblur_output/final_sample_freq.qza
```

- Git add, commit and push `deblur_table_final_summary.qzv` Download and open in https://view.qiime2.org/

# VISUALIZATION
1. Create a phylogenetic tree
```         
qiime phylogeny align-to-tree-mafft-fasttree \
  --i-sequences deblur_output/rep_seqs_final.qza \
  --o-alignment deblur_output/aligned-rep-seqs.qza \
  --o-masked-alignment deblur_output/masked-aligned-rep-seqs.qza \
  --o-tree deblur_output/unrooted-tree.qza \
  --o-rooted-tree asvs-tree.qza
```
2. Generate rarefaction curves
```         
qiime diversity alpha-rarefaction \
  --i-table deblur_output/deblur_table_final.qza \
  --p-max-depth X \
  --p-steps 20 \
  --i-phylogeny asvs-tree.qza \
  --o-visualization rarefaction_curves.qzv
```
replace X with minimum frequency per sample 4500(?)
- Answer question 17

2. Calculating diversity metrics.  replace X with min freq
```         
qiime diversity core-metrics-phylogenetic \
  --i-table deblur_output/deblur_table_final.qza \
  --i-phylogeny asvs-tree.qza \
  --p-sampling-depth X  \
  --m-metadata-file Blueberry_metadata_reduced.tsv \
  --p-n-jobs-or-threads 4 \
  --output-dir diversity
```
3. Produce boxplot for shannon
```         
qiime diversity alpha-group-significance \
  --i-alpha-diversity diversity/shannon_vector.qza \
  --m-metadata-file Blueberry_metadata_reduced.tsv \
  --o-visualization diversity/shannon_compare_groups.qzv
```
4. Generate stacked bar chart of taxa relative abundances
```         
qiime taxa barplot \
  --i-table deblur_output/deblur_table_final.qza \
  --i-taxonomy taxa/classification.qza \
  --m-metadata-file Blueberry_metadata_reduced.tsv \
  --o-visualization taxa/taxa_barplot.qzv
```

