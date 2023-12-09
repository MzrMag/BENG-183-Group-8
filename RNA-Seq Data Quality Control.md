# FASTP  

## FASTP Overview  
Fastp is a tool designed for preprocessing FASTQ files prior to downstream analysis. Its features include quality control, adapter trimming, quality filtering, per-read quality pruning and many other functions to provide clean data.  

## FAST Workflow  
![Image](fastp_workflow.jpg)

### Main workflow:  
* Fastp parallelly process the input fastq files using multi-threading techniques to improve the efficiency.  
* It loads reads from FASTQ files and groups them into packs.  
* There is a one-on-one relationship between each pack of reads and the thread assigned to process it.  
  * Within each thread, each read from the pack is individually processed.
* Each thread operates independently to process its subset of reads and store reads’ statistical information in its own environment for later use.
  * This information may include per-cycle quality profiles, per-cycle base contents, results from adapter trimming and k-mer counts.
* After all the reads have been processed, these statistical information will be merged together.
* Fastp’s reporter will generate reports in both HTML (visualization) and JSON formats.
  * Reports provide both pre-filtering and post-filtering statistical information for comparison purposes. 
