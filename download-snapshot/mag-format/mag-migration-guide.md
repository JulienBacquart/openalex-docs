---
description: >-
  Overview of how to use OpenAlex's MAG-format data snapshot if you were a
  previous user of the Microsoft Academic Graph data dump.  For new users, we
  recommend the Standard format instead.
---

# MAG migration guide

[From the beginning,](https://blog.ourresearch.org/were-building-a-replacement-for-microsoft-academic-graph/) we've worked to ensure that [MAG](https://aka.ms/msracad) users could easily migrate to OpenAlex data when [MAG goes away at the end of 2021.](https://www.microsoft.com/en-us/research/project/academic/articles/microsoft-academic-to-expand-horizons-with-community-driven-approach/) If you're looking at making this switch, you're in the right place! This page describes what you'll need to do to switch, as well as detailing the differences you can expect between the two datasets. (If you just want to download the data, see the [How To Download](https://docs.openalex.org/download-snapshot/mag-format/how-to-download) page.)

## **1. Formatting changes (breaking!)**

For the most part, the OpenAlex data dump is formatted just like the MAG one, for compatibility purposes. However, there are a few important changes. You'll probably need to modify your MAG ingest script to accommodate these:

* **Header row:** Tables now have a header row, which provides column names.
* **New columns:** There are a number of new columns with shiny cool new data (these are documented in part below, and more fully in the [schema docs](https://docs.openalex.org/download-snapshot/mag-format/mag-format-schema).

## **2. Data changes️**

There are four main relationships between the data/features of OpenAlex and MAG: [Unsupported](https://docs.openalex.org/download-snapshot/mag-format/mag-migration-guide#2.1-unsupported), [Frozen](https://docs.openalex.org/download-snapshot/mag-format/mag-migration-guide#2.2-frozen)[,](https://openalex.org/mag-migration-guide#data-changes-archival) [Ongoing](https://docs.openalex.org/download-snapshot/mag-format/mag-migration-guide#2.3-ongoing), and [New](https://docs.openalex.org/download-snapshot/mag-format/mag-migration-guide#2.4-new).&#x20;

Below, we dig in to the different data types:

### **2.1 Unsupported ❌**

These are MAG data types and functionality that are _not included_ in OpenAlex.

* **Patents:** MAG included data for patents; OpenAlex doesn't.
* **MAKES:** MAG included code to help you create a private, locally-hosted REST API. This was called [MAKES,](https://docs.microsoft.com/en-us/academic-services/knowledge-exploration-service/?view=makes-3.0) and OpenAlex doesn't support it. Instead, we support a single [centrally-hosted REST API](https://docs.openalex.org/api) that anyone can use, without having to host it or get the code running. Of course, if you want to create your own local API to serve the dataset you can still do that.

### **2.2 Frozen ❄️**

This data is included in OpenAlex but is _no longer be updated._

This frozen data will remain useful for many purposes, including backwards compatibility and historical research — but it's important to remember it'll become increasingly stale, and increasingly unsuitable for many use cases.

Please see the [schema docs](https://docs.openalex.org/download-snapshot/mag-format/mag-format-schema) to get the detailed, column-level documentation for which data will be frozen. But here's a high-level overview:

* **Rank:** MAG computed a "rank" property for lots of things. We are no longer updating this rank.
* **Family:** MAG's concept of "family" was used to group a preprint with its associated paper. Instead, OpenAlex treats a preprint and associated paper as the _same work,_ with two different _versions._ That version relationship is enumerated in the new [PaperUrls.version](https://openalex.org/schema#PaperUrls\_Version) column.
* **GRID IDs:** GRID has now been [officially replaced](https://www.digital-science.com/grid-passes-the-torch-to-ror-faqs/) by [ROR,](https://ror.org/) and we'll stop updating GRID IDs, and use ROR IDs instead. Since there's a simple 1-to-1 mapping between them, it's easy to [transition from GRID to ROR.](https://ror.readme.io/docs/gridror-transition-faq)
* **Conferences:** In an early announcement, we said OpenAlex might not support conference papers. This turns out to be only partly true. We actually will index the vast majority of conference _papers._ However, we won't do a good job of indexing the _conference instances_ and _conference series_ that publish the papers.&#x20;
* **Random webpage-hosted papers:** MAG indexed papers informally hosted on faculty and personal homepages; we won't be adding more of these.
* **Paper resources:** This table, which gathered code and data associated with a given papers, is no longer being updated
* **Paper citation contexts:** This table, indexed the text around each citation, is no longer being updated.

### **2.3 Ongoing 👍**

This data is included in OpenAlex, updated via OpenAlex's algorithms and data sources.

For many MAG use cases, you'll be able to ignore this transition, as the data will be compatible. However, it's important to note that our methods of data collection and processing differ from MAG's. So for many analytical and research cases, it'll be important to note the discontinuity on January 3 2022, when MAG's update processes were replaced by ours. OpenAlex is _compatible_ with MAG, but it is absolutely not _identical_ to MAG. In some ways our coverage will be better and more accurate; in others, not so much.

Because we get a lot of questions about a few specific MAG data types, we're listing them below to clear up any confusion — but keep in mind this list is not comprehensive:

* **Updates monthly:** Like MAG, we continue to release a [MAG-format data dump](https://docs.openalex.org/download-snapshot/mag-format/how-to-download), updated with all the latest data, about once a month.
* **Author name disambiguation (AND):** Like MAG, OpenAlex uses document properties (title, topic, journal, etc) to identify and assign unique author IDs, even when authors share a name — so you'll see one ID for the biologist Jane Smith, and a different one for the Jane Smith who's an art historian. Also like MAG, we identify two name strings as referring to the same person even if they are written differently ("J smith" and "Jane Smith").
* **Work clustering:** Like MAG, we infer that two papers are the same, even when they don't have [PIDs.](https://en.wikipedia.org/wiki/Persistent\_identifier) We use fingerprint-based fuzzy matching that's robust again typos, and can identify different versions of the same paper (preprint and version of record, for example).
* **Institution identification:** Like MAG, we identify authors' institutions, even there are no [PIDs](https://en.wikipedia.org/wiki/Persistent\_identifier) or other structured metadata to use.
* **Topic (field of study) tagging:** Like MAG, we automatically tag works with their field of study (variously described as "concepts," "topics," and "fields of study" in the MAG documentation). We're using MAG's same automatically-generated controlled taxonomy, although we've trimmed it down a bit: we only assign topics that are apply to more than 500 individual works. This means we support fewer topics as MAG did. We have added a field that specifies the topic assignment algorithm used for a given topic assignment (MAG's algorithm or the new OpenAlex algorithm) to help when comparing topics over time.

### **2.4 New 🔥**

These data types and functions are included in OpenAlex, but were _not in Microsoft's dataset_. They're updated using OpenAlex's algorithms and data sources.

* **Open Access (OA) status:** By integrating the industry-standard [Unpaywall](https://unpaywall.org/) dataset, OpenAlex can tell you whether a given paper is OA or not, name its copyright license (eg CC-BY), and give you the URL where to find it.
* **Full ISSN support:** OpenAlex provides a comprehensive list of ISSNs associated with each journal, including the [ISSN-L](https://www.issn.org/understanding-the-issn/assignment-rules/the-issn-l-for-publications-on-multiple-media/), the standard for for deduplicating journals.
* **ORCID:** In addition to disambiguating authors, OpenAlex also matches authors to their [ORCID](https://orcid.org/) IDs.
* **ROR:** We report [ROR](https://ror.org/) IDs, the new standard which has replaced the old GRID system. Since there's a simple 1-to-1 mapping between them, it's easy to [transition from GRID to ROR.](https://ror.readme.io/docs/gridror-transition-faq)
* **REST API:** Because not everyone has the time and money to manage a 500GB dataset, we're launching a [free REST AP](https://docs.openalex.org/api)I that will support easy, expressive access to the dataset without the need for much technical knowledge.

## **3. Coverage comparison**

Because OpenAlex will use a different set of sources and algorithms from MAG, our coverage and accuracy will be different. We'd like to compare the two in greater detail here — for example, to compare the systems based on number and type of new articles, relative percentage of new papers which include institutional affiliation information, precision/recall of author clustering, and so forth.

We're making progress on these comparisons now, and our internal data looks good. OpenAlex will offer similar accuracy and coverage to MAG (better in some ways, and worse in others). However, the comparisons are in flux since OpenAlex is still under construction, and so we don't currently have any data to share.

Please [subscribe to our mailing list](http://eepurl.com/hA8PhL) to get updates!

\
