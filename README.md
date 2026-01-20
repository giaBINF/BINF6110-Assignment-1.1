# **Introduction**
Genomic sequencing, more specifically whole genome sequencing (WGS), is a cornerstone of modern medicine and an essential tool across many scientific fields. Recent technological advances have improved the cost, speed, and accuracy of sequencing platforms, enabling the development of higher throughput second and third generation technologies.<sup>1,2</sup> These platforms generate either short DNA reads (Illumina), or long DNA reads (Oxford Nanopore) each with distinct trade offs in read length, accuracy, and analytical applications. Short read sequencing produces highly accurate reads typically ranging from 35 to 1000 base pairs, whereas long read sequencing generates reads spanning several to tens of kilobases.<sup>1</sup> However, when research objectives prioritize genome assembly and structural resolution, long read sequencing offers clearer advantages.<sup>3</sup> This study applies whole genome sequencing to *Salmonella enterica*, a bacterial pathogen for which comprehensive genome reconstruction is critical for downstream genomic analyses.

## **Genome Assmebly**
Long read sequencing has improved genome assembly by enabling more accurate reconstruction of complex genomes, particularly in the presence of genomic diversity relevant to health and disease.<sup>4</sup> During genome assembly, overlapping reads are computationally joined to form contiguous sequences, or contigs, that represent continuous regions of the genome. In bacterial genomes such as *Salmonella enterica*, repetitive elements and plasmids complicate accurate genome reconstruction from short sequencing reads, whereas long read sequencing can capture these features within single reads.<sup>3,5</sup> Therefore, despite higher per base error rates, long read sequencing provides a more complete representation of bacterial genome structure relevant to virulence, antimicrobial resistance, and transmission.<sup>6</sup>

## **Alignment**
Following genome assembly, contigs generated from long read sequencing are aligned to reference genomes to assess assembly accuracy and identify structural variation. Although long reads improve genome reconstruction by spanning repetitive and structurally complex regions, alignment of nanopore long-read assemblies is complicated by high and uneven error rates. Error-prone regions, especially those enriched for insertions and deletions, can interfere with alignment algorithms and reduce mapping confidence.<sup>1</sup> These challenges necessitate appropriate error correction and alignment strategies for reliable downstream analysis.<sup>7</sup>

# **Methods**
## Long-read data
Oxford Nanopore Technology (ONT) long-read whole genome sequencing data for *Salmonella enterica* were used in this study. Raw reads consisted of single molecule DNA fragments with read lengths ranging from several kilobases to tens of kilobases. Base called reads were generated with an expected per base accuracy of approximately Q20+ N50:15kb.

## **Genome Assembly**
Read quality was assessed using NanoPlot v1.41.0, and low-quality reads and contigs shorter than 500 bp were removed. Genome assemblies were generated using Flye v2.9.2 and polished with Medaka v1.7.3.6 Polishing changes were inspected using the Integrated Genomics Viewer v2.15.4 to verify corrections.3 Polished assemblies were further evaluated by mapping the original reads back to the assembly using minimap2 v2.24 to confirm completeness and accuracy.














# **Reference List**
1. 	Brlek P, Bulić L, Bračić M, Projić P, Škaro V, Shah N, et al. Implementing Whole Genome Sequencing (WGS) in Clinical Practice: Advantages, Challenges, and Future Perspectives. Cells. 2024 Jan;13(6):504. 
2. 	Guiglielmoni N. De novo Genome Assembly Using Long Reads and Chromosome Conformation Capture. In: Bonizzoni M, Ometto L, editors. Insect Genomics: Methods and Protocols [Internet]. New York, NY: Springer US; 2025 [cited 2026 Jan 15]. p. 1–27. Available from: https://doi.org/10.1007/978-1-0716-4583-3_1 
3. 	Wick RR, Judd LM, Holt KE. Assembling the perfect bacterial genome using Oxford Nanopore and Illumina sequencing. PLOS Comput Biol. 2023 Mar 2;19(3):e1010905. 
4. 	Szakállas N, Barták BK, Valcz G, Nagy ZB, Takács I, Molnár B. Can long-read sequencing tackle the barriers, which the next-generation could not? A review. Pathol Oncol Res. 2024 May 16;30:1611676. 
5. 	Kitchens SR, Wang C, Price SB. Bridging Classical Methodologies in Salmonella Investigation with Modern Technologies: A Comprehensive Review. Microorganisms. 2024 Nov;12(11):2249. 
6. 	Schiffer AM, Rahman A, Sutton W, Putnam ML, Weisberg AJ. A comparison of short- and long-read whole-genome sequencing for microbial pathogen epidemiology. mSystems. 10(12):e01426-25. 
7. 	Chen Y, Nie F, Xie SQ, Zheng YF, Dai Q, Bray T, et al. Efficient assembly of nanopore reads via highly accurate and intact error correction. Nat Commun. 2021 Jan 4;12(1):60. 

 
 