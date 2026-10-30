# 16S rRNA Microbiome Research Training

**Li Laboratory · Department of Chemistry and Biochemistry · Florida State University**

Microbiome science is increasingly important in modern biology, yet hands-on sequencing-based education remains out of reach for many undergraduate programs due to high costs and technical complexity. This repository contains all materials for an inquiry-based 16S rRNA gene sequencing curriculum that enables undergraduate students to conduct complete, independent microbiome research projects — from sample collection through bioinformatic analysis — at approximately **$13 per sample**.

The curriculum was developed and piloted at Florida State University with students majoring in Biochemistry and Biological Science. Students designed their own research questions, collected environmental samples from self-selected locations, performed all molecular laboratory steps, analyzed sequencing data using QIIME 2, and communicated findings through scientific poster presentations. Assessment data showed strong learning gains across microbiome concepts, molecular biology techniques, bioinformatics, and scientific communication, with all participants expressing increased interest in pursuing future research in microbiology or bioinformatics.

Two cost-reduction strategies make the workflow accessible to teaching labs with limited budgets:

- **Centrifugation-based cell collection** — microbial cells are pelleted directly from water or surface swab buffer, eliminating the need for membrane filtration or commercial extraction kits
- **Surfactant-assisted direct PCR** — cells are lysed *in situ* during PCR amplification using a detergent-assisted thermal lysis approach [[1]](#references), removing the DNA extraction step entirely while maintaining sufficient template quality for high-throughput sequencing

Student projects have successfully characterized microbial communities in campus water sources, gym equipment surfaces, and other local environments, generating publication-quality datasets using the Illumina MiSeq platform.

---

## Repository Contents

| File | Description |
|---|---|
| `16S_rRNA_Colab_Notebook.ipynb` | Google Colab notebook for QIIME 2 analysis (import → DADA2 → taxonomy → diversity → ANCOM-BC) |
| `SI_Microbial_community_analysis_procedure.docx` | Step-by-step wet lab protocol (sample collection, direct PCR, library preparation, MiSeq submission) |
| `SI_Project_Design_template.docx` | Student project design template with guided prompts for hypothesis development |
| `SI_Cost.xlsx` | Itemized cost breakdown per sample and per class cohort |
| `Student_Survey.docx` | Post-course survey for assessment of student learning outcomes |

---

## Getting Started

### Wet Lab Protocol

Follow the procedure in `SI_Microbial_community_analysis_procedure.docx`. The workflow uses:

- **Direct PCR** from environmental swab or water pellet — no DNA extraction required [[1]](#references)
- **515F / 806R primers** [[2,3]](#references) targeting the V4 region of the 16S rRNA gene with Illumina adapter overhangs [[4]](#references)
- **Two-step barcoding PCR** for indexed library preparation [[4]](#references)
- **Illumina MiSeq** paired-end sequencing (2×250 bp) submitted through a commercial provider

### Bioinformatic Analysis

Open `16S_rRNA_Colab_Notebook.ipynb` in Google Colab:

1. Go to [colab.research.google.com](https://colab.research.google.com)
2. Click **File → Open notebook → GitHub**
3. Paste this repository URL and select the notebook
4. Mount your Google Drive when prompted — this is where your FASTQ files and results will be stored
5. Run cells sequentially from top to bottom

No local software installation is required. QIIME 2 [[5]](#references) (2024.5) and all dependencies install automatically during the Environment Setup section (~15–20 minutes on first run).

#### What the notebook does

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

### Storage Requirements

- FASTQ files for a typical class run: 2–8 GB
- SILVA 138 classifier: ~1 GB (downloaded once per Google Drive account)
- Google Drive free tier (15 GB) is sufficient for a single semester cohort

---

## Requirements

**Wet lab:**
- Standard molecular biology equipment (centrifuge, thermocycler, gel electrophoresis)
- Sterile swabs and collection tubes
- PCR reagents and primers (see `SI_Cost.xlsx` for suppliers and catalog numbers)

**Bioinformatic analysis:**
- Google account (free)
- Web browser
- FASTQ files returned from sequencing provider

---

## Cost

Total cost per sample is approximately **$13 USD** when sequencing is batched across a class cohort (~24 samples per run). This represents a ~35% reduction compared to conventional DNA extraction-based methods (~$20/sample). See `SI_Cost.xlsx` for a complete breakdown including reagents, consumables, and sequencing fees.

---

## Curriculum Framework

This module is designed as a Course-based Undergraduate Research Experience (CURE) [[9,10]](#references), in which students contribute original data rather than repeat predetermined outcomes. The CURE model has been shown to improve student motivation, deepen conceptual understanding, and broaden access to authentic research experiences across undergraduate populations. The curriculum aligns with American Society for Microbiology (ASM) curricular guidelines emphasizing scientific thinking, quantitative analysis, and communication.

---

## IRB

Environmental sample collection does not involve human subjects. The post-course student survey is anonymous and voluntary and is conducted under Florida State University IRB protocol [insert protocol number upon approval] through the FSU Office for Human Subjects Protection.

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

[6] Quast, C., Pruesse, E., Yilmaz, P., Gerken, J., Schweer, T., Yarza, P., Peplies, J., & Glöckner, F. O. (2012). The SILVA ribosomal RNA gene database project: improved data processing and web-based tools. *Nucleic Acids Research*, 41(D1), D590–D596. https://doi.org/10.1093/nar/gks1219

[7] Yilmaz, P., Parfrey, L. W., Yarza, P., Gerken, J., Pruesse, E., Quast, C., Schweer, T., Peplies, J., Ludwig, W., & Glöckner, F. O. (2014). The SILVA and "All-Species Living Tree Project (LTP)" taxonomic frameworks. *Nucleic Acids Research*, 42(D1), D643–D648. https://doi.org/10.1093/nar/gkt1209

[8] Lin, H., & Peddada, S. D. (2020). Analysis of compositions of microbiomes with bias correction. *Nature Communications*, 11(1), 3514. https://doi.org/10.1038/s41467-020-17041-7

[9] Auchincloss, L. C., Laursen, S. L., Branchaw, J. L., Eagan, K., Graham, M., Hanauer, D. I., et al. (2014). Assessment of course-based undergraduate research experiences: a meeting report. *CBE—Life Sciences Education*, 13(1), 29–40. https://doi.org/10.1187/cbe.14-01-0004

[10] Bangera, G., & Brownell, S. E. (2014). Course-based undergraduate research experiences can make scientific research more inclusive. *CBE—Life Sciences Education*, 13(4), 602–606. https://doi.org/10.1187/cbe.14-06-0099

---

## Contact

**Xiangpeng Li, Ph.D.**  
Department of Chemistry and Biochemistry  
Florida State University  
Tallahassee, FL 32306  
xli@chem.fsu.edu

Questions about the curriculum, requests for collaboration, or interest in adapting this module for your institution are welcome.
