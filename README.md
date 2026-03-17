# Genome Dashboard

A single-file, client-side genome analyzer and visualizer. Drop your 23andMe (or AncestryDNA) raw data file into your browser and get an interactive dashboard with trait analysis, health risk assessment, pharmacogenomics, and more.

**100% private.** Your genome data never leaves your browser. Everything runs locally in JavaScript. No server, no uploads, no tracking.

![screenshot](https://img.shields.io/badge/SNPs_Analyzed-100%2B_variants-6366f1) ![license](https://img.shields.io/badge/license-MIT-green)

## Features

- **Instant analysis** of 100+ well-studied SNPs across 9 categories
- **Interactive chromosome karyogram** showing SNP density across all chromosomes
- **Neurotransmitter radar chart** comparing your profile to population averages
- **Health risk bar chart** with color-coded risk levels
- **Animated DNA helix** hero visualization
- **Tabbed interface** with detailed cards for every analyzed variant:
  - Identity & Traits (eye color, skin, hair, blood type, taste, earwax)
  - Brain & Neuro (COMT, BDNF, DRD2, OPRM1, oxytocin, serotonin, DBH)
  - Health & Risk (APOE, BRCA, Factor V Leiden, T2D, FTO, MTHFR, celiac)
  - Athletic (ACTN3 muscle fiber type, adrenergic receptors)
  - Pharmacogenomics (CYP1A2, CYP2C19, SLCO1B1, VKORC1, TPMT)
  - Inflammation & Immune (IL-6, TNF-alpha, secretor status)
  - Nutrition & Metabolism (lactose, caffeine, omega-3, alcohol flush)
  - Sleep & Circadian (CLOCK gene, melatonin receptor)
  - Aging & Misc (telomere length, DPYD safety, smoking response)
- **Genotype-aware interpretation** — every card dynamically interprets your specific alleles
- **Works with any AI agent** — the SNP database is structured for easy extension

## Usage

### Option 1: Open directly
Just open `index.html` in any modern browser and drop your file.

### Option 2: Serve locally
```bash
npx serve .
# or
python -m http.server 8000
```

### Option 3: Use with AI agents
Feed this file to Claude, GPT, Codex, or any AI coding agent and ask them to:
- Add new SNPs to the `SNP_DB` array
- Create new analysis categories
- Extend the interpretation logic
- Generate personalized health recommendations

The SNP database format is self-documenting:
```javascript
{
  id: 'rs12913832',          // rsID
  gene: 'HERC2/OCA2',       // gene name
  title: 'Eye Color',        // display title
  category: 'identity',      // tab category
  color: 'blue',             // card accent color
  interpret: g => { ... },   // genotype -> interpretation
  summary: g => { ... }      // optional quick summary
}
```

## Supported Formats

- 23andMe v3/v4/v5 raw data (`.txt`)
- AncestryDNA raw data (`.txt`, comma-separated)

## How It Works

1. Your browser reads the raw genome file (typically 15-25 MB of text)
2. Each line is parsed into rsID, chromosome, position, and genotype
3. The built-in SNP database is checked against your genotypes
4. Interpretations are generated based on published research
5. Charts and visualizations are rendered using Chart.js

No data is transmitted. No cookies. No analytics. View source to verify.

## Extending the SNP Database

Adding a new variant is as simple as pushing to the `SNP_DB` array:

```javascript
SNP_DB.push({
  id: 'rs1234567',
  gene: 'GENE_NAME',
  title: 'What It Does',
  category: 'health',  // identity|neuro|health|athletic|pharma|inflammation|nutrition|sleep|aging
  color: 'green',      // green|yellow|red|blue|purple|cyan|pink|orange
  interpret: genotype => {
    if (genotype === 'AA') return { tag: 'Result', tagClass: 'tag-normal', detail: 'Explanation...' };
    if (genotype === 'AG') return { tag: 'Result', tagClass: 'tag-carrier', detail: 'Explanation...' };
    if (genotype === 'GG') return { tag: 'Result', tagClass: 'tag-risk', detail: 'Explanation...' };
    return null;
  }
});
```

Tag classes: `tag-normal` (green), `tag-carrier` (yellow), `tag-risk` (red), `tag-info` (blue), `tag-notable` (purple).

## Disclaimer

This tool is for **educational and informational purposes only**. It is NOT medical advice. Genotyping chips test only a subset of known variants. Many traits and conditions are polygenic and heavily influenced by environment, lifestyle, and epigenetics. Always consult a certified genetic counselor or physician for clinical interpretation.

## License

MIT
