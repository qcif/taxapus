# daff/taxassignwf

<!-- TODO [![GitHub Actions CI Status](https://github.com/daff/taxassignwf/actions/workflows/ci.yml/badge.svg)](https://github.com/daff/taxassignwf/actions/workflows/ci.yml)
[![GitHub Actions Linting Status](https://github.com/daff/taxassignwf/actions/workflows/linting.yml/badge.svg)](https://github.com/daff/taxassignwf/actions/workflows/linting.yml)[![Cite with Zenodo](http://img.shields.io/badge/DOI-10.5281/zenodo.XXXXXXX-1073c8?labelColor=000000)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![nf-test](https://img.shields.io/badge/unit_tests-nf--test-337ab7.svg)](https://www.nf-test.com)

[![Nextflow](https://img.shields.io/badge/nextflow%20DSL2-%E2%89%A524.04.2-23aa62.svg)](https://www.nextflow.io/)
[![run with conda](http://img.shields.io/badge/run%20with-conda-3EB049?labelColor=000000&logo=anaconda)](https://docs.conda.io/en/latest/)
[![run with docker](https://img.shields.io/badge/run%20with-docker-0db7ed?labelColor=000000&logo=docker)](https://www.docker.com/)
[![run with singularity](https://img.shields.io/badge/run%20with-singularity-1d355c.svg?labelColor=000000)](https://sylabs.io/docs/)
[![Launch on Seqera Platform](https://img.shields.io/badge/Launch%20%F0%9F%9A%80-Seqera%20Platform-%234256e7)](https://cloud.seqera.io/launch?pipeline=https://github.com/daff/taxassignwf) -->

## Introduction

This workflow analyses  DNA barcode sequences, attempting to annotate each with a taxonomic identity.

Much of the code for this analysis is written in Python and lives in https://github.com/qcif/daff-biosecurity-wf2/.

<!-- TODO nf-core:
   Complete this sentence with a 2-3 sentence summary of what types of data the pipeline ingests, a brief overview of the
   major pipeline sections and the types of output it produces. You're giving an overview to someone new
   to nf-core here, in 15-20 seconds. For an example, see https://github.com/nf-core/rnaseq/blob/master/README.md#introduction
-->

<!-- TODO nf-core: Include a figure that guides the user through the major workflow steps. Many nf-core
     workflows use the "tube map" design for that. See https://nf-co.re/docs/contributing/design_guidelines#examples for examples.   -->
<!-- TODO nf-core: Fill in short bullet-pointed list of the default steps in the pipeline -->

## Setup

### Software

1. Instructions on how to set up Nextflow and a compatible version of Java on [this page](https://www.nextflow.io/docs/latest/install.html#installation).
2. To install singularity follow instructions from [this website](https://docs.sylabs.io/guides/3.7/admin-guide/installation.html#before-you-begin).

The following versions of the programs were used to test this pipeline:

<table border="1" style="border-collapse: collapse;">
    <tr>
        <th style="border: 1px solid;">Program</th>
        <th style="border: 1px solid;">Version</th>
    </tr>
    <tr>
        <td style="border: 1px solid;">Singularity</td>
        <td style="border: 1px solid;">3.7.0</td>
    </tr>
    <tr>
        <td style="border: 1px solid;">Java</td>
        <td style="border: 1px solid;">17.0.13</td>
    </tr>
    <tr>
        <td style="border: 1px solid;">Nextflow</td>
        <td style="border: 1px solid;">24.10.3</td>
    </tr>
</table>

### Databases
1. Download a preformatted NCBI BLAST database `core_nt` database by running the update_blastdb.pl program. Follow instructions from [this book](https://www.ncbi.nlm.nih.gov/books/NBK569850/). [Perl installation](https://www.perl.org/get.html) is required.
2. Download the NCBI TaxDB files from https://ftp.ncbi.nlm.nih.gov/blast/db/taxdb.tar.gz and extract them to `~/.taxonkit`.


## Input
### Required
The mandatory input includes the following parameters:
- metadata /path/to/metadata.csv: The metadata file containing information about the sequences.
- sequences /path/to/queries.fasta: The FASTA file containing the query sequences (up to 100).
- blastdb /path/to/blastdbs/core_nt: The BLAST database to be used for query searching. Your `/path/to/blastdbs` folder should contain the following files:
- core_nt with extensions `.nal`, `.ndb`, `.njs`, `.nos`, `.not`, `.ntf` and `.nto`
- multiple volumes of core_nt, named core_nt.`NUM` with extensions `.nhr`, `.nin`, `.nnd`, `.nni`, `.nog`, `.nsq`
- taxdb.btd and taxdb.bti files
- outdir /path/to/output: The output directory where the results will be stored.
- taxdb /path/to/.taxonkit/: The path to the taxonomic database NCBI Taxonomy Toolkit. Following files should be available in that folder: citations.dmp, division.dmp, gencode.dmp, merged.dmp, nodes.dmp, taxonkit, delnodes.dmp, gc.prt, images.dmp, names.dmp and readme.txt

### Recommended
You can [generate an NCBI API key](https://support.nlm.nih.gov/kbArticle/?pn=KA-05317) to eliminate restrictions on Entrez queries and make the database coverage evaluation process faster. Pass it with the following parameters:
- ncbi_api_key <your_key_123>
- user_email <me@example.com>


### Sequences file (`queries.fasta`)
#### Example
```
>VE24-1075_COI
TGGATCATCTCTTAGAATTTTAATTCGATTAGAATTAAGACAAATTAATTCTATTATTWATAATAATCAATTATATAATGTAATTGTTCACAATTCATGCTTTTATTATAATTTTTTTTATAACTATACCAATTGTAATTGGTGGATTTGGAAATTGATTAATTCCTATAATAATAGGATGTCCTGATATATCATTTCCACSTTTAAATAATATTAGATTTTGATTATTACCTCCATCATTAATAATAATAATTTGTAGATTTTTAATTAATAATGGAACAGGAACAGGATGAACAATTTAYCCHCCTTTATCAAACAATATTGCACATAATAACATTTCAGTTGATTTAACTATTTTTTCTTTACATTTAGCAGGWATCTCATCAATTTTAGGAGCAATTAACTTTATTTGTACAATTCTTAATATAATAYCAAAYAATATAAAACTAAATCAAATTCCTCTTTTTCCTTGATCAATTTTAATTACAGCTATTTTATTAATTTTATMTTTACCAGTTTTAGCTGGTGCCATTACAATATTATTAACTGATCGTAATTTAAATACATCATTTTTGATCCAGCAGGAGGAGGAGATCC
>VE24-1079_COI
AACTTTATATTTCATTTTTGGAATATGGGCAGGTATATTAGGAACTTCACTAAGATGAATTATTCGAATTGAACTTGGACAACCAGGATCATTTATTGGAGATGATCAAATTTATAATGTAGTAGTTACCGCACACGCATTTATTATAATTTTCTTTATAGTTATACCAATTATAATTGGAGGATTTGGAAATTGATTAGTACCTCTAATAATTGGAGCACCAGATATAGCATTCCCACGGATAAATAATATAAGATTTTGATTATTACCACCCTCAATTACACTTCTTATTATAAGATCTATAGTAGAAAGAGGAGCAGGAACTGGATGAACAGTATATCCCCCACTATCATCAAATATTGCACATAGTGGAGCATCAGTAGACCTAGCAATTTTTTCACTACATTTAGCAGGTGTATCTTCAATTTTAGGAGCAATTAATTTCATCTCAACAATTATTAATATACGACCTGAAGGCATATCTCCAGAACGAATTCCATTATTTGTATGATCAGTAGGTATTACAGCATTACTATTATTATTATCATTACCAGTTCTAGCTGGAGCTATTACAATATTATTAACAGATCGAAACTTTAATACCTCATTCTTTGACCCAGTAGGAGGAGGAGATCCTATCTTATATCAACATTTATTTTGATTTTTT
```

### Metadata file (`metadata.csv`)

The `metadata.csv` file should adhere to the following structure

#### Required Columns
1. **sample_id**
2. **locus**
3. **preliminary_id**

#### Optional Columns
1. **taxa_of_interest** - if multiple, they should be separated by a `|` character
2. **host**
3. **country**

#### Example

<table>
    <thead>
        <tr>
            <th>Sample ID</th>
            <th>Locus</th>
            <th>Preliminary ID</th>
            <th>Taxa of Interest</th>
            <th>Host</th>
            <th>Country</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>VE24-1075_COI</td>
            <td>COI</td>
            <td>Aphididae</td>
            <td>Myzus persicae | Aphididae</td>
            <td>Cut flower Rosa</td>
            <td>Ecuador</td>
        </tr>
        <tr>
            <td>VE24-1079_COI</td>
            <td>COI</td>
            <td>Miridae</td>
            <td>Lygus pratensis</td>
            <td>Cut flower Paenonia</td>
            <td>Netherlands</td>
        </tr>
    </tbody>
</table>

<!-- TODO nf-core: Describe the minimum required steps to execute the pipeline, e.g. how to prepare samplesheets.
     Explain what rows and columns represent. For instance (please edit as appropriate):

First, prepare a samplesheet with your input data that looks as follows:

`samplesheet.csv`:

```csv
sample,fastq_1,fastq_2
CONTROL_REP1,AEG588A1_S1_L002_R1_001.fastq.gz,AEG588A1_S1_L002_R2_001.fastq.gz
```

Each row represents a fastq file (single-end) or a pair of fastq files (paired end).

-->
## Running the pipeline
You can run the pipeline using:

<!-- TODO nf-core: update the following command to include all required parameters for a minimal example -->

```bash
nextflow run qcif/nf-daff-biosecurity-wf2 \
    --metadata /path/to/metadata.csv \
    --sequences /path/to/queries.fasta \
    --blastdb /path/to/blastdbs/core_nt \
    --outdir /path/to/output \
    -profile singularity \
    --taxdb /path/to/.taxonkit/ \
    -- ncbi_api_key API_KEY \
    -- user_email EMAIL \
    -resume
```

## Results folder structure
```
output
├── blast_result.xml
├── pipeline_info
│   ├── execution_report_2025-03-15_03-16-36.html
│   ├── execution_timeline_2025-03-15_03-16-36.html
│   ├── execution_trace_2025-03-15_03-16-36.txt
│   ├── params_2025-03-15_03-16-43.json
│   ├── pipeline_dag_2025-03-15_03-16-36.html
│   ├── taxassignwf_software_versions.yml
│   └── versions.yml
├── query_001_VE24-1075_COI
│   ├── blast_hits.fasta
│   ├── candidates.csv
│   ├── candidates.fasta
│   ├── candidates.msa
│   ├── candidates.msa.nwk
│   └── identity-boxplot.png
└── query_002_VE24-1079_COI
    ├── blast_hits.fasta
    ├── candidates.csv
    ├── candidates.fasta
    ├── candidates.msa
    └── candidates.msa.nwk
```

<!-- TODO 
> [!WARNING]
> Please provide pipeline parameters via the CLI or Nextflow `-params-file` option. Custom config files including those provided by the `-c` Nextflow option can be used to provide any configuration _**except for parameters**_; see [docs](https://nf-co.re/docs/usage/getting_started/configuration#custom-configuration-files). -->

## Credits

daff/taxassignwf was originally written by Magdalena Antczak, Cameron Hyde, Daisy Li.

<!-- TODO 

We thank the following people for their extensive assistance in the development of this pipeline:

-->

<!-- TODO nf-core: If applicable, make list of people who have also contributed -->

<!-- TODO 
## Contributions and Support

If you would like to contribute to this pipeline, please see the [contributing guidelines](.github/CONTRIBUTING.md).

## Citations
-->

<!-- TODO nf-core: Add citation for pipeline after first release. Uncomment lines below and update Zenodo doi and badge at the top of this file. -->
<!-- If you use daff/taxassignwf for your analysis, please cite it using the following doi: [10.5281/zenodo.XXXXXX](https://doi.org/10.5281/zenodo.XXXXXX) -->

<!-- TODO nf-core: Add bibliography of tools and data used in your pipeline -->

<!-- TODO 
An extensive list of references for the tools used by the pipeline can be found in the [`CITATIONS.md`](CITATIONS.md) file.

This pipeline uses code and infrastructure developed and maintained by the [nf-core](https://nf-co.re) community, reused here under the [MIT license](https://github.com/nf-core/tools/blob/main/LICENSE).

> **The nf-core framework for community-curated bioinformatics pipelines.**
>
> Philip Ewels, Alexander Peltzer, Sven Fillinger, Harshil Patel, Johannes Alneberg, Andreas Wilm, Maxime Ulysse Garcia, Paolo Di Tommaso & Sven Nahnsen.
>
> _Nat Biotechnol._ 2020 Feb 13. doi: [10.1038/s41587-020-0439-x](https://dx.doi.org/10.1038/s41587-020-0439-x).
-->
