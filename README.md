**DESeq Explorer (featureCounts → DESeq2)**
- Shiny app to run DESeq2 on featureCounts outputs, then explore results with an interactive volcano plot and TPM heatmap. Great for teammates without command-line experience.
- Live app: [https://frankhmx.shinyapps.io/deseq_explorer/].

**Highlights**
- Upload featureCounts count table + design table (tsv/csv); skips `#` comment lines automatically.
- Auto-matches samples, flags missing or duplicated sample IDs.
- Configurable `padj` and |FC| thresholds; six heatmap palettes.
- Plotly volcano with top significant genes labeled; contrast direction respected (Treated = numerator, Control = denominator).
- TPM calculation plus top-gene heatmap (falls back to top 20 by padj if none significant); one-click bundle download (CSV, DEG lists, RData, volcano/heatmap PDFs).
- Clear validation errors for common issues (missing length column, sample mismatch, etc.).

**Input Formats**
- featureCounts table: needs `Geneid` (or first column), `Length` (or `length/GeneLength/...`), and sample columns.
- Design table: includes sample column and group column (you choose which in the UI); sample names must match count table columns.
- Example design table:
  ```
  sample    group
  S1        ctrl
  S2        ctrl
  S3        treat
  S4        treat
  ```

**Run Locally**
```r
# Install dependencies
install.packages(c("shiny","readr","dplyr","tibble","ggplot2","plotly","pheatmap","RColorBrewer"))
if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager")
BiocManager::install("DESeq2")

# Launch
shiny::runApp("app.R")
```
- Default upload limit is 50 MB (set via `options(shiny.maxRequestSize = 50 * 1024^2)` in app.R).

**How to Use**
1) Upload featureCounts table and design table.  
2) Select sample column and group column; pick Treated (numerator) and Control (denominator).  
3) Set `padj` and |FC| thresholds and heatmap palette; click “Run DESeq2.”  
4) View volcano and TPM heatmap; click “Download results” to get a `.tar.gz` with all outputs.

**Outputs**
- `deseq2_results.csv`: gene, log2FC, padj, labels, TPM columns.
- `DEGs_list.txt`: tab-separated up/down gene lists.
- `deseq2_objects.Rdata`: `res_df`, `tpm`, `colData`.
- `volcano_ggplot.pdf`, `tpm_heatmap.pdf` (when data is available).

**Assumptions / Notes**
- Length column required for TPM; lengths must be positive.
- Sample names are case-sensitive.
- Contrast direction: Treated (group_a) / Control (group_b).
- If no significant genes, heatmap falls back to the top 20 genes by smallest padj.
