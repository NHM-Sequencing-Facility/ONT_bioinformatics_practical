---
title: "PART 4: Quality control of sequence data"
layout: default
nav_order: 7
---

# PART 4: Quality control of sequence data

---

As you know, ONT library preparation results in the addition of a sequencing adapter at each end of the fragment so the DNA fragment can be carried by the motor protein to the nanopore. For downstream analysis, it is important to remove these adapters.

For this we will use [Porechop](https://github.com/rrwick/Porechop). Among many other things, this program can process all of the reads in our basecalled FASTQ file and remove these adapter sequences. Furthermore, the ligation library preparation process can result in conjoined reads (also called [chimeras](https://en.wikipedia.org/wiki/Chimera_(molecular_biology))), meaning an adapter will be found in the middle of an extra-long read. Porechop will also identify these, split the read in two and remove the adapters. If you use a multiplexing kit to maximise sample throughput (as we have done), this program can also split the reads based on the molecular barcode added to each sample. This has already been done by the minKNOW software, and so isn't necessary here.

Make sure you are back in your practical directory again (you can navigate back home using `cd ~/ONT_practical`, or by typing `cd ../..` to move back up 2 directories).

Before starting the trimming process, let's first make another directory to store our trimmed sequence data.

```bash
mkdir trimmed_amplicon
```

Then run Porechop on our 'raw' sequence data and output the adapter-trimmed sequences to our newly created `trimmed_amplicon` directory. `--verbosity` controls how extensive the logging is. `1` is suitable here, and allows us to see how Porechop is progressing with the adapter trimming. This might take 5 minutes to run.

```bash
porechop -i raw_reads/raw_reads.fastq.gz -o trimmed_amplicon/trimmed_reads.fastq --verbosity 1
```

{: .note }
**[Q]**  
Given the logging that was output to the terminal,
1. How many reads were processed?
2. How many reads had adapters trimmed from the start, and how many from the end?
3. How many reads were split in half as they were determined to be chimeras?

Now we have trimmed all of our sequences to remove any remaining adapters and split any chimeric sequences, we'll use another QC tool called [Chopper](https://github.com/wdecoster/chopper) to trim and filter our adapter-trimmed sequences based on quality and length, as short and low quality reads can make things difficult later on.

Here we'll set `-q`, the minimum Phred (Q) quality score of each read to 9, so any read < Q9 is removed. We'll also set `--minlength` to 200 so reads < 200 bp are removed.

```bash
chopper -q 9 --minlength 200 --threads 4 --input trimmed_amplicon/trimmed_reads.fastq > trimmed_amplicon/clean_reads.fastq
```

{: .note }
**[Q]**  
Given the logging that was output to the terminal,
1. How many reads were kept based on their quality thresholds?
2. How many reads were removed?