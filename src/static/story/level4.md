ANOTHER DAY AT THE OFFICE
=====

analyzing structured data (part II)
--------

images/level4.jpg

"Colossus isn't the name of any protein in any database, unless you count this supplement powder I found online," Anita tells you. The two of you, along with the rest of the team, have spent the last week analyzing the genomic regions in the .bed file you retrieved from Oregon. Day after day you've crossed regions off your list. For the most part, the regions denoted locations near known protein-coding genes, none of which appear to have any real relationship to each other.

"Figures," you respond. You didn't expect the name to match anything, which is why Anita hadn't checked before now. You also note to yourself that you're being a little curt, probably because it's been an exasperating week, so you ask Joe to make a coffee run for the team. Joe laughs and says he'll be back soon. You're about to look at one of the last regions when your laptop shuts off on you. Of course! That's what you get for not charging the battery.

"Would you mind looking up chromosome 12, base pairs 123045 through 378909?" you ask Anita. "My computer just died." She says she's on it, so you make a quick lap around the office. A couple days ago, you decided to assign a few people to start investigating the Konrad brothers full-time. You ask them how they're doing, and they tell you that their systems should be fully functional by the end of the day. But they don't have anything to report at the moment. The pair of agents you have working on Life/Better, LLC, has a similar amount of material to report. And you know the rest of the team doesn't have anything, so you circle back to Anita.

"This region is just a gene desert. As far as I can tell, it doesn't do anything and isn't tied to any functions, diseases, or cancers," she informs you. Given the contents of the rest of the file, that doesn't make much sense. But then again, maybe you've been looking at this all backwards. You've spent this whole time looking at what's in or around the regions in this file. What if you should be more interested in what *isn't* there?

"What if they were looking for a gene insertion site?" you hypothesize aloud. The total lack of connections between the contents of the sites would then make sense: just the fact that the site had contents was the connection.

"Hmm...how about this theory, then?" starts Anita. "What if they're looking for a site to add this Colossus protein, whatever it is? If that were the case, I bet we could use Theraptrix's protein order forms for the past year and cross-reference those with the database of proteins which other researchers have inserted into this site. We also have phenotype data for these experiments. Maybe there will be a clue in there somewhere."

"Sounds like a stretch to me." That's what you want to say. But you don't have any better ideas. So instead you sit down and get to work.

---

### Problem

You have two files: `theraptrix_protein_orders.txt`, which contains a list of names of all the proteins that Theraptrix has purchased recently, and `locus_data.tab`, which contains tabular data of a series of experiments. The tab file has the following format:

1. **(Unnamed Column):** Unique numbers for the experiments.
2. **Protein:** The name of the protein inserted into the locus. These names correspond to the names found in `theraptrix_protein_orders.txt`.
3. **Cell Type:** The name of the cell line used in the experiment.
4. **Exp p-value:** A p-value indicating how strongly the protein is expressed in the experiment.
5. **Exp Sig:** A boolean value indicating if the protein is expressed highly enough to meet the threshold for confidence in the experimental results.
6. **'tumor', 'protein synthesis', 'lipid synthesis', 'growth', 'cell cycle', 'cytoskeletal activity', 'apoptosis':** Each of these columns represents how correlated the expression of the "Protein" in the "Cell Type" is with the given phenotype. For example, DD6X, when expressed in HSkMC cells, has a correlation value of 0.797 with "cytoskeletal activity".

Your program should take paths to both `locus_data.tab` and `theraptrix_protein_orders.txt` as parameters. Filter `locus_data.tab` by removing rows where the expression of the protein is not significant or the protein name is not in `theraptrix_protein_orders.txt`. Then, for each cell type within the filtered data set, average the correlation values of all proteins for each individual phenotype. Write the maximum average correlation value (rounded to 3 decimals) among all of the average values, along with the cell type and phenotype corresponding to this max value, to the file `results/4.txt`.


**Note(s):**

* Each protein is only measured once per cell type.
* You need to find the average across all proteins in a cell type. For example, if proteins “A”, “B”, and “C” were tested in K562 cells, you could do (“A”+”B”+”C”)/3.
* To summarize, you're asking, "Which single phenotype is most strongly displayed in which single cell type when these proteins are expressed?"

##### Example

Contents of `theraptrix_protein_orders.txt`:

    CSTF2
    CSTF2T
    DDX3X
    DDX55

Contents of `locus_data.tab`:

      Protein Cell Type Exp p-value Exp Sig tumor growth apoptosis
    0 CSTF2   SK-N-SH   0.011       True    -0.5  -0.4   -0.3
    1 CSTF2T  SK-N-SH   0.032       True    0.1   0.2    0.3
    2 DDX3X   SK-N-SH   0.061       False   0.3   0.2    0.4
    3 CSTF2   K562      0.020       True    -0.9  -0.4   0.7
    4 CSTF2T  K562      0.047       True    0.6   0.7    -0.3
    5 DDX3X   K562      0.079       False   0.3   -0.5   0.2
    6 ILF3   K562      0.019       True    0.1   0.2    0.8

This minimal example is also provided [here as a file](https://github.com/Jessime/Excision/blob/dev/src/examples/locus_data_ex.tab).

**Example Execution:**

`$ python pheno_corr.py /path/to/locus_data.tab /path/to/theraptrix_protein_orders.txt`

**Example Result:**

    0.200
    ('K562', 'apoptosis')

---

### Task

Before you get started on this task, [here is the documentation](http://pandas.pydata.org/pandas-docs/stable/index.html) for `pandas`, one of the most popular data analysis tools in Python. Use this documentation to write a function called `abc_df`. `abc_df` will take a 2D `numpy` array as a parameter. The array will have a shape of no greater than (26, 26). `abc_df` should return a `DataFrame` where the data of the `DataFrame` is the `numpy` array and both the indices and the columns are labeled with letters.

#### Hint

Yet another useful way to read in a file is the pandas [`read_csv`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html) method or, equivalently, [`read_table`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_table.html). `pandas` has numerous methods for I/O, so if you're going to be parsing a file into a `DataFrame` always check if `pandas` has a built-in function before manually parsing the file.

---

### Task

Write `most_common_index`, a function which, when applied to a `DataFrame`, returns the count (i.e., the number of times the element occurs) of the index element which occurs most often. The maximum count will be unique.

Note: If you're used to R, pandas DataFrames are slightly different in that they allow an index to be duplicated. R requires all index values to be unique.

#### Hint

The `pandas` [documentation on indexing](https://pandas.pydata.org/pandas-docs/stable/indexing.html) is enormous, because indexing in `pandas` can be a little overwhelming. Chris Albon has a nice TL;DR version you can [find here](https://chrisalbon.com/python/pandas_indexing_selecting.html). One small caveat, though: you should no longer use the `ix` method he mentions at the bottom of the page.

Also, Chris has a lot of helpful tutorials. You should poke around his page some.

---

### Task

Create a function called `last_col_median`. Given a `DataFrame` as a parameter, `last_col_median` should attempt to find the median of the last column of the `DataFrame`. If the median cannot be found because the last column does not have a numeric data type, `last_col_median` should simply return [`None`](https://docs.python.org/3/library/constants.html#None).

#### Hint

Hopefully, you've noted the structural similarities between this problem and Level 3. This problem was written to emphasize how much `pandas` can simplify things.

Of course, you may not agree with this point if this is your first time working with `pandas` or with a `DataFrame`. There can be a bit of a learning curve. Because of this potential learning curve, this is a rather short problem (in terms of lines of code). The last method you need to write this script succinctly is [`groupby`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.groupby.html).

Bonus hint: This task would have been a nice time to use [try/except](https://docs.python.org/3/tutorial/errors.html).
