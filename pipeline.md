Had to convert sra to fastq (should saying this in the methods section?) maybe use it for the discussion section

docker pull --platform linux/amd64 ncbi/sra-tools

docker run --rm --platform linux/amd64 \
  -v "$PWD":/work -w /work \
  ncbi/sra-tools \
  fasterq-dump data/SRR32410565.sra -O data --threads 8

### QUALITY CONTROL ###
docker run --rm --platform linux/amd64 \
  -v "$PWD":/work -w /work \
  quay.io/biocontainers/nanoplot:1.46.2--pyhdfd78af_0 \
  NanoPlot \
    --fastq data/SRR32410565.fastq \
    --outdir qc/nanoplot \
    --threads 8

Open the html output produced by NanoPlot
open qc/nanoplot/NanoPlot-report.html

Raw reads were assessed using NanoPlot, which reported a read length N50 of 4,683 bp and a mean read quality of Q18.9. Read length and quality distributions indicated long, variable-length reads without large clusters of low-quality sequences. As a result, aggressive read filtering was not applied in order to preserve coverage and long-read continuity. Downstream polishing was performed to mitigate residual Nanopore-specific basecalling errors.

# Flye #
docker run --rm --platform linux/amd64 \
  -v "$PWD":/work -w /work \
  staphb/flye:2.9.6 \
  flye \
    --nano-hq data/SRR32410565.fastq \
    --genome-size 5m \
    --threads 8 \
    --out-dir assembly/flye

De novo genome assembly was performed using Flye on Oxford Nanopore long-read data, producing four contigs with a total assembled length of 5.11 Mb, consistent with the expected genome size of Salmonella enterica.

### Reference Alignment ###
docker run --rm --platform linux/amd64 \
  -v "$PWD":/work -w /work \
  quay.io/biocontainers/minimap2:2.28--he4a0461_0 \
  minimap2 -ax asm5 \
    ref/reference.fasta \
    assembly/flye/30-contigger/contigs.fasta | \
  docker run --rm --platform linux/amd64 -i \
    -v "$PWD":/work -w /work \
    quay.io/biocontainers/samtools:1.20--h50ea8bc_0 \
    samtools sort -o align/asm_to_ref.bam

BAM Indexing 
docker run --rm --platform linux/amd64 \
  -v "$PWD":/work -w /work \
  quay.io/biocontainers/samtools:1.20--h50ea8bc_0 \
  samtools index align/asm_to_ref.bam

Assembled contigs were aligned to a curated Salmonella enterica reference genome obtained from the NCBI RefSeq database (GCF_000006945.2) using minimap2 with the asm5 preset. Resulting alignments were sorted and indexed using samtools for downstream visualization and interpretation. This step answers structural questions. 

### Aligning for Variants ###
docker run --rm --platform linux/amd64 \
  -v "$PWD":/work -w /work \
  quay.io/biocontainers/minimap2:2.28--he4a0461_0 \
  minimap2 -ax map-ont \
    ref/reference.fasta \
    data/SRR32410565.fastq | \
  docker run --rm --platform linux/amd64 -i \
    -v "$PWD":/work -w /work \
    quay.io/biocontainers/samtools:1.20--h50ea8bc_0 \
    samtools sort -o align/reads_to_ref.bam

### Indexing 
docker run --rm --platform linux/amd64 \
  -v "$PWD":/work -w /work \
  quay.io/biocontainers/samtools:1.20--h50ea8bc_0 \
  samtools index align/reads_to_ref.bam

Homozygous SNP: NC_003197.2:1,695,111-1,695,161

### Variant Calling ###

Variant calling using Clair3 identified 10,268 candidate variants across chromosomal and plasmid contigs relative to the Salmonella enterica Typhimurium LT2 reference genome.

While many variants likely reflect true strain-level divergence, a subset of calls—particularly small indels—are consistent with residual Oxford Nanopore sequencing error, especially in homopolymeric regions.

# Create output directory
mkdir -p variants/clair3

# Run Clair3 (ONT, bacterial fine-tuned model, include chromosome + plasmid contigs)
docker run --rm --platform linux/amd64 \
  -v "$PWD":/work -w /work \
  hkubal/clair3:latest \
  bash --noprofile --norc -lc '
set -e
source /opt/conda/etc/profile.d/conda.sh
conda activate clair3

/opt/bin/run_clair3.sh \
  --bam_fn=align/reads_to_ref.bam \
  --ref_fn=ref/reference.fasta \
  --threads=8 \
  --platform=ont \
  --model_path=/opt/models/r1041_e82_400bps_sup_v430_bacteria_finetuned \
  --include_all_ctgs \
  --output=variants/clair3
'

Variants were called from Oxford Nanopore read alignments using Clair3 with an ONT bacterial fine-tuned model, with calling enabled across all contigs, and results were written to a merged VCF.