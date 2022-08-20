# Fastq

## Fast grep

```bash
LC_ALL=C fgrep <text> <file>
```

## FASTQ stats

[Reference](https://expressionanalysis.github.io/ea-utils/).

```bash
sudo apt install ea-utils
fastq-stats <file>
```

## FASTQ manipulation

[Reference](https://github.com/lh3/seqtk).

```bash
sudo apt install seqtk
```

Convert multi-line FASTQ to 4-line FASTQ.

```bash
seqtk -l0 <input> > <output>
```
