---
title: Running BLAST from the command line to identify environmental sequences
layout: post
date: 2015-06-25
category: null
comments: true
tags: []
---

# Introduction
OK, your first introduction to the use and abuse of command line tools is... BLAST! That's right, the [Basic Local Alignment Search Tool](http://en.wikipedia.org/wiki/BLAST)!

Let's assume that all of you have used the NCBI BLAST Web page to do individual searches. Today we'll automate batch searches at the command line on your own computer. This is a technique that works well for small-to-medium sized sequencing data sets. The various database (nr, nt) are getting big enough that it's reasonably time consuming to search them on your own, although of course you can do it if you want – you might just have to wait a while for things to finish.

Before I forget, let me say that there are a lot of tips and tricks for working at the UNIX command line that I'm going to show you, so even if you've used command line BLAST before, you should skim along.

First, let's check and see if we have BLAST.

```
which blastall
```

If you have BLAST installed and in your path (BLAST may be installed but not in your path), it will give you something like this:

```
/opt/blast/bin/blastall
```

If you don't have BLAST (you should have it in your QIIME or mothur paths), then you will need to install it.

## Installing BLAST
To install the BLAST software, you need to download it from NCBI, unpack it, and copy it into standard locations:

```
curl -O ftp://ftp.ncbi.nih.gov/blast/executables/release/2.2.24/blast-2.2.24-ia32-linux.tar.gz
tar xzf blast-2.2.24-ia32-linux.tar.gz
sudo cp blast-2.2.24/bin/* /usr/local/bin
sudo cp -r blast-2.2.24/data /usr/local/blast-data
```

## Download the databases
Now, we can't run BLAST without downloading the databases. Let's start by doing a BLAST of some sequences from an environmental sequencing project (not telling you from what yet). For this you'll need the nt db.  This, like a lot of NCBI databases is huge, so I don't suggest putting this on your laptop unless you have a lot of room.  It's best on a larger computer (HPCC, Amazon machine, that you have access to).  I wouldn't install this database unless you know you have room on your computer.

```
wget https://s3.amazonaws.com/edamame/EDAMAME_MG.tar.gz
```

Now you've got these files. How big are they?

```
ls -l *.gz
```

These are large files and they are going to be even larger when you uncompress them.

```
tar xzf *.tar.gz
```

Now, you can see the CentraliaMG_7GB.fastq file in the EDAMAME_MG folder.

```
cd EDAMAME_MG
ls
```

Before we start a BLAST of all of our sequences, we need to make sure our blast is working.  To do this, we want to start with something small (~100 sequences). Let's take a few sequences off the top of the Centralia metagenome data set:

```
grep -n 300 CentraliaMG_7GB.fastq > example_MG.fastq
```

Here, the program 'head' takes the first ten lines from that file, and the `>` tells UNIX to put them into another file, `example_MG.fastq`.

However, for running BLAST, we need fasta formatted file, instead of fastq format. So, we should convert our example_MG.fastq file to fasta format. You can also re-format fastq file using QIIME command, 'convert_fastaqual_fastq.py', but, in here, we would like to use fastq_to_fasta program.

```
fastq_to_fasta -i example_MG.fastq -o example_MG.fasta
```

Can you find converted fasta file in your folder? Now, we are ready to BLAST!!!


Now try a BLAST:

```
blastall -i example_MG.fasta -d nt -p blastn
```

You can do three things at this point.

First, you can scroll up in your terminal window to look at the output.  

Second, you can save the output to a file:

```
blastall -i nt-first.fasta -d nt -p blastn -o out.txt
```

and then use 'less' to look at it:

```
less out.txt
```

and third, you can pipe it directly to less, by which I mean you can send all of the output to the 'less' command without saving it to a file:

```
blastall -i nt-first.fasta -d nt -p blastn | less
```

## Different BLAST options
BLAST has lots and lots and lots of options. Run 'blastall' by itself to see what they are. Some of the most useful ones are `-v`, `-b`, and `-e`.