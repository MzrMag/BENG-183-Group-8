# Raw Data

## FASTQ file

FASTQ is a data file format that is used to store sequencing data and quality scores. It is used to contain raw data generated from NGS sequencing technologies. So the RNA-seqencing data is usually contained in a FASTQ file.

Here is a sample FASTQ file:
![image](fastq_file.png)

In the FASTQ file, each read is consist of four lines. 
* The first line is the sequence header which starts with an "@" (not a ">")!.
  * From the leading "@" to the first whitespace character is considered as the sequence identifier, which consist information about the cluster and sequence run (like flow cell lane, flow cell ID, run ID, instrument name).
  * Then the contents after the first whitespace are the sequence description.
* The second line is the sequence or the base-calls in standard letter code (A, C, G, T, N).
* The third line starts with a "+" and can have the same sequence identifier after, but usually just simply a "+" as a separator.
* The fourth line are the quality scores (Phred quality score for the sequence, encoded in ASCII characters).

## Phred Score



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

### Paired-end processing workflow：
*	UMI(Unique Molecular Identifiers) preprocessing: Process data containing UMIs by making the UMI to be the read’s identifier.  
*	Global trimming: trim all reads in the front or the tail.  
*	Sliding window cutting: As the window slide from either 5′end to 3′end or from 3′end to 5′, fastp evaluate the mean quality score within the window and drop the low-quality bases in each read’s head and tail.  
*	PolyG tail trimming: polyG tail is an issue for Illumina NextSeq or NovaSeq when T or C is misidentified as G by sequencers. By determine the data sequencers using flow cell identifier, fastp automatically removes polyG tail.  
*	Based correction: If fastp detects substantial overlap between one pair of reads, then it compares the bases within the overlapped region and performs a correction when the total number of mismatches is under a specified threshold.  
*	Adapter trimming: fastq automatically cut adapters for both single-end and paired-end Illumina data.  
    * For SE: fastq check the ends of the reads and identifying sequences that occur frequently across many reads as adapters.  
    * For PE: finding the overlap between each pair of reads enables the detection of adapter sequences.  
*	Filtering: reads with low quality, high proportions of N contents, or length under certain thresholds will be removed.

 ## Result
 ![Image](fastp_result.jpg)
As the figure demonstrates, the curve for base G is abnormal before processing and the quantity of G does not match that of cytosine (C) which violates Chargaff's rule. After fastp processing, the curve for G gets normalized and the proportions of G and C matches.  
 ![Image](fastp_report.png)  
 The figure demonstrates the pre-filtering and post-filtering statistical values for a sample data. 363.76K reads with low quality is removed as well as 34.8K reads that are too short.

