## Using Biopython's `Bio.Entrez` package to Access NCBI's Entrez databases

This IPython Notebook is based on Chapter [9](http://biopython.org/DIST/docs/tutorial/Tutorial.html#sec118) of the [Biopython Tutorial and Cookbook](http://biopython.org/DIST/docs/tutorial/Tutorial.html), "Accessing NCBI’s Entrez databases". 

Related documents supplied by the [National Center for Biotechnology Information (NCBI)](http://www.ncbi.nlm.nih.gov/) include:

* [A General Introduction to the E-utilities](http://www.ncbi.nlm.nih.gov/books/NBK25497)
* [The E-utilities In-Depth: Parameters, Syntax and More](http://www.ncbi.nlm.nih.gov/books/NBK25499)

The Introduction to the first NCBI document offers the following overview:

> The Entrez Programming Utilities (E-utilities) are a set of nine server-side programs that provide a stable interface into the Entrez query and database system at the National Center for Biotechnology Information (NCBI). The E-utilities use a fixed URL syntax that translates a standard set of input parameters into the values necessary for various NCBI software components to search for and retrieve the requested data. The E-utilities are therefore the structured interface to the Entrez system, which currently includes 38 databases covering a variety of biomedical data, including nucleotide and protein sequences, gene records, three-dimensional molecular structures, and the biomedical literature.

My primary goal was to learn how to use `Bio.Entrez` to conduct citation analysis on [PubMed](http://www.ncbi.nlm.nih.gov/pubmed) articles. I find the best way to learn how to use nearly any new [Python](http://www.python.org) tool is to create an [IPython Notebook](http://ipython.org/notebook.html) to experiment with sample code and data. I am sharing this notebook in case others find it useful ... and my intention to share the notebook helps motivate me to learn the material better than I might otherwise.

I have elaborated on some sections in Chapter [9](http://biopython.org/DIST/docs/tutorial/Tutorial.html#sec118) of the [Biopython Tutorial and Cookbook](http://biopython.org/DIST/docs/tutorial/Tutorial.html), inserted links to relevant NCBI and Python documentation, adopted more consistent coding and naming conventions (the Biopython document was developed and revised by different people at different times), corrected some errors (e.g., variable names, file names) and added some error checking (try/except statements) in Section [9.12 (Handling Errors)](#9.11--Handling-errors) so that all the cells in the notebook can be run without interruption.

I addressed a TODO item in Section [9.15.3](http://biopython.org/DIST/docs/tutorial/Tutorial.html#sec143) of the Biopython Tutorial and Cookbook - see the section near the bottom, [Update: Searching for citations using ELink, EPost and EFetch with history](#Update:-Searching-for-citations-using-ELink,-EPost-and-EFetch-with-history) below. I encountered an [`HTTPError`](https://docs.python.org/library/urllib2.html#urllib2.HTTPError) while working out a solution to using ELink and EFetch with the Entrez History server (I initially omitted an EPost call in between those two). Entrez defines an internal `_HTTPError` exception that does not appear to be intended for reuse, but I found it useful to access that, so I added another new section at the bottom, [update on handling HTTPErrors](#Update:-Handling-HTTPErrors).

To make the notebook as self-contained as possible, I replaced absolute linked references to sections in Chapter 9 with relative links, and then inserted anchors into the corresponding sections in this notebook (using a technique suggested by [Sebastian Raschka](http://sebastianraschka.com/) in his IPython Notebook, [Creating a table of contents with internal links in IPython Notebooks and Markdown documents](http://nbviewer.ipython.org/github/rasbt/python_reference/blob/master/tutorials/table_of_contents_ipython.ipynb)). 

The [Biopython wiki](http://biopython.org/wiki/Main_Page) has instructions for [downloading and installing Biopython](http://biopython.org/wiki/Download) on various platforms. I use [Anaconda](https://store.continuum.io/cshop/anaconda/) on a Mac, and simply used 

`conda install biopython` 

on a Terminal command line for my installation.

A final note: when I first tried to run some of the examples in Chapter 9, warning messages indicated that I needed to install the following DTD files, either in a system-wide Python library subfolder (//anaconda/lib/python2.7/site-packages/Bio/Entrez/DTDs) or in a user-specific subfolder (~/.biopython/Bio/Entrez/DTDs). I chose to install them in the system-wide folder:

* http://eutils.ncbi.nlm.nih.gov/eutils/dtd/20130322/einfo.dtd
* http://eutils.ncbi.nlm.nih.gov/eutils/dtd/20131226/efetch.dtd
* http://eutils.ncbi.nlm.nih.gov/eutils/dtd/20060628/esearch.dtd
* http://eutils.ncbi.nlm.nih.gov/eutils/dtd/20060131/esummary-v1.dtd
* http://www.ncbi.nlm.nih.gov/corehtml/query/DTD/bookdoc_140101.dtd
* http://www.ncbi.nlm.nih.gov/corehtml/query/DTD/nlmmedlinecitationset_140101.dtd
* http://www.ncbi.nlm.nih.gov/corehtml/query/DTD/pubmed_140101.dtd