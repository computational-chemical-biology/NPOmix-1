# NPOmix

## Overview

To use the NPOmix approach (Fig. 1, schematic example for the approach used in only four samples), you will need a dataset of so-called paired genome-MS/MS samples, samples that contain both a genome (or metagenome) and a group of MS/MS spectra obtained via untargeted LC-MS/MS. Many paired datasets are available at the Paired omics Data Platform [(PoDP)](https://pairedomicsdata.bioinformatics.nl), one of the first initiatives to gather paired genome-MS/MS samples. By applying [BiG-SCAPE](https://bigscape-corason.secondarymetabolites.org), each biosynthetic gene cluster (BGC) in the genomes will go through a pairwise similarity comparison (Fig. 1A) to every other BGC in the same set of genomes to compute similarity scores (1 minus BiG-SCAPE raw distance) and to assign BGCs to Gene Cluster Families (GCFs), if possible. In order to create a BGC fingerprint (Fig. 1C), we identify the maximum similarity of the query BGC to one of the many BGCs in each genome (which can be considered a pool of BGCs) in the dataset. Therefore, the BGC fingerprints can be represented as a row of values (a vector with the maximum similarity scores) and each column is a different genome from the selected dataset. Similarity scores range from 0.0 to 1.0, for instance, identical BGCs have a perfect similarity score of 1.0, a score of 0.8 would represent that a homologous BGC is present in the genome and the score of 0.0 (or below the similarity cutoff of 0.7) represents that the queried BGC is absent in the genome. A similar process happens to create MS/MS fingerprints (Fig. 1B), however, genomes are replaced by MS/MS spectra and a query BGC is replaced by a query MS/MS spectrum; either a reference spectrum from [GNPS](https://gnps.ucsd.edu/ProteoSAFe/static/gnps-splash.jsp) or a cryptic MS/MS spectrum from a new sample (that contains a genome and experimental MS/MS spectra). In the case of MS/MS fingerprints (Fig. 1D), we used GNPS to calculate the pairwise modified cosine score and then identify the maximum similarity of the query MS/MS spectrum to one of the many MS/MS spectra in each experimental sample. Of particular note, we did not used the full classical GNPS molecular networking capabilities, we only used the functions required to calculate a modified cosine similarity score between a pair of MS/MS spectra. The BGC fingerprints create a training matrix (Fig. 1E) where rows are the maximum similarity scores, normally thousands of rows (e.g., for our first release, round 4, we have used 5,421 BGCs in 1,006 genomes/metagenomes), where each genome is a column. This matrix can be fed to the k-nearest neighbor (KNN) algorithm in order to train it with the genomic data. Additionally, one extra column is required in this genomic data matrix, a column that labels each BGC fingerprint with a GCF so the KNN algorithm will know the fingerprint patterns that belong together. The KNN algorithm plots the BGC fingerprints in the KNN space (in Fig. 1G, the KNN space is exemplified by only 2 dimensions). Next, the MS/MS fingerprints also form a testing matrix (Fig. 1F), in this case, this matrix also containing 1,006 columns due to the 1,006 sets of paired experimental MS/MS spectra. For example, for our first release, this testing matrix contained 18 reference MS/MS fingerprints (rows) for MS/MS reference spectra from the GNPS database. Each query MS/MS fingerprint (a row in the testing metabolomic matrix and columns are the experimental MS/MS spectra per sample) will also be plotted into the same KNN space (Fig. 1G) so the algorithm can obtain the GCF labels for the k-nearest neighbors to the query MS/MS fingerprint (e.g., for three most similar BGC neighbors, k = 3). We note that GCF labels can be present more than once in the returned list if two or more BGC nearest neighbors belong to the same gene family. This repetition on the GCF classification is a common behavior of the KNN approach. Our approach is suitable for bacterial, fungal, algal and plant genomes and MS/MS spectra. Metagenomes and metagenome-assembled genomes can also be used instead of genomes, however, complete genomes are preferred. This KNN approach also supports LC-MS/MS from fractions or from different culture conditions; multiple LC-MS/MS files for the same genome were merged together into a single set of experimental MS/MS spectra.

![Fig1_part1](https://github.com/tiagolbiotech/NPOmix/blob/main/Screen%20Shot%202021-06-23%20at%201.35.17%20PM.png)

![Fig1_part2](https://github.com/tiagolbiotech/NPOmix/blob/main/Screen%20Shot%202021-06-23%20at%201.35.53%20PM.png)

## Rounds of test

Number of samples:
```
Round 1 - 60 cyanobacterial samples
Round 2 - 129 PoDP genomes downloaded automatically
Round 3 - 129 PoDP genomes downloaded automatically + 450 metagenomes (a total of 579)
Round 4 - 326 PoDP genomes (downloaded automatically and manually) + 680 metagenomes (a total of 1006)
```
Number of validated links:
```
Round 1 - 39 BGC-MS/MS links (1 correct link)
Round 2 - 1 BGC-MS/MS links (0 correct link)
Round 3 - 16 BGC-MS/MS links (8 correct links)
Round 4 - 18 BGC-MS/MS links (9 correct links)
```
