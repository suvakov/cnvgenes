# CNV Gene Intersection Analyzer

A single-page web tool to analyze chromosomal Copy Number Variation (CNV) regions and identify intersecting genes. The tool queries the Ensembl (hg38/GRCh38) database to provide detailed information about how a given CNV affects overlapping genes.

## Description

This application provides a user-friendly interface to input one or more genomic regions and receive a detailed analysis of gene overlap. For each gene, it determines the type of intersection (e.g., fully contained, partial overlap) and can perform a deeper analysis on partially-affected genes to check if the CNV breakpoints disrupt coding sequences or specific exons.

This tool is designed for researchers and bioinformaticians who need a quick way to assess the potential impact of CNVs on gene function without complex command-line workflows.

## Features

* **Simple Interface:** Clean, single-page application that runs in any modern web browser.
* **Batch Processing:** Analyze multiple CNV regions in a single submission.
* **Detailed Gene Analysis:** Determines the relationship between the CNV and each overlapping gene.
* **Breakpoint Analysis:** For partial overlaps, it checks if breakpoints fall within a gene's coding region (CDS).
* **Exon-Level Detail:** Optional analysis to identify specific exons contained within the CNV or hit by a breakpoint.
* **Clear Output:**
    * A primary, tab-separated output for easy copy-pasting into spreadsheets or other software.
    * Detailed, collapsible logs for each region, showing the step-by-step analysis logic and raw API responses for transparency.
* **Robust API Handling:** Automatically handles Ensembl API rate-limiting by waiting and retrying requests.

## How to Use

1.  **Save the Code:** Copy the entire HTML code from the `cnv-gene-analyzer.html` file.
2.  **Open in Browser:** Save the code as an HTML file (e.g., `analyzer.html`) and open it with a web browser like Chrome, Firefox, or Edge.
3.  **Enter Regions:** In the "CNV Regions" text area, enter one or more genomic regions.
4.  **Select Options:** Check the boxes under "Include in summary" if you want to see details about exons within the CNV or at the breakpoint for partially affected genes.
5.  **Analyze:** Click the "Analyze Regions" button. The results and logs will appear below.

## Input Format

Regions should be provided in the format `[chrom]:[start]-[end]`, based on the hg38/GRCh38 human genome assembly.

You can enter multiple regions separated by a **newline** or a **comma**.

**Example Input:**

```
7:140719331-140783331
13:32315474-32335474
X:155019803-155029803
```
## Output Format

### 1. Tab-Separated Output

The main results are presented in a text area, with each line formatted as:

`[region]    [gene1(details),gene2(details),...]`

**Example:**
```
3:12430001-12830000    PPARG(partial,coding_breakpoint;intronic_breakpoint),CAND2(partial,coding_breakpoint;exon_at_breakpoint:ENSE00000966695|ENSE00000966696)
```
**Annotation Details:**

* `(contained)`: The gene is fully inside the CNV.
* `(contains_CNV)`: The CNV is fully inside the gene (e.g., in an intron).
* `(adjacent)`: The CNV and gene are next to each other but do not overlap.
* `(partial,coding_breakpoint)`: A CNV breakpoint falls within the gene's coding sequence (CDS).
* `(partial,non-coding_breakpoint)`: A CNV breakpoint falls within the gene's non-coding sequence (e.g., an intron outside the CDS).
* `;exons_inside:[id1|id2]`: (Optional) Lists exons fully inside the CNV.
* `;exon_at_breakpoint:[id1|id2]`: (Optional) Lists exons physically hit by a breakpoint.
* `;intronic_breakpoint`: (Optional) Indicates a coding breakpoint occurred in an intron, not within an exon's boundaries.

### 2. Detailed Logs

For each region analyzed, a collapsible log is generated. This provides a step-by-step trace of the analysis, including:

* The coordinates being checked.
* The logic used to determine the breakpoint type.
* The full JSON response from the Ensembl API for detailed gene lookups, allowing for manual verification.

## Technical Details

* **Frontend:** Plain HTML, JavaScript, and Tailwind CSS for styling. No server-side component is needed.
* **API:** Uses the public Ensembl REST API (`https://rest.ensembl.org`) for all genomic data.
* **Genome Assembly:** All coordinates and gene models are based on **GRCh38 (hg38)** as defined by Ensembl.