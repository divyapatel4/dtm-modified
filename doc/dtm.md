# Dynamic Topic Model (DTM)

## Input data format

The ``dtm`` tool requires at least two input files: one for the description of each document
and its respective terms; and another to identify the time slices to be analyzed.

The first file, usually defined with the name ``??? - mult.dat``, contains M lines, where M
is the number of documents to be analyzed. Documents must be ordered by date, in ascending order.
Each line describes a document, its terms and the quantity of each term in the document,
according to the following format:

```
unique_word_count index1: count1 index2: count2 ... index<n>: count<n>
```

There is no identifier for a document: the line number is used for that purpose. The terms
that make up the document are also not declared in a textual way: a numeric identifier must
be adopted for each term. This identifier must be unique for the same term in relation to all
documents (see this as an optimization: it is faster to process a number than a word). 
The count corresponds to the absolute frequency of the term in the current document/row.
Finally, the first element of the line indicates the size of the vocabulary needed to describe
the document (see this as a facilitator to read the remaining line data).

For example, consider the following documents:

```
Document 1 (Kulesza-etal2007):
The development of collaborative and multimedia systems is a
complex task and one of the key challenges is to promote the reuse and
integration of those two software categories in the same environment.

Document 2 (Bezerra-Wainer2006):
This work shows a model to detect a set of anomalous traces in a log
generated by a business process management system.
```

Their definition in the DTM format DTM is as follows:

```
24 1: 4 2: 1 3: 3 4: 1 5: 3 6: 1 7: 1 8: 2 9: 1 10: 1 11: 1 12: 1 13: 1 14: 1 15: 1 16: 1 17 : 1 18: 1 19: 1 20: 1 21: 1 22: 1 23: 1 24: 1
17 3: 1 9: 4 14: 1 22: 1 25: 1 26: 1 27: 1 28: 1 29: 1 30: 1 31: 1 32: 1 33: 1 34: 1 35: 1 36: 1 37 :1
```

Note that for the second document, some terms that had already been used to specify the first document
appeared. Therefore, the index that identifies the term is the same (3, 9 and 14), although the count
is specific to each document (the term 3 appeared 3 times in the first document and only 1 time in the
second document). 

To facilitate the subsequent analysis of the data, it is necessary to keep the
information of which term corresponds to each term identifier and
which document corresponds to each document identifier.

For the first case, you must create a file named ``vocab`` with the
vocabulary used in the analyzed collection. The format of this file is
very simple: you put one term per line, with the line number
matches the term identifier.


```
1 the
2 development
3 of
4 collaborative
5 and
6 multimedia
7 systems
8 is
9 a
10 complex
11 task 
12 one
13 challenges
14 to
15 promote
16 reuse
17 integration
18 those
19 two
20 software
21 categories
22 in
23 same
24 environment
25 this
26 work
27 shows
28 model
29 detect
30 set
31 log
32 generated
33 by
34 business
35 process
36 management
37 system
```

For the second case, you must create a file named ``docs`` with the name of each document,
organized in the same order as they were specified in the input file (``???-mult.dat``).
In this way, the data can be later recovered, facilitating the analysis of the results by
researchers.

The second DTM input file defines the time slices that will be analyzed. This file, usually
named ``???-seq.dat``, has the following format:

```
Number_Timestamps
number_docs_time_1
...
number_docs_time_i
...
number_docs_time_NumberTimestamps
```

The first line determines the number of time slices to be analyzed. The following lines specify how
many documents are part of each slice, in ascending order. These documents are obtained in sequence,
from the beginning to the end of the file that describes the data collection to be analyzed. Likewise
that file describes one document per line, the ``M_1`` will be used as first lines for the first time
slice, the next ``M_2`` lines for the second time slice and so on.


## Configuration

### Flags from data.c:
* ``-influence_flat_years``:  How many years is the influence nonzero? If nonpositive, a lognormal distribution is used.) type: int32, default: -1
* ``-influence_mean_years``: How many years is the mean number of citations?) type: double, default: 20
* ``-influence_stdev_years``: How many years is the stdev number of citations?) type: double, default: 15
* ``-max_number_time_points``: Used for the influence window. type: int32, default: 200
* ``-resolution``:  The resolution.  Used to determine how far out the beta mean should be.) type: double, default: 1
* ``-sigma_c``:  c stdev.  type: double, default: 0.050000000000000003
* ``-sigma_cv``: Variational c stdev. type: double, default: 9.9999999999999995e-07
* ``-sigma_d``:  If true, use the new phi calculation. type: double, default: 0.050000000000000003
* ``-sigma_l``:  If true, use the new phi calculation. type: double, default: 0.050000000000000003
* ``-time_resolution``: This is the number of years per time slice. type: double, default: 0.5
      
## Flags from gsl-wrappers.c:
* ``-rng_seed``: Specifies the random seed.  If 0, seeds pseudo-randomly. type: int64, default: 0

## Flags from lda-seq.c:
* ``-fix_topics``: Fix a set of this many topics. This amounts to fixing these  topics' variance at 1e-10. type: int32, default: 0
* ``-forward_window``: The forward window for deltas. If negative, we use a beta with mean 5. type: int32, default: 1
* ``-lda_sequence_max_iter (The maximum number of iterations.) type: int32, default: 20
* ``-lda_sequence_min_iter (The maximum number of iterations.) type: int32, default: 1
* ``-normalize_docs (Describes how documents's wordcounts are considered for finding influence. Options are "normalize", "none", "occurrence", "log", or "log_norm".) type: string, default: "normalize"
* ``-save_time (Save a specific time.  If -1, save all times.) type: int32, default: 2147483647

## Flags from lda.c:
* ``-lambda_convergence (Specifies the level of convergence required for lambda in the phi updates.) type: double, default: 0.01

## Flags from main.c:
* ``-alpha``: type: double default: -10
* ``-corpus_prefix``: The function to perform. Can be dtm or dim. type: string, default: ""
* ``-end``: type: int32 default: -1
* ``-heldout_corpus_prefix``: type: string default: ""
* ``-heldout_time``: A time up to (but not including) which we wish to train, and at which we wish to test. type: int32, default: -1
* ``-initialize_lda``: If true, initialize the model with lda. type: bool, default: false
* ``-lda_max_em_iter``: type: int32 default: 20
* ``-lda_model_prefix``: The name of a fit model to be used for testing likelihood.  Appending "info.dat" to this should give the name of the file. type: string, default: ""
* ``-mode``: The function to perform. Can be fit, est, or time. type: string, default: "fit"
* ``-model``: The function to perform. Can be dtm or dim. type: string, default: "dtm"
* ``-ntopics``: type: double, default: -1
* ``-outname``: type: string, default: ""
* ``-output_table``: type: string default: ""
* ``-params_file``: A file containing parameters for this run. type: string, default: "settings.txt"
* ``-start``: type: int32 default: -1
* ``-top_chain_var``: type: double default: 0.0050000000000000001
* ``-top_obs_var``: type: double default: 0.5



# Running

This program takes as input a collection of text documents and creates as output a list of topics over time,
a description of each document as a mixture of these topics, and (possibly) a measure of how "influential"
each document is, based on its language.

We have provided an example dataset, instructions for formatting input
data and processing output files, and example command lines for
running this software in the file dtm/sample.sh.


## Topic estimation

```
./main \
  --ntopics=20 \
  --mode=fit \
  --rng_seed=0 \
  --initialize_lda=true \
  --corpus_prefix=example/test \
  --outname=example/model_run \
  --top_chain_var=0.005 \
  --alpha=0.01 \
  --lda_sequence_min_iter=6 \
  --lda_sequence_max_iter=20 \
  --lda_max_em_iter=10
```

## Topic inference

```
./main \
    --mode=fit \
    --rng_seed=0 \
    --model=fixed \
    --initialize_lda=true \
    --corpus_prefix=example/test \
    --outname=example/output \
    --time_resolution=2 \
    --influence_flat_years=5 \
    --top_obs_var=0.5 \
    --top_chain_var=0.005 \
    --sigma_d=0.0001 \
    --sigma_l=0.0001 \
    --alpha=0.01 \
    --lda_sequence_min_iter=6 \
    --lda_sequence_max_iter=20 \
    --save_time=-1 \
    --ntopics=10 \
    --lda_max_em_iter=10
```
    
The ``dtm`` program will create the following files:

\ begin {itemize}
\ item \ srccode {topic - ??? - var-e-log-prob.dat}: the distribution of
words (e-betas) for the topic ??? for each analyzed period.

The data contained in the file is in the format \ foreign {row-major},
that is, the table rows are stored one after the other.
Each row, in turn, has an equivalent number of columns
the amount of time fractions analyzed. For example, if
10 time slices were analyzed, the command to read the relative data
to topic 2 would be, in R:

\ begin {lstlisting} [language = R]
a = scan ("topic-002-var-e-log-prob.dat")
b = matrix (a, ncol = 10, byrow = TRUE)
\ end {lstlisting}

Each cell in the matrix has the natural logarithm of the probability of
term M (where M is the term identifier number, as defined
in the vocabulary and in the input matrix), which is in line M of the
matrix, in relation to the topic identified by the number N, which
corresponds to the matrix column. For example, to get the
probability of term 100 for time slice 3, the command would be:

\ begin {lstlisting} [language = R]
exp (b [100, 3])
\ end {lstlisting}


\ item \ srccode {gam.dat}: Stores the parameters of the variational Dirichlet
for each document.


Divide these by the sum for each document to get expected topic mixtures.
\ begin {lstlisting} [language = R}
a = scan ("gam.dat")
b = matrix (a, ncol = 10, byrow = TRUE)
rs = rowSums (b)
e.theta = b / rs
# Proportion of topic 5 in document 3:
e.theta [3, 5]
\ end {lstlisting}


\ item [\ srccode {influence_time - ???}]: Stores the influence of documents in the
time slice ??? for each topic. Each line in the file corresponds to the
document M, such identifier M being equivalent to the line on which the document
in question is in the input matrix (-mult). Each column corresponds
to a topic N. For example, to get the influence of document 2 on the topic
5, the commands in R would be:

\ begin {lstlisting} [language = R]
a = scan ("influence-time-010")
b = matrix (a, ncol = 10, byrow = TRUE)
b [2, 5]
\ end {lstlisting}
\ end {description}


The analysis of all these files can be automated as follows:

\ begin {lstlisting} [language = R]
# For a topic
data0 = scan ("topic-000-var-e-log-prob.dat")
b0 = matrix (data0, ncol = 10, byrow = TRUE)
write.table (b0, file = "dist-topic0.csv", sep = ";")


# Processa todos t??picos
# Para cada t??pico, gera um arquivo com a probabilidade de cada
# termo para cada ano
# TODO: rodar exp() nos valores
topics = list()
for (i in 0:9) {
	filename = paste("topic-00", i, sep = "")
	filename = paste(filename, "-var-e-log-prob.dat", sep = "")
	data = scan(filename)
	topic = matrix(data, ncol=10, byrow=TRUE)
	filename = paste("dist-topic", i, sep = "")
	filename = paste(filename, ".csv", sep = "")
	write.table(topic, file=filename, sep=";")
}


# - gam.dat: The gammas associated with each document.  Divide these by
#  the sum for each document to get expected topic mixtures.
# Proportion of topic 5 in document 3:
# e.theta[3, 5]
a = scan("gam.dat")
b = matrix(a, ncol=10, byrow=TRUE)
rs = rowSums(b)
e.theta = b / rs
write.table(e.theta, file="documents_topics.csv", sep=";"
\end{lstlisting}
