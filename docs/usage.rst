=====
Usage
=====

LitterGetter currently creates interfaces for three different literature databases:

* `Pubmed`_
* EPA `HERO`_
* `RIS`_ exports from Endnote/RefManager

The data are currently extracted as list of dicts, using a preset, opinionated,
dictionary format. This could probably be revised for increased flexibility; if
you have a need, feel free to submit an issue.

PubMed
------

The PubMed API requires a registration of tools, in order to use the API. Therefore,
before making an pubmed requests, you must register a tool and valid email address:

.. code-block:: python

    from litter_getter import pubmed

    pubmed.connect('TOOL_NAME', 'email@nobody.com')


After submitting your tool credentials, a query can be submitted. The number of
references returned and the IDs for the references can be extracted:

.. code-block:: python

    from litter_getter import pubmed

    search_term = """science[journal] AND breast cancer AND 2008[pdat]"""
    search = pubmed.PubMedSearch(term=search_term)

    search.get_ids_count()
    search.id_count
    >>> 123

    search.get_ids()
    >>> ['19008416', '18927361', '18787170', '18487186', '18239126', '18239125']


After obtaining a list of references, details on each reference can be downloaded and parsed:

.. code-block:: python

    from litter_getter import pubmed

    ids = [19008416, 18927361]
    fetch = pubmed.PubMedFetch(id_list=ids)
    refs = fetch.get_content()

    refs[0].keys()
    >>> ['xml', 'doi', 'title', 'abstract', 'citation', 'authors_short', 'year', 'PMID', 'authors_list']

The complete XML returned from Pubmed is captured in the ``xml`` field.

HERO
----

If reference IDs are known, references can be collected and parsed from the EPA HERO database:

.. code-block:: python

    from litter_getter import hero

    ids = [1221, 1223]
    fetch = hero.HEROFetch(id_list=ids)
    results = fetch.get_content()

    results.keys()
    >>> ['failure', 'success']

    results.failures()
    >>> [12413113]

    >>> results['success'][0].keys()
    >>> ['source', 'json', 'title', 'authors_short', 'HEROID', 'abstract', 'year', 'PMID', 'authors_list']

The complete JSON returned from HERo is captured in the ``json`` field.

RIS
---

RIS exports from common reference management software such as `Endnote`_
or `Reference Manager`_ can be parsed via:

.. code-block:: python

    from litter_getter import ris
    import os

    fn = os.path.expanduser("~/Desktop/sample_ris.txt")
    importer = ris.RisImporter(fn)
    refs = importer.references

    refs[0].keys()
    >>> ['doi', 'title', 'reference_type', 'abstract', 'citation', 'accession_number',
         'json', 'authors_short', 'year', 'accession_db', 'PMID', 'id']


.. _`Endnote`: http://endnote.com/
.. _`Reference Manager`: http://referencemanager.com/
