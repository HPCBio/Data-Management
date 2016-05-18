# Data Management and Access

## Objectives

Students will be able to:

- articulate a definition and rationale for data management
- understand and use common database formats and other data formats for
  bioinformatics and omics
- utilize Git to commit and manage data sets and changes thereto
- utilize online repository services (such as GitHub, RDS, and Dryad) to make
  data publicly available
- make data citable via DOI

## Topics

A disclaimer: my background is nuclear engineering and high-performance
computing. So I had a strong feeling that "I have no idea what I'm doing" when I
started working on this project. But fortunately, practices of good data
management carry over significantly between fields.


## Motivation for database and archival

When you process data, it is a trivial truth that you want access to the
following forms of your data:

1. the raw data, either as gathered from the initial instrumentation or
simulation or immediately after filtering for "good" values, whatever that may
mean;
2. the data in the process of analysis *or* the process of analysis itself; and
3. the final results of the analysis (if nontrivial).

Numbers one and three are fairly straightforward — you simply archive a copy of
the data fresh from the source and then deposit a copy of the final results.
Number two can be a bit trickier. This is the stage where you often see data
overwritten as one goes down one false start after another (which is fine and
part of the process of science). But sometimes you actually *did* go down the
right path and then went back and now you've lost that road... shoot. With a lab
book, backing up can be straightforward, but with digital data a history of your
data process is more elusive.

We'll mention two levels of granularity in data processing:

1. process-level data changes (which corresponds to recovering your history in
R);
2. system-level data changes (which corresponds to annotating and storing
specific versions of your files).

The first is readily accessible to you. Not all data operations are reversible,
but you ought to be starting from a point where you can repeat the recent set of
steps to arrive again where you are from the initial input. In this case, your
process is a good candidate for a *script*: you simply extract the relevant
steps from the history in R and use the script to process your data in
reproducible ways. The second options corresponds to using a version-control
system such as Git or Mercurial, which we'll explore in a bit.

We hope to establish in this workshop a practical way of managing data sets that
has an auditable provenance: that is, changes are as reversible as possible,
allowing you to both recover from a previous mistake as well as revert to a
specific time in the past (such as when you submitted a paper for review).


## Practical data set management

What we'd like to see ideally is a world where datasets are open to interested
scientists; have an auditable provenance for their collection, processing, and
publication; and are hosted in friendly formats with sufficient metadata.

(I say "open to interested scientists" because, although I support open science,
I recognize that there are a number of situations where data need to be at least
embargoed, particularly where patents are pursuable.)

Technical prerequisites that will enable this ideal world include:

1. accessible platforms for hosting data;
2. tools for tracking changes and tagging key versions and changes to the data
set; and
3. suitable formats (where "suitable" will be defined below).

(Obviously the culture *around* these things will have to change, and is in the
process of so doing. What I mean by this is that we have to value an openness in
science that hasn't historically been valued because of technological barriers
and fears about getting "scooped"—the latter, if it ever happens, is extremely
rare.)

Now that we have a numbered list, naturally I'll step through it in reverse
order.


### Data formats

In 1914, Ernest Shackleton, one of the last great explorers, commissioned the
Imperial Trans-Antarctic Expedition, a magnificent attempt to cross Antarctica
only three years after Amundsen had won the race to the South Pole. Shackleton
assembled a crew and, despite the outbreak of World War I only five days
earlier, First Lord of the Admiralty Winston Churchill sent Shackleton a
one-word telegram: "Proceed.". The *HMS Endurance* set out on the expedition on
August 8, 1914, with 28 men, 70 dogs, and a stowaway for a two-year adventure.

En route to the coast, however, the *Endurance* encountered the pack ice of the
Weddell Sea and became finally entrapped in the ice in January 1915. The men
used the ship as a fixed base and encamped there as a winter station. But when
the ice began to move again in the September spring, the ship began to leak
under immense pressure and finally sank in November 1915. The men portaged
lifeboats across the ice floes and floated closer to an island with cached
supplies by March 1916. Then in April, their ice floe broke in two, Shackleton
ordered all men to the lifeboats, and they made their way over five harrowing
days to the uninhabited Elephant Island. Other adventures by sea and land
followed, but I'll leave off at this juncture. All of the men of the *HMS
Endurance* survived their ordeal due to Shackleton's extreme resourcefulness in
the face of disaster.

The point of this remarkable history is this: these men had with them a
photographer, Frank Hurley. Recently, the Royal Geographical Society pulled out
the original glass negatives, about a century old, and reprinted the developed
images<sup>[[Hurley](http://catalogue.nla.gov.au/Search/Home?lookfor=hurley+shackleton&type=all&limit%5B%5D=&submit=Find&page=5)]</sup>.
These were glass negatives exposed using early twentieth-century technology,
carried under extreme stress across ice floes in sub-zero temperatures, sailed
across the sea, and then dropped into a box for a century. Now *that's* a robust
format.

Do you seriously expect that your JPEGs will be as accessible to your
grandchildren as your grandparents' photos are to you? I don't have any idea,
and you don't either. Most of our data won't be historically significant in such
a dramatic way, but it is clear that longevity of our data is still a priority
for our research and science. We will here posit a *principle of data access*:

<blockquote>
A suitable data format is readable by most people in most places with most
equipment (embracing domain-specific software and hardware) at most times in the
future—and don't be stingy with *most*.
</blockquote>

As anyone who has needed to read a 3½" floppy disk in the past five years can
attest, formats change. The CD has persisted, but eventually it too will
disappear; in any case, a CD only lasts somewhere between 18 months and 20 years
under normal storage conditions, a wide and unreliable range. Archival-grade
disks [exist](http://jvc-media.com/archives/passedtest.html) but are rare and
difficult to acquire. Few people are using them to store data, that's for sure.
And even if they work, you can't distribute the data trivially.

Okay, that's old news. The hardware you store your data on shouldn't be *only*
physical, master's theses aside: you need to get your data onto a server or
(better) into the cloud where it can be moved from disk to disk as hardware ages
and is replaced and where it is accessible to people. That's the major point
here, actually: you should put your data in a redundant accessible location such
as a data-hosting service.


#### Text *v.* binary

Of course, the *physical* format is one thing—what about the software encoding?
By which I mean, how your data are actually stored on the disk affects whether
or not a file is suitable for long-term usability. You are aware that most
programs offer different ways of saving data—a trivial example is Word, which as
of Word for Mac 2011 offered 15 different formats for saving a simple document.
Why are all of these different? Well, for starters, some are very simple
text-based formats, which just store the words and maybe some minimal
information about bold, italic, or colored text. Others are the Cadillacs of
documents, including animations, embedded videos, word art, and hyperlinks. This
latter are generally binary formats, meaning that instead of storing their data
as neat little bits of text everything is converted into compressed binary
strings of bits and written to the drive. Let's examine some consequences of
this.

WordPerfect and WordStar lie not outside the memory of some in this audience.

We have a file called `microarray.csv`
here<sup>[[Horton2004](http://www.cybertory.org/downloads/bae/)]</sup>. This
file contains experimental microarray measurements on a number of human viruses,
described in the author's commentary thus: <blockquote> This microarray contains
approximately 12,000 spots, each containing a small sequence of a conserved
region from a known virus. By hybridizing a sample of an unknown virus to this
array of segments from known viruses, we hope to be able to identify which known
virus the sample most closely resembles. </blockquote>

First, we're going to open the file and examine its contents. Next, we'll save a
copy in XLS format and look at the raw data. Then we'll add lines indicating the
average and standard deviation of each measurement and look at both the raw data
again and then at the `diff`s of the files.


#### Data *v.* process

The data set used a moment ago originally came from a master's
thesis<sup>[[Horton2004](#Horton2004)]</sup> illustrating the use of machine
learning algorithms like naïve Bayes classifiers, but these macros are now
unusable in a modern version of Excel. The data are available to a modern user
only a decade later, but the process and results of the calculations are not.

There is a corollary underlying principle, which we'll call the principle of
segregation: you should keep your *data* and your *analysis process* separate.
There is a temptation to include the data sets and the Excel macros as the same
process, but for archival purposes you don't want to keep both together.

Why not? In many cases, you run the risk of losing control of your data
processing over the long run if you don't keep both components orthogonal. For
instance, with the 2008 release of Microsoft Office for Mac, support for Visual
Basic (the macro language in Excel) was dropped due to difficulties with the
transition from PowerPC and Intel. In 2011, Microsoft restored this
functionality, but that's a shaky leg to stand on.

Another process that can occur is that platforms shift and subtly alter
functionality. For instance, many people use OpenOffice instead of Microsoft
Office; this is fine for data but breaks macros written in Microsoft's
proprietary Visual Basic language.

I am not suggesting that you shouldn't be using Excel to process your data—far
from it. I am simply arguing that you need to be aware of how your data set's
format will make it more or less accessible down the road, and how that encoding
affects issues like transparency of changes and ease of processing and analysis.
Thus, to the extent possible, text-based formats should be used to ensure that
the raw data of revisions remain trivially intelligible. (This is clearly not
possible with graphics, for instance.)

In software development, there is a rule-of-thumb which states that you should
keep your code separate from your code's configuration and
input<sup>[[Davison2014](#Davison2014)]</sup>. That is, if you hard-code in
numbers like grid size or number of time steps, you make your code more
difficult to use. If you instead set the program or script to use a command-line
flag or read a configuration file to set the useful parameters and load data,
that makes the script itself more useful. What I'm hinting at here is a similar
idea for data: the data set itself should be independent of the process, and
although you may occasionally be tempted that it's useful to have the analysis
sitting right there, it's generally far better in the long run to just keep
things orthogonal.

What constitutes data and what constitutes process? Things can get a little
fuzzy when you start to include things like calculating the mean or standard
deviation—which are they?

-   Keep data and analysis in separate folders.
-   Keep raw data and analysis results in separate folders.

Now what should you do if part of your analysis process is manual—if it can't be
scripted? Are there examples of this in bioinformatics?


#### Case studies

So let's take a look at a few case studies and critique what is right and what
is wrong according to this paradigm.

-   [Atmar1997](https://www.jstor.org/stable/4220548)
    [review:Kelt1997](https://www.jstor.org/stable/20168111) (where are the data
    files? the initial link in other citations is dead, even, and there's no
    [archive.org](https://web.archive.org/web/19970701232759/http://www.bvis.uic.edu/museum/science/Science.html)
    version that works; the main link is a Gopher protocol, which predates the
    Web and is not supported for access by any mainstream browser anymore;
    finally I found [a modern
    site](http://www.fieldmuseum.org/nestedness-temperature-calculator-program)
    but all of the links are still dead. This is a worst-case scenario.)

-   [Baden2014](http://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.1001972)
    (provides raw data in original format, also in raw TXT and XLS; but DOCX for
    no good reason)

-   [Begun2007](http://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.0050310)
    (uses TXT well but also XLS when no reason)

-   [Cantlon2007](http://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.0050328#s5)
    (EPS format +/- but not bad for graphics; also great title)

-   [Martinez-Bakker2015](http://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.1002172)
    (uses CSV! perfection!)

-   [Tsuriel2006](http://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.0040271)
    (reference to VBA algorithm but no code and no data)

-   [Wang2003](http://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.0000002)
    (good example of data sets, but in DOC format)


### Version control (Git)

Version control is an increasingly inappropriate name for what you could also
just call controlled change tracking. Imagine doing all of your work in an
efficient Dropbox that you share with your friends, that has a read-only version
which faces the world but only with the bits and pieces you tell it to, and that
lets you write yourself a short note justifying every change in case you went
down the wrong garden path. A version control repository gives you peace of
mind, like a shared backup server that lets you recover the thought process that
originally led you to the changes you made.

The specific program we'll use to illustrate the advantages is Git. Git is a
tool for storing the changes to a body of program code over time, along with
annotations to clarify why design decisions or bug corrections were made: thus
Dropbox with a message tag. It really was developed to work with the code of the
Linux kernel and spread by its merits to much of the contemporary software
development community. So far it seems like everything I've said about Git could
be really talking to coders, not to data creators or data analysts, but what I
want you to latch onto is *the idea of treating data and data analysis as
ongoing codelike processes* that we can track the same way as code. (And that's
aside from the innumerable benefits of just knowing *which* version of a
collaborator's R script you should be running on the data set!)


#### Conceptual model

We'll divide the world into three zones: a working area, a staging area, and a
repository or archive. Each of these serves its own rôle and communicates data
conceptually (although not physically) to the others.

We start with a simple file. We'll say our repository contains the Viking
scientist Hrothgar's studies on Old Norse fauna: frost giant, valkyrie, troll,
kobold, wurm.

    >NP_852610.1 microtubule-associated proteins [frost giant]
    MKMRFFSSPCGKAAVDPADRCKEVQQIRDQHPSKIPVIIERYKGEKQLPVLDKTKFLVPDHVNMSELVKI
    IRRRLQLNPTQAFFLLVNQHSMVSVSTPIADIYEQEKDEDGFLYMVYASQETFGFIRENE


#### Creating a repository


#### Recording changes to files: add, commit, ...

Now let us add data to this file—let's say another observation, and we can argue
about the appropriateness in bioinformatics of appending varied sequence data
this way later.

    >NP_85262345.1 microtubule-associated proteins [valkyrie]
    ADQLTEEQIAEFKEAFSLFDKDGDGTITTKELGTVMRSLGQNPTEAELQDMINEVDADGNGTID
    FPEFLTMMARKMKDTDSEEEIREAFRVFDKDGNGYISAAELRHVMTNLGEKLTDEEVDEMIREA
    DIDGDGQVNYEEFVQMMTAK*

    >NP_45983039.2 cytochrome b [troll]
    LCLYTHIGRNIYYGSYLYSETWNTGIMLLLITMATAFMGYVLPWGQMSFWGATVITNLFSAIPYIGTNLV
    EWIWGGFSVDKATLNRFFAFHFILPFTMVALAGVHLTFLHETGSNNPLGLTSDSDKIPFHPYYTIKDFLG
    IENY*

    >NP_54896930.4 cytochrome b [kobold]
    VTEVRGMKGAPDAILSRAIEIEEENKRLLEGMEMIFGQVIPGAKETEPYPVWSGLPSLQTKDED
    ARYSAFYNLLHCLRRDSSKIDTYLKLLNCRIIYNNNC*


#### Viewing changes: status, diff, ...


#### Ignoring files


#### Working on the web: clone, pull, push, ...


#### Resolving conflicts


### Publishing data online (GitHub, Dryad, RDS)

#### Creating a repository on GitHub

Time was when you wanted to host a new open-source project online, you went to
[SourceForge](http://sourceforge.net/) or the now-defunct Google Code project.
These were mainly aimed at software developers and were built around principles
of access and collaboration. Eventually, however, the upstart
[GitHub](http://www.github.com/) ate everyone else's lunch and became the
dominant hosting platform, which is why we'll use it as our primary example
today. (GitHub is separate from Git, and not developed or sponsored by the same
people.)

To a first approximation, GitHub simply acts as a remote repository that you can
synchronize to your local copy. This gives you both redundancy in the case of
losing your local file system as well as a mutually accessible URL for
collaborators or users to share with you. As with Git, the benefits of managing
software development with version control and a repository system readily extend
to those who generate and analyze data and who need to document the data set or
process itself.


#### Collaborating with others on GitHub

Collaboration generally proceeds along two lines: either the fork-join model or
the sharing/branching model.


#### GitHub Auxiliary Features

There are a lot of auxiliary features as well on GitHub that make it attractive
to anyone developing anything: [automatic project
pages](https://pages.github.com/),
[wikis](https://help.github.com/articles/about-github-wikis/), and [issue
tracking](https://github.com/blog/411-github-issue-tracker) (this last is common
to many platforms but underutilized).

-   **Automatic Project Pages**. GitHub will automatically generate a home page
    for your project using templates. This has a number of design patterns and
    internal links that makes the page more usable.

-   **Issue tracking**. If you are familiar with bug tracking systems, this is
    no big deal. If you're *not*, then think of this as a perpetual to-do list
    and task tracker, useful not only for bugs but for feature requests, plans,
    wishlist items, real-world tasks, etc.
    
    [Rails example page](https://github.com/rails/rails/issues)

-   **Project Wikis**. Something of an alternative to the project page, you can
    also set up a wiki as documentation. You compose this in
    [Markdown](https://daringfireball.net/projects/markdown/), a text-based
    format that's very readable and is converted internally by GitHub into HTML
    for rendering the page. (Lawrence Angrave of Computer Science has actually
    written/crowd-sourced an [entire
    textbook](https://github.com/angrave/SystemProgramming/wiki) using the
    GitHub wiki feature.)


#### Publishing data on Dryad

For years, there was no good way to make code, documentation, and data available
persistently. Although services such as the [Internet
Archive](http://www.archive.org/) have mitigated this somewhat, more systematic
and less *ad hoc* repositories are available. We will take Dryad as typical of
open-access data repositories, although there are others including a few
specific to molecular sequence data, for instance. You will probably be using
the INSDC databases like GenBank, ENA, DDBJ if you're interested in sequence
data as was discussed in the Bioconductor segment yesterday.

[Dryad](http://datadryad.org/) is a nonprofit, curated repository intended to
render the data underlying scientific publications discoverable, freely
reusable, and citable. Dryad also offers integrated data submission for a
growing list of journals.

Journal access is often brokered by institutions such as the university library,
which purchases block packages from publishers and archivists in order to
provide access for individual affiliated users. Those who do not have access via
an institution have to pay per article or are restricted to open-access journals
(incidentally, a huge incentive to publish in open-access journals). In
contrast, Dryad is an open data archive and any individual user can utilize
published data sets. Dryad recovers its costs by charging journals or depositors
a fee for depositing the data.

One of the *huge* advantages of Dryad over the older process of simply
submitting data to a journal is that any file format is acceptable, with a 10 GB
limit. In addition to Dryad, I mentioned
[GenBank](http://www.ncbi.nlm.nih.gov/genbank/), which you may all be familiar
with; this is a service of the NIH which collects all publicly available DNA
sequences.

There are still a lot of cases which a journal-oriented data service like Dryad
doesn't cover, though. For instance, a master's thesis' data may not all be
published in a journal source, and other technical projects pass the bar of
being technically useful but not sufficiently scientifically motivating to fit
well with certain repositories. The service [re3data](http://www.re3data.org/)
can help you search for persistent solutions to data archival in those cases as
well.


#### Overview of Research Data Service

The University of Illinois' [Research Data
Service](http://researchdataservice.illinois.edu/) has a collection of best
practices available on their web site, suggesting ways to organize your data and
links to major funding agency requirements for data management plans. I would
like to introduce data curator Elizabeth Wickes of RDS. She's going to talk
about their services and data management.


#### Citability

Of course, if we're going to all of the trouble of preserving our data online,
we want to make it as easy as possible for other researchers to cite the data
set. The typical way to do this is to use a DOI, which I'm certain all of you
have seen in citations previously. Normally these are issued by a journal
directly when you publish a paper, and you have to do no work in order to get
one. With data sets, once you submit to a repository like Dryad or the Research
Data Service, you are also assigned a persistent DOI to use in citation or on a
CV.


### Data usability

Thus far we've focused on format and access. I would be remiss in not reviewing
best practices in rendering your data *usable* to other researchers. The
principles I'll outline now come from [[White2013](#White2013)]. While we've
seen many of these before in our discussion, this provides a good recap.

1.  Share your data.
2.  Provide metadata.
3.  Provide an unprocessed form of the data.
4.  Use standard data formats.
    
    ![](./img/xkcd-standards.png)
    
    1.  Use standard file formats.
        
        In HPC, this often means "don't make up a new file format when an old
        one will do", but it also means avoid proprietary formats for archival
        purposes.
    2.  Use standard table formats.
    3.  Use standard formats within cells.
    4.  A good resource for more information is [BioSharing.org](https://www.biosharing.org/).
5.  Use good null values.
6.  Make it easy to combine your data with other datasets.
7.  Perform basic quality control.
8.  Use an established repository.
9.  Use an established and open license.
    1. We haven't talked much about licenses, which of course tell others *how*
    they can use your work.


#### Common data types

| Format Name | Description |
|-------------|-------------|
| `RAW` | Sequence format without header. |
| `FASTA` | Sequence format with header line and sequence: `>name AGCTGTGTGGGTTGGTGGGTT` |
| `PIR` | Sequence format that's similar to FASTA but less common |
| `MSF` | Multiple sequence alignment format |
| `CLUSTAL` | Multiple sequence alignment format (works with T-Coffee) |
| `TXT` | Text format |
| `GIF`, `JPEG`, `PNG` | Graphic formats |
| `PDF`, `DOC` | Binary format |

(Table data adapted from [*Bioinformatics for
Dummies*](http://www.dummies.com/how-to/content/bioinformatics-data-formats.html).
Other formats are listed at
[Wikipedia](https://en.wikipedia.org/wiki/List_of_file_formats#Biology).)

-   Which of these are more appropriate in light of what we've discussed?  Why?


## References

-   <a id="Bioinformatics2015">Bioinformatics2015</a>:  [*Bioinformatics Activity Bank*](http://teachingbioinformatics.fandm.edu/).  2015.

-   <a id="Davison2014">Davison2014</a>:  Andrew P. Davison.  [Best practices for data management in neurophysiology](https://rrcns.readthedocs.org/en/latest/analysis.html).  2014.

-   <a id="Haklev2014">Haklev2014</a>:  Stian Haklev.  [Starting data analysis/wrangling with R:  Things I wish I'd been told](http://reganmian.net/blog/2014/10/14/starting-data-analysiswrangling-with-r-things-i-wish-id-been-told/).  2014.

-   <a id="Horton2004">Horton2004</a>:  Robert M. Horton.  [Bioinformatics Algorithms Demonstration](http://www.cybertory.org/downloads/bae/).  2004.

-   <a id="White2013">White2013</a>:  Ethan P. White, Elita Baldridge, Zachary T. Brym, Kenneth J. Locey, Daniel J. McGlinn, and Sarah R. Supp.  Nine simple ways to make it easier to (re)use your data.  *Ideas in Ecology and Evolution 6*(2): 1–10, 2013.  <a href="http://library.queensu.ca/ojs/index.php/IEE/article/view/4608">doi:10.4033/iee.2013.6b.6.f</a>

<!-- A.M.D.G. -->