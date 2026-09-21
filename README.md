# conjectures-compression-corpus-1

The **public** scoring corpus for the miniz-oxide DEFLATE competition: 28 files,
15,930,000 bytes, cut from a ~900 MB pool of real-world data.

This is the set you tune against. The validator scores submissions on it and on a
held-out stage 2 that is drawn from the disjoint half of the same pool — same
distribution, different bytes. Doing well here should mean doing well there; that is
the whole design, and it is measured (every provable candidate matches across the two
stages to within 0.001 of its ratio against the incumbent).

## Using it

The competition repository pulls this repo into `data/benchmark/corpus-stage1/`:

```bash
just corpus-pull          # clones or fast-forwards this repo
```

Nothing else is required. You do **not** need the source pool to compete.

## These bytes are the specification

The corpus is distributed as bytes rather than as a builder plus a seed, and that is
deliberate. 38 of the 73 upstream sources are floating URLs — eighteen GitHub branch
heads, live Wikipedia, current npm metadata, CSVs regenerated daily, an arXiv OAI query
that answers differently on every call. A fresh download is not the pool this was cut
from, so two people building from the same seed a week apart would get different
corpora and rank the same submission differently.

So: **what is committed here is the corpus.** The builder reproduces it only against a
pool that has not drifted, which is a useful check and not a distribution mechanism.

## Contents

| file | bytes |
|---|---:|
| `corpus/binary.db.bin` | 500,000 |
| `corpus/bundle.min.js.txt` | 1,000,000 |
| `corpus/catalog.xml.txt` | 500,000 |
| `corpus/compressed.bin` | 300,000 |
| `corpus/config.yaml.txt` | 400,000 |
| `corpus/docs.md.txt` | 800,000 |
| `corpus/dump.sql.txt` | 150,000 |
| `corpus/genome.fasta` | 900,000 |
| `corpus/images.bin` | 250,000 |
| `corpus/lean.txt` | 1,000,000 |
| `corpus/machine-code.bin` | 500,000 |
| `corpus/metrics.csv.txt` | 1,300,000 |
| `corpus/multibyte.txt` | 1,100,000 |
| `corpus/page.html.txt` | 600,000 |
| `corpus/prose.txt` | 1,500,000 |
| `corpus/records.json.txt` | 400,000 |
| `corpus/server.log` | 300,000 |
| `corpus/source.c.txt` | 1,400,000 |
| `corpus/source.py.txt` | 600,000 |
| `corpus/source.rs.txt` | 700,000 |
| `corpus/sourcemap.map.txt` | 350,000 |
| `corpus/sparse.bin` | 40,000 |
| `corpus/tiny-app.log` | 28,000 |
| `corpus/tiny-config.json.txt` | 12,000 |
| `corpus/weights-bf16.bin` | 450,000 |
| `corpus/weights-f16.bin` | 250,000 |
| `corpus/weights-f32.bin` | 350,000 |
| `corpus/weights-q8.bin` | 250,000 |

27 of the 28 are sliced from real data: source in four languages, Gutenberg prose,
Chinese/Japanese/Russian text, Wikipedia HTML, production logs, npm and Maven metadata,
airport and epidemiological CSVs, real SQL dumps, minified bundles and source maps, NCBI
genomes, SQLite databases, PNG/JPEG, jars, WebAssembly, and five real model checkpoints
(all-MiniLM-L6-v2 F32, pythia-70m F16, SmolLM2-135M BF16, and SmolLM2 quantized to Q8_0
and Q4_K_M). The one exception is `sparse.bin` — 40 KB of zero runs, 0.26% of the bytes
and 0.00% of the headroom.

The `weights-*.bin` parts hold tensor data only; GGUF headers and the shared tokenizer
vocabulary are stripped, because otherwise two different quantizations profile as the
same file.

## Where it comes from, and under what licence

[`SOURCES.md`](SOURCES.md) lists all 73 downloads with their URLs and stated licences,
plus what each corpus file was cut from and how many bytes it took from each. It is
generated from the download manifest and the byte counts recorded while pooling, so it
cannot drift from what was actually used.

It is a record of what each project states about itself, not a vetted legal review.

[`formats.json`](formats.json) maps each corpus filename to a format label, so the
competition's benchmark analysis can group and normalize by file format instead of
pooling raw bytes. Same reasoning as `SOURCES.md`: generated in the competition
repository, copied here so this repo is self-contained.

## Regenerating

You do not need to, and a rebuild will not match unless your pool is the one this was
cut from. If you want to anyway, from a checkout of the competition repository:

```bash
just corpus-sources                       # ~900 MB, one time
just corpus-build --stage 1 --repool
just corpus-verify                        # rebuilds and diffs against these bytes
```

`corpus-verify` exits 3 when the pool has drifted — expected, eventually, and it breaks
nothing. These committed bytes remain the reference.
