Reference QA Benchmarking Dataset
=================================

This repository contains **factoid-curated**, a reference question dataset
for benchmarking Question Answering systems, as used e.g. by the YodaQA
system.

The complete dataset is a combination of two sub-datasets (irc/ and trec/)
and consists of three files:

  * ``curated-full.tsv`` is the complete set of questions that are part
    of this dataset.  If you modify this set, you should also tweak the
    appropriate sub-dataset.

  * ``curated-train.tsv`` is the standard *train* split of the dataset, for
    primary development, detailed performance analysis and training (and
    if possible also testing) machine learning algorithms.

  * ``curated-test.tsv`` is the standard *test* split of the dataset, for
    benchmarking and reporting system performance.  Everyone should attempt
    to treat this dataset as "blind" and do not analyze or optimize
    performance for individual questions in this dataset!

(Some small portion of questions may be left out of the splits; these are
left reserved for future use, e.g. as part of a validation dataset.)

Ideally, humans should be doing all stages of evaluation instead of just
using regex matches, as time by time an unconcieved legitimate answer
pops up and on the other hand, sometimes the regex is unintentionally
over-permissive.  For a similar reason (to allow e.g. leading the/a
and other variations), we declare the answer as correct when the regex
matches any substring.


Using This Dataset
------------------

As explained above, please use the test dataset only for performance
reporting, not for question-by-question error analysis.  Always report
the version of the dataset you used - this is the LARGE dataset (make
sure to also include the particular commit id) which aims to put
together a complementary, significantly larger training dataset
to explore overfitting behavior of systems.

To make results comparable, it is not enough to use the same set of
questions, we should strive to use the same or similar knowledge bases
as well; we realize that especially as time goes, this might not be
practical, but some degree of effort would be appreciated.  We also
expect the primary public YodaQA endpoints to track the latest version
of this dataset, so these could be reused.  We use:

  * enwiki-20150112 (archived at http://v.or.cz/~pasky/brmson/)
  * Freebase RDF dump from Jan 11 2015 (but it's probably not very volatile)
  * DBpedia 2014
  * WordNet 3.1


Aims of This Dataset
--------------------

We want to build a dataset of questions, which are:

  * Factoid.  This means that there is only a single answer (not a list
    or a sequence [of steps]), the answer is typically a simple phrase
    (not a story or topic summary; definition query like "Who is Obama?"
    is borderline and does not occur much) and the question is not
    a boolean yes/no (which requires quite different techniques to answer).

  * Wikipedia focused.  Wikipedia is a vast, unstructured repository of
    knowledge, so the bias is not terribly high, but we would like to think
    that a human with Wikipedia in hand would be able to answer all of these
    questions.  Most of them should require just a straightforward
    information retrieval, some are more challenging and might need some
    inference.

We may want to relax either requirement, but at that point we should start
tagging the questions to still keep a set of "simpler" ones.  The motivation
is to give a chance even to simple, focused systems.


Large Variant of the Dataset
----------------------------

An extra variant of the dataset is provided, which is a strict superset
of the curated dataset.  It is *not* curated, i.e. it is a lot more noisy
and may not fulfill the above constraints.  The aim of this dataset is to
check behavior of QA systems when given larger, noisy training data.

The **large2180** dataset is built by adding TREC 1999, 2000, 2001 data
to the curated dataset.  Unmodified questions of each year were shuffled
and 80% were added to the train split, 20% to the test split.  This way,
we fulfill our goal of boosting the training set, while propagating
possible per-year biases to the test set as well and somewhat controlling
for benchmark noise.  However, it may be also valid to check system
performance with **large2180** training and **curated** testing set;
XXX this is still not finalized, we may yet decide to keep the original
testing set and throw everything in just the training set!

Note that while **curated-train** is to be used also as the development
set, **large2180-train** *should not* be used for development.  We may
relax this based on some future consensus, but for now let's be conservative.

The dataset is called **large2180**, which refers to the number of questions
in the dataset.  We may build even larger datasets (e.g. including the
WebQuestions, TREC years 2004+, QALD challenges or such) of similar nature
in the future.

## Discussion: How much data is appropriate?

It might be obvious to just use the largest dataset possible, but there
are three issues with that:

  * Development convenience.  Many QA systems are pretty slow, and just
    providing a big dataset may tempt system authors to create custom
    splits of the datasets for their development, something we would like
    to discourage.

  * Noise.  The large2180 dataset is already relatively low quality,
    some questions are really arbitrary and not answerable using public
    knowledge bases.  The problem is even worse when we also consider the
    answer patterns, which may unfairly disqualify many correct answers,
    and worse this adds question type bias (probably almost no numerical
    quantity answers will pass the pattern muster, but years or names
    typically will).  Beyond the large2180 dataset, e.g. the WebQuestions
    dataset also contains typos in questions or downright mistakes; this
    is not unrealistic, but adds extra burden to authors of early systems;
    furthermore, the answer patterns based on Freebase will create an
    unfair bias towards FB in multi-source systems.  So the noise issue
    is not just about noisy measurements, but also introducing biases,
    not all of them obvious.

  * Realistic setting.  In the end, one aspect of QA systems we consider
    important is domain adaptability; but when moving the QA system to
    a particular domain, it is unlikely there will be a large dataset
    available to train it.  So observing the *scaling behavior* of a
    system and its *small-data performance* is completely reasonable.
