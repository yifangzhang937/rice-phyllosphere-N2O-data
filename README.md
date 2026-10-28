# Rice phyllosphere N2O data

Research data supporting the study of rice phyllosphere nitrous oxide (N2O) emissions under partial replacement of urea nitrogen with pig manure compost.

This repository contains N2O emission rates, enclosure headspace N2O concentrations, microbial gene abundances, and selected metabolomic and transcriptomic abundance tables with sample metadata.

## Repository contents

All data tables are located in the `data/` directory. Row counts below exclude the header.

| File | Contents | Records / dimensions |
| --- | --- | --- |
| `data/n2o_emission_rates.csv` | N2O-N emission rates by growth stage, treatment and source sample label | 48 records |
| `data/n2o_headspace_concentrations.csv` | Enclosure headspace N2O concentrations at 0, 8, 16 and 24 hours | 192 records |
| `data/microbial_gene_abundances.csv` | qPCR gene abundances and the bacterial (nirK + nirS) / nosZ ratio | 48 records |
| `data/metabolomics_abundances.csv` | LC-MS feature abundances and 16 original annotation columns | 4,978 features x 48 samples |
| `data/metabolomics_sample_metadata.csv` | Sample identifiers, growth stages, treatments, replicate labels and tissue types for metabolomics | 48 samples |
| `data/transcriptomics_expression_tpm.csv` | Gene-level expression abundance and four original annotation columns | 38,993 genes/sequences x 48 samples |
| `data/transcriptomics_sample_metadata.csv` | Sample identifiers, growth stages, treatments, replicate labels and tissue types for transcriptomics | 48 samples |
| `data/data_dictionary.csv` | Field definitions, units and allowed values for the N2O and microbial gene tables | 24 field definitions |

## Experimental codes

### Rice growth stages

- `GF`: grain filling.
- `M`: maturity.

### Fertilization treatments

- `CK`: no nitrogen fertilizer.
- `U`: 100% urea-derived fertilizer nitrogen.
- `25P`: pig manure compost replacing 25% of urea-derived fertilizer nitrogen, at the same total nitrogen input as `U`.

Equal total nitrogen input applies to `U` and `25P`.

### Biological materials

In the metabolomic and transcriptomic tables:

- `P`: leaf tissue.
- `R`: root tissue.

In the microbial gene abundance table:

- `Rhizosphere`: rhizosphere soil.
- `Phyllosphere`: whole-leaf-associated sample. Leaf samples were not surface sterilized, so this measurement includes epiphytic and endophytic microorganisms.

The root-tissue omics samples and rhizosphere-soil qPCR samples represent different biological materials.

## Variables and units

| Variable / data type | Unit or interpretation |
| --- | --- |
| `n2o_emission_rate_ug_N2O_N_m2_h` | Micrograms of N2O-N per square metre of ground area per hour (ug N2O-N m^-2 h^-1) |
| `n2o_concentration_ppbv` | Parts per billion by volume, equivalent to nmol mol^-1 |
| `source_time_label` | Hours after enclosure: `0h`, `8h`, `16h`, `24h` at both growth stages |
| qPCR fields ending in `_copies_per_g` | Gene copies per gram of dry material used for DNA extraction: dry soil for `Rhizosphere` and dry leaf material for `Phyllosphere` |
| `nirK_nirS_to_nosZ_ratio` | Dimensionless ratio defined below |
| Metabolomic sample columns | Source-exported LC-MS peak-area/abundance values on the supplied numerical scale; these are signal abundances rather than absolute metabolite concentrations |
| Transcriptomic sample columns | Transcripts per million (TPM), following the study Methods |

For each qPCR sample, the gene ratio is:

```text
(bacterial_nirK_copies_per_g + bacterial_nirS_copies_per_g)
/
(nosZ_clade_I_copies_per_g + nosZ_clade_II_copies_per_g)
```

Fungal `nirK` is excluded from this ratio. Gene abundances and the ratio describe genetic potential rather than measured N2O production or reduction rates.

## Sample identifiers and replication

### N2O measurements

For each growth stage and treatment, emission-rate labels end in 1-8. Adjacent pairs (1/2, 3/4, 5/6 and 7/8) represent two technical replicates from each of four independent field plots. The eight records should therefore not be treated as eight independent field plots.

The headspace concentration table contains eight readings per growth-stage x treatment x time group. It does not include individual sample or plot identifiers; row order alone does not establish pairing across time points.

### Microbial gene measurements

The qPCR table contains four records per growth-stage x compartment x treatment group. Labels repeat across stages and compartments. Identify a record using `growth_stage`, `compartment`, `treatment` and `source_sample_id` together. A cross-file correspondence between these labels and the emission-rate technical-replicate pairs is not encoded in the shared tables.

### Metabolomics and transcriptomics

Both omics datasets use the same selected sample identifiers:

- Leaf tissue: `P33-P44` and `P49-P60`.
- Root tissue: `R33-R44` and `R49-R60`.

Each tissue x growth stage x treatment group contains four samples. Use each sample metadata file to match expression/abundance-column headers to experimental groups. `replicate_number` is the numerical component of the supplied treatment-replicate label.

Two source transcriptomic column names were standardized as follows:

| Original column name | Standardized sample identifier |
| --- | --- |
| `mix_R` | `R38` |
| `mix_Rp1` | `R39` |

These header changes do not alter the associated expression values.

## Omics processing and table preparation

### Metabolomics

LC-MS data were processed using Progenesis QI. Metabolite annotation used HMDB, METLIN and Majorbio databases. The study's analysis workflow retained features detected in at least 80% of samples, normalized peak areas to the total peak area of each sample, excluded features with QC relative standard deviation greater than 30%, and used log10 transformation for analysis.

The shared matrix preserves the numerical scale of the supplied feature-abundance export. No additional normalization or log transformation was applied when selecting the 48 samples for this repository. The workflow description does not imply that every processing step has already been applied to the exported matrix. All 4,978 supplied feature rows are retained: 2,584 positive-ion and 2,394 negative-ion features. Feature IDs and source annotations are retained; features sharing a metabolite name are not combined.

### Transcriptomics

Libraries were sequenced on an Illumina NovaSeq X Plus platform using 150-bp paired-end reads. Reads were quality-filtered with fastp and aligned to the Oryza sativa IRGSP-1.0 reference genome using HISAT2. StringTie was used for assembly and RSEM for expression quantification. Functional annotation used NR, Swiss-Prot, Pfam, eggNOG, GO and KEGG.

Gene-level raw counts were used for differential-expression analysis, whereas TPM values summarized expression abundance. The shared TPM table contains all 38,993 supplied gene/sequence rows, including zero-expression rows. The TPM designation follows the study Methods. Values were copied without recalculation, additional filtering, rounding or transformation during preparation of this repository.

## Raw-data accessions

The omics tables in this repository complement the raw-data deposits identified below:

| Raw data | Repository | Accession |
| --- | --- | --- |
| LC-MS metabolomics | MetaboLights | `MTBLS15247` |
| Transcriptomic sequencing reads | NCBI Sequence Read Archive, BioProject | `PRJNA1507832` |

Search the accessions at [MetaboLights](https://www.ebi.ac.uk/metabolights/) and [NCBI BioProject](https://www.ncbi.nlm.nih.gov/bioproject/).
