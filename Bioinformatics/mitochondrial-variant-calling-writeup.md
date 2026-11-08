# Mitochondrial Variant Calling: Finding and Interpreting DNA Differences

A bioinformatics project that compares one person's mitochondrial DNA to the standard human reference, finds the differences (variants), classifies them, and checks whether they change a protein. It includes a Python notebook and a short presentation explaining the biology behind it.

---

## The question

When you compare a person's DNA to a reference genome, where do they differ, what kind of changes are they, and could those changes matter?

This is called **variant calling**. It is the same basic idea used to look for cancer-risk gene changes (for example in *BRCA1*) or gene changes that affect how the body handles drugs (the *CYP450* family).

## Data

Both sequences are downloaded straight from NCBI with Biopython, so no data files need to be stored in this repo.

| Sequence | NCBI accession | Length |
|---|---|---|
| Reference (rCRS, the standard human mitochondrial genome) | `NC_012920.1` | 16,569 bases |
| Sample (one individual's mitochondrial genome) | `KY645908.1` | 16,569 bases |

Both are the same length, which makes a base-by-base comparison straightforward.

## What the code does

**1. Download the sequences.** `Entrez.efetch` pulls both FASTA files from NCBI and saves them locally.

**2. Load and clean them.** `SeqIO.read` reads each file, and the sequence is converted to uppercase so `a` and `A` are not treated as different.

**3. Pick a region to compare.** The comparison window is positions **16,024 to 16,383** (360 bases), part of the mitochondrial control region.

**4. Call the variants.** The code walks through both sequences one position at a time and records every spot where the sample differs from the reference. It also handles the case where the two regions have different lengths.

**5. Classify each change.** Every difference is labeled as a transition or a transversion (explained below).

**6. Check the effect on a protein.** The gene region at positions **3,307 to 4,262** (this is the *MT-ND1* gene) is translated into amino acids for both sequences using the **mitochondrial genetic code** (`table=2`), and the two proteins are compared.

## Results

**Four variants** were found in the 360-base region:

| Position | Reference | Sample | Type |
|---|---|---|---|
| 16,142 | C | T | Transition |
| 16,223 | C | T | Transition |
| 16,325 | T | C | Transition |
| 16,362 | T | C | Transition |

- **4 transitions, 0 transversions.**
- **0 amino acid changes** in the 318-amino-acid protein from the gene region checked.

### Transitions vs. transversions

DNA bases come in two shapes. A and G are the bigger two-ring bases. C and T are the smaller one-ring bases.

- **Transition:** swaps a base for one of the same shape (C to T, or A to G). This is the milder, more common kind of change.
- **Transversion:** swaps a base for one of the other shape (for example C to A). This is the rarer kind and tends to disrupt things more.

All 4 variants here are C-to-T or T-to-C, so all are transitions.

## Interpretation

Because every change is a transition, the pattern points toward common, everyday causes of DNA changes rather than something unusual:

- **Cytosine deamination:** a C loses a small chemical group and turns into uracil, which is then copied as a T.
- **DNA polymerase mistakes:** the enzyme that copies DNA occasionally puts in the wrong base, and these slips are usually transitions.

Changes like these are usually less severe. The working hypothesis from the presentation is that these four variants are **benign**.

Two other results support that view: the variants sit in the control region (a non-protein-coding stretch of the genome), and the protein check on the gene region found no amino acid changes. The four variant positions do not overlap with the gene region that was translated, so the protein result is consistent with them but does not test them directly.

## Proposed follow-up experiment

**Question:** Do these mitochondrial variants affect how well the cell makes energy (ATP)?

**Experiment:** An **ATP production assay** comparing cells from the patient to normal cells to see whether energy output differs.

## Limitations

- **One sample, one reference.** Differences from the reference are normal, and many mitochondrial differences simply reflect ancestry rather than disease.
- **Small window.** Only 360 of the 16,569 bases were compared for variants, and only one gene was translated.
- **Simple base-by-base comparison.** This works because both sequences are the same length. Insertions and deletions would need a proper alignment tool.
- **"Benign" is a hypothesis.** It is based on the type of change, not on lab evidence. That is why the ATP assay is proposed.

## How to run it

1. Install the requirements:
   ```bash
   pip install biopython pandas jupyter
   ```
2. In the first cell of the notebook, replace the email in `Entrez.email = "..."` with your own. NCBI asks for one when you download data.
3. Open the notebook and run the cells in order:
   ```bash
   jupyter notebook BioGeneticMutationAnalysis.ipynb
   ```

## Tools used

Python, Biopython (`Entrez`, `SeqIO`, `Seq`), pandas, Jupyter, NCBI GenBank.

## References

- Hillis et al. (2020). *Life: The Science of Biology* (12th ed.). Macmillan Learning.
- National Center for Biotechnology Information. NCBI. https://www.ncbi.nlm.nih.gov
- Biopython. http://biopython.org
- MedlinePlus. Variants and health (Mutations and genetic disorders). https://medlineplus.gov/genetics/understanding/mutationsanddisorders/mutations
- EMBL-EBI Training. Bioinformatics for the terrified: What is bioinformatics? https://www.ebi.ac.uk/training/online/courses/bioinformatics-terrified/what-bioinformatics/
- Learn.Genetics, University of Utah Genetic Science Learning Center. https://learn.genetics.utah.edu/
