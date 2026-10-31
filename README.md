# 16S rRNA Microbiome Research Training

**Li Laboratory · Department of Chemistry and Biochemistry · Florida State University**

Microbiome science is increasingly important in modern biology, yet hands-on sequencing-based education remains out of reach for many undergraduate programs due to high costs and technical complexity. This repository contains materials for an inquiry-based 16S rRNA gene sequencing curriculum that enables undergraduate students to conduct complete, independent microbiome research projects, from sample collection through bioinformatic analysis, at approximately **$13 per sample**.

The curriculum was developed and piloted at Florida State University with students majoring in Biochemistry and Biological Science. Students designed their own research questions, collected environmental samples from self-selected locations, performed all molecular laboratory steps, analyzed sequencing data using QIIME 2, and communicated findings through scientific poster presentations. Assessment data showed strong learning gains across microbiome concepts, molecular biology techniques, bioinformatics, and scientific communication, with all participants expressing increased interest in pursuing future research in microbiology or bioinformatics.

Two cost-reduction strategies make the workflow accessible to teaching labs with limited budgets:

- **Centrifugation-based cell collection**: microbial cells are pelleted directly from water or surface swab buffer, eliminating the need for membrane filtration or commercial extraction kits
- **Surfactant-assisted direct PCR**: cells are lysed *in situ* during PCR amplification using a detergent-assisted thermal lysis approach [[1]](#references), removing the DNA extraction step entirely while maintaining sufficient template quality for high-throughput sequencing

Student projects have characterized microbial communities in campus water sources, gym equipment surfaces, and other local environments, generating publication-quality datasets on the Illumina MiSeq platform.

---

## Repository Contents

| File | Description |
|---|---|
| `Student-Guide-Research-Question-and-Sampling.docx` | Student guide for choosing a research question and samples, including permitted sample types, biosafety precautions, and the project approval process |
| `Microbiome_Research_Project_Proposal_Template.docx` | Project proposal template students complete (research question, hypothesis, sampling plan, controls, biosafety, timeline) before any sampling begins |
| `Microbial_community_analysis_wetlab_guide.docx` | Step-by-step laboratory workflow (cell collection, direct PCR, bead cleanup, index PCR, library purification) plus guidance for preparing QIIME 2 manifest and metadata files |
| `16S_rRNA_Colab_Notebook.ipynb` | Google Colab notebook for QIIME 2 analysis (import, DADA2, taxonomy, diversity, ANCOM-BC) |
| `master_pipeline.sh` | Command-line QIIME 2 script that runs the same analysis end to end (import, primer trimming, DADA2, taxonomy, phylogeny, diversity, ANCOM-BC) on a workstation or server |
| `SI_Cost.xlsx` | Itemized cost breakdown per sample and per class cohort |

---

## Getting Started

The materials are designed to be used in this order:

1. **Choose a question and samples.** Read the student guide, then complete the project proposal template. Projects use environmental samples only (surfaces, water, soil, air), and the instructor approves each proposal before sampling begins.
2. **Run the wet lab workflow.** Follow the wet lab guide from sample collection through library preparation and sequencing submission.
3. **Analyze the data.** Prepare the manifest and metadata files as described in the wet lab guide, then run either the Colab notebook (no installation) or the command-line script (`master_pipeline.sh`).

### Wet Lab Workflow

The workflow in `Microbial_community_analysis_wetlab_guide.docx` uses:

- **Direct PCR** from an environmental swab or water pellet, with no DNA extraction required [[1]](#references)
- **515F / 806R primers** [[2,3]](#references) targeting the 16S rRNA gene with Illumina adapter overhangs [[4]](#references)
- **Two-step barcoding PCR** for indexed library preparation [[4]](#references)
- **AMPure XP bead cleanup** with Qubit and TapeStation quality control
- **Illumina MiSeq** sequencing through a commercial provider or core facility

### Bioinformatic Analysis

You can analyze your sequencing data in either of two ways. Both use the same QIIME 2 workflow, so choose whichever fits your setup.

| | Option A: Colab notebook | Option B: Command-line script |
|---|---|---|
| File | `16S_rRNA_Colab_Notebook.ipynb` | `master_pipeline.sh` |
| Where it runs | Google Colab in a web browser | A Linux workstation or server with QIIME 2 installed |
| Installation | None | QIIME 2 must already be installed and activated |
| Best for | Classes and first-time users | Larger datasets, repeat analyses, and users comfortable with the command line |

#### Option A: Google Colab notebook

Open `16S_rRNA_Colab_Notebook.ipynb` in Google Colab:

1. Go to [colab.research.google.com](https://colab.research.google.com)
2. Click **File → Open notebook → GitHub**
3. Paste this repository URL and select the notebook
4. Mount your Google Drive when prompted. This is where your FASTQ files and results will be stored
5. Run cells sequentially from top to bottom

No local software installation is required. QIIME 2 [[5]](#references) (2024.5) and all dependencies install automatically during the Environment Setup section (~15–20 minutes on first run).

| Section | Steps |
|---|---|
| Environment Setup | Installs Miniforge, QIIME 2 2024.5, and Python packages |
| User Configuration | Set your file paths, primer sequences, and truncation lengths here |
| FASTQ Import & QC | Imports paired-end reads, generates quality plots |
| DADA2 Denoising | Quality filtering, denoising, chimera removal, ASV table |
| Taxonomic Classification | SILVA 138 classifier [[6,7]](#references), taxonomic bar plots |
| Diversity Analysis | Alpha diversity, beta diversity, PCoA, PERMANOVA, rarefaction |
| Differential Abundance | ANCOM-BC [[8]](#references) for group comparisons |
| Results Export | Downloads figures and summary tables to Google Drive |

#### Option B: Command-line script

`master_pipeline.sh` runs the full analysis in one command and writes each step to its own numbered folder. It is useful when you want to re-run an analysis with different settings or process data on a server.

**Before you run it**

1. Install QIIME 2 [[5]](#references) and activate its environment (the script uses the cutadapt, DADA2, feature-classifier, phylogeny, diversity, and composition plugins).
2. Download the SILVA 138 99% naive Bayes classifier [[6,7]](#references) and note its location.
3. Prepare your `manifest.tsv` and `metadata.tsv` as described in the wet lab guide.
4. Open the script and edit the **User Configuration** block at the top:

| Setting | What to set |
|---|---|
| `PROJECT_NAME` and `BASE_DIR` | Name of your project and the folder where results will be written |
| `MANIFEST` and `METADATA` | Paths to your manifest and metadata files |
| `CLASSIFIER` | Path to the SILVA classifier (`.qza`) |
| `TRUNC_LEN` | DADA2 truncation length, chosen from the quality plots for your own data |
| `SAMPLING_DEPTH` | Rarefaction depth for diversity metrics, chosen from your own feature table |
| `THREADS` | Number of cores (`0` uses all available cores) |
| `EXCLUDE_SAMPLES` | Sample IDs to remove (for example, failed samples), separated by a vertical bar (for example, `ID_1\|ID_2`) |

Two more lines in the script also need editing for your project: the **primer sequence** in the cutadapt step (`--p-front`) and the **ANCOM-BC formula** (`--p-formula`), which must use column names from your own metadata file.

> The values in the script (truncation length, sampling depth, excluded samples, and formula) are examples from one dataset. Do not reuse them for a different dataset. Choose your own after inspecting the quality summary (`03_quality/demux_summary.qzv`) and your feature table.

**To run it**

```bash
chmod +x master_pipeline.sh
./master_pipeline.sh
```

The script stops immediately if any step fails, so you can fix the problem and run it again.

**What the script does**

| Step | Output folder | What happens |
|---|---|---|
| Import | `02_import` | Imports single-end FASTQ files from the manifest |
| Primer trimming and quality summary | `03_cutadapt`, `03_quality` | Removes the forward primer, discards reads without it, and creates a quality summary |
| DADA2 denoising | `04_dada2` | Quality filtering, denoising, and chimera removal; produces the feature table and representative sequences |
| Taxonomy and filtering | `05_taxonomy` | Classifies sequences with SILVA 138, removes mitochondria, chloroplast, eukaryote, archaea, and unassigned sequences, and removes excluded samples |
| Phylogeny | `06_phylogeny` | Builds a rooted tree with MAFFT and FastTree |
| Diversity | `07_diversity` | Alpha and beta diversity metrics and PCoA at the chosen sampling depth |
| Differential abundance | `08_ancombc` | ANCOM-BC [[8]](#references) on genus-level counts using your formula |

### Storage Requirements

- FASTQ files for a typical class run: 2–8 GB
- SILVA 138 classifier: ~1 GB (downloaded once per Google Drive account)
- Google Drive free tier (15 GB) is sufficient for a single semester cohort

---

## Requirements

**Wet lab:**
- Standard molecular biology equipment (centrifuge, thermocycler, gel electrophoresis, magnetic rack)
- Sterile swabs and collection tubes
- PCR reagents and primers (see `SI_Cost.xlsx` for items and costs)

**Bioinformatic analysis:**
- FASTQ files returned from the sequencing provider
- *Option A (Colab):* a Google account (free) and a web browser
- *Option B (script):* a Linux computer or server with QIIME 2 installed, the SILVA 138 classifier, and enough disk space for your FASTQ files

---

## Cost

Total cost per sample is approximately **$13 USD** when sequencing is batched across a class cohort (~24 samples per run). This represents a ~35% reduction compared to conventional DNA extraction-based methods (~$20/sample). See `SI_Cost.xlsx` for a complete breakdown including reagents, consumables, and sequencing fees.

---

## Curriculum Framework

This module is designed as a Course-based Undergraduate Research Experience (CURE) [[9,10]](#references), in which students contribute original data rather than repeat predetermined outcomes. The CURE model has been shown to improve student motivation, deepen conceptual understanding, and broaden access to authentic research experiences across undergraduate populations. The curriculum aligns with American Society for Microbiology (ASM) curricular guidelines emphasizing scientific thinking, quantitative analysis, and communication.

---

## Acknowledgments

This work was supported by the Florida State University startup fund, the FSU First-Year Assistant Professor Award, and the Innovations for Gram-Negative Antibiotic Discovery program funded by the Novo Nordisk Foundation (NNF25SA0112407).

---

## References

[1] Song, F., Kuehl, J. V., Chandran, A., & Arkin, A. P. (2021). A simple, cost-effective, and automation-friendly direct PCR approach for bacterial community analysis. *mSystems*, 6(5). https://doi.org/10.1128/mSystems.00224-21

[2] Parada, A. E., Needham, D. M., & Fuhrman, J. A. (2016). Every base matters: assessing small subunit rRNA primers for marine microbiomes with mock communities, time series and global field samples. *Environmental Microbiology*, 18(5), 1403–1414. https://doi.org/10.1111/1462-2920.13023

[3] Apprill, A., McNally, S., Parsons, R., & Weber, L. (2015). Minor revision to V4 region SSU rRNA 806R gene primer greatly increases detection of SAR11 bacterioplankton. *Aquatic Microbial Ecology*, 75(2), 129–137. https://doi.org/10.3354/ame01753

[4] Illumina, Inc. (2013). *16S Metagenomic Sequencing Library Preparation* (Document No. 15044223 Rev. B). https://support.illumina.com/documents/documentation/chemistry_documentation/16s/16s-metagenomic-library-prep-guide-15044223-b.pdf

[5] Bolyen, E., Rideout, J. R., Dillon, M. R., Bokulich, N. A., et al. (2019). Reproducible, interactive, scalable and extensible microbiome data science using QIIME 2. *Nature Biotechnology*, 37(8), 852–857. https://doi.org/10.1038/s41587-019-0209-9

[6] Quast, C., Pruesse, E., Yilmaz, P., Gerken, J., Schweer, T., Yarza, P., Peplies, J., & Glöckner, F. O. (2013). The SILVA ribosomal RNA gene database project: improved data processing and web-based tools. *Nucleic Acids Research*, 41(D1), D590–D596. https://doi.org/10.1093/nar/gks1219

[7] Yilmaz, P., Parfrey, L. W., Yarza, P., Gerken, J., Pruesse, E., Quast, C., Schweer, T., Peplies, J., Ludwig, W., & Glöckner, F. O. (2014). The SILVA and "All-Species Living Tree Project (LTP)" taxonomic frameworks. *Nucleic Acids Research*, 42(D1), D643–D648. https://doi.org/10.1093/nar/gkt1209

[8] Lin, H., & Peddada, S. D. (2020). Analysis of compositions of microbiomes with bias correction. *Nature Communications*, 11(1), 3514. https://doi.org/10.1038/s41467-020-17041-7

[9] Auchincloss, L. C., Laursen, S. L., Branchaw, J. L., Eagan, K., Graham, M., Hanauer, D. I., et al. (2014). Assessment of course-based undergraduate research experiences: a meeting report. *CBE—Life Sciences Education*, 13(1), 29–40. https://doi.org/10.1187/cbe.14-01-0004

[10] Bangera, G., & Brownell, S. E. (2014). Course-based undergraduate research experiences can make scientific research more inclusive. *CBE—Life Sciences Education*, 13(4), 602–606. https://doi.org/10.1187/cbe.14-06-0099

*Citations were written from memory without database access. Please verify details (especially volume, page numbers, and DOIs) before publishing.*

---

## Contact

**Xiangpeng Li, Ph.D.**  
Department of Chemistry and Biochemistry  
Florida State University  
Tallahassee, FL 32306  
xli@chem.fsu.edu

Questions about the curriculum, requests for collaboration, or interest in adapting this module for your institution are welcome.
