# Genome Dashboard — Handoff

## What This Is

A single-file, client-side genome analyzer and visualizer. Users drop a 23andMe (or AncestryDNA) raw data file into the browser and get a full interactive dashboard analyzing 100+ SNPs across 9 categories. Zero server, zero dependencies beyond a Chart.js CDN, 100% private (nothing leaves the browser).

**Repo:** https://github.com/KewkLW/genome-dashboard
**Main file:** `index.html` (~1650 lines, self-contained)
**License:** MIT

## Architecture

Everything lives in one HTML file. No build step, no framework, no Node, no Python.

- **CSS:** Inline `<style>` block. Dark theme, Inter + JetBrains Mono fonts (Google Fonts CDN), responsive grid layout, card-based UI with tab navigation
- **JS:** Inline `<script>` block. Three main systems:
  1. **File parser** — reads 23andMe tab-separated or AncestryDNA comma-separated formats, extracts rsID/chr/position/genotype into a `snps` object and `chrCounts` object
  2. **SNP database** (`SNP_DB` array) — ~30 variant definitions with `interpret()` functions that take a genotype string and return `{ tag, tagClass, detail }` objects. Some entries have `multi` arrays for variants requiring multiple SNPs (e.g., APOE needs rs429358 + rs7412)
  3. **Renderer** — `renderDashboard(data)` builds all UI: hero stats, summary grid, karyogram, charts (Chart.js radar + horizontal bar), tabbed card grid, legends
- **Caching:** After first file drop, parsed SNP data is cached in `localStorage` under key `genome_dashboard_data`. Only the ~100 relevant SNPs are stored (not the full 16MB file). Subsequent visits skip the upload screen and render instantly. "Load New File" button calls `clearCacheAndReload()` to wipe cache and restart
- **Visuals:** Animated triple DNA helix rendered on a `<canvas>` in the hero section. Chart.js for radar (neurotransmitter profile) and bar (risk overview). Karyogram is hand-built div bars

## SNP Database Schema

Each entry in `SNP_DB`:

```javascript
{
  id: 'rs12913832',          // primary rsID to look up
  gene: 'HERC2/OCA2',       // gene name for display
  title: 'Eye Color',        // card title
  category: 'identity',      // which tab: identity|neuro|health|athletic|pharma|inflammation|nutrition|sleep|aging
  color: 'blue',             // card accent: green|yellow|red|blue|purple|cyan|pink|orange
  multi: ['rs429358','rs7412'], // optional: array of rsIDs when multiple SNPs are needed
  interpret: (genotype, allSnps) => {
    // genotype = string like 'AG', 'CC', 'DD', '--'
    // allSnps = full snps object (for multi-SNP lookups)
    // returns { tag: 'Display Label', tagClass: 'tag-normal|tag-carrier|tag-risk|tag-info|tag-notable', detail: 'Explanation text' }
    // can also return { genotype: 'custom display' } to override the allele boxes
  },
  summary: (genotype, allSnps) => {
    // optional: if present, creates a summary tile in the top grid
    // returns { emoji: '...', label: 'Eye Color', value: 'Brown', tip: 'Hover tooltip text' }
  }
}
```

## Categories Covered

| Tab | What's In It | Key SNPs |
|-----|-------------|----------|
| Identity & Traits | Eye color, skin, hair, blood type, taste, earwax, freckling, height, Duffy/malaria | rs12913832, rs1426654, rs16891982, rs8176719, rs17822931, rs713598, rs72921001, rs3827760, rs12203592 |
| Brain & Neuro | COMT warrior/worrier, BDNF, DRD2, opioid receptor, oxytocin, serotonin, norepinephrine | rs4680, rs6265, rs1800497, rs1799971, rs53576, rs6311, rs1611115 |
| Health & Risk | APOE/Alzheimer's, Factor V Leiden, T2D, FTO/obesity, MTHFR, celiac | rs429358+rs7412, rs6025, rs7903146, rs9939609, rs1801133+rs1801131, rs2187668 |
| Athletic | ACTN3 muscle type | rs1815739 |
| Pharmacogenomics | Statins (SLCO1B1), warfarin (VKORC1), caffeine (CYP1A2) | rs4149056, rs9923231, rs762551 |
| Inflammation | IL-6, TNF-alpha, secretor status | rs1800795, rs1800629, rs601338 |
| Nutrition | Lactose, caffeine, alcohol flush | rs4988235, rs762551, rs671 |
| Sleep | CLOCK chronotype | rs1801260 |
| Aging | Telomeres, smoking, DPYD safety | rs10936599, rs1051730 |

## Educational Features

- **Summary tile tooltips:** Every tile in the top grid has a hover tooltip explaining the biology, why it matters, and practical takeaways
- **Karyogram explainer:** Collapsible `<details>` below the chromosome chart explaining what SNP density is, how to read the chart, and practical uses
- **Neurotransmitter legend:** Collapsible panel below the radar chart with each axis described: what the system does, what high/low scores mean, how it affects behavior
- **Risk legend:** Collapsible panel below the risk chart with all 8 risk categories covering: what the condition is, how it manifests, and evidence-based prevention/mitigation strategies

## What's NOT Done / Future Ideas

- **More SNPs:** The database has ~30 entries covering the highest-impact variants. There are hundreds more well-studied SNPs that could be added (see the SNP research agent output in the conversation for a massive list). Adding a new one is just pushing to `SNP_DB`
- **AncestryDNA format:** Parser supports it but hasn't been heavily tested. The comma-separated format should work but edge cases may exist
- **Ancestry/haplogroup analysis:** Y-DNA and mtDNA haplogroup determination was not implemented. 23andMe uses internal IDs (not rsIDs) for many of these markers, making it harder to parse from raw data
- **GitHub Pages:** Not enabled yet. Could be turned on in repo settings for instant hosting
- **Circos/circular plot:** The research agent found pyCirclize and Circos.js for circular genome visualizations. Could be a cool addition
- **3D visualization:** The `exactlyallan/23andMeVisualization` GitHub project renders 23andMe data in WebXR/VR. Could be integrated or linked
- **Export/PDF:** No export functionality. Users can print the page but a proper PDF or shareable report would be nice
- **Polygenic risk scores:** Currently each risk is based on 1-2 SNPs. Real polygenic risk scores aggregate hundreds of variants. Would need a reference dataset
- **SNPedia auto-lookup:** Could add a "look up on SNPedia" link for each rsID
- **Comparison mode:** Load two files side by side (e.g., partners checking compatibility for trait inheritance)
- **More pharmacogenomics:** CYP2D6 (affects ~25% of all drugs) is complex (copy number variations) and wasn't included. CYP3A4, CYP2C9 could be added
- **Mobile optimization:** Works on mobile but some tooltips may be awkward on small screens. The karyogram labels get tight

## Key Files from the Original Analysis

- **Patrick's raw data:** `F:\genome\genome_Patrick_Bowman_v5_Full_20260317131223.txt` (631K SNPs, 23andMe v5)
- **Patrick's hardcoded dashboard:** `F:\genome\genome_dashboard.html` (the original version with his data baked in, kept as reference)
- **Research agent outputs:** Visualization tools research and SNP database research were run as background agents during the conversation. The SNP agent returned a comprehensive list of ~100+ rsIDs with full genotype interpretations across 7 categories. The visualization agent returned a curated list of tools including Gosling.js, Ideogram.js, pyCirclize, DNA Painter, Genomelink, Promethease, and more

## How to Extend

The fastest way to add new variants:

1. Find the rsID and published research for the variant
2. Add an entry to `SNP_DB` with `id`, `gene`, `title`, `category`, `color`, and `interpret` function
3. Optionally add a `summary` function if it should appear in the top tile grid
4. Test by dropping a 23andMe file that contains the SNP

Any AI agent (Claude, GPT, Codex, etc.) can do this given the schema. The README includes the format. The whole codebase is one file so context is trivial.
