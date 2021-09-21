---
layout: post
title:  "Paper Notes: Abstract Meaning Representation for Sembanking"
date:   2021-09-19 22:20:00 +0530
categories: papers
tags: 
---
## Paper Details
[PDF Link](https://amr.isi.edu/a.pdf)
[AMR Specification](https://www.isi.edu/~ulf/amr/help/amr-guidelines.pdf)

Authors
- Laura Banarescu
- Claire Bonial
- Shu Cai
- Madalina Georgescu
- Kira Griffitt
- Ulf Hermjakob
- Kevin Knight
- Philipp Koehn
- Martha Palmer
- Nathan Schneider

Year of publication: 2013

## Notes

>
Researchers have exploited manually-built treebanks to build statistical parsers that improve in accuracy every year. This success is due in part to the fact that we have a single, whole-sentence parsing task, rather than separate tasks and evaluations for base noun identification, prepositional phrase attachment, trace recovery, verb-argument dependencies, etc. Those smaller tasks are naturally
solved as a by-product of whole-sentence parsing, and in fact, solved better than when approached in isolation. ...  
semantic annotation today is balkanized. We have separate annotations for named entities, co-reference, semantic relations, discourse connectives, temporal entities, etc. Each annotation has its own associated evaluation, and training data is split across many resources.

Perhaps this approach needs to be broadened even further to look for dependencies across sentences too at the time of parsing itself. It seems likely that context appearing in prior sentences affects the parsing of subsequent ones.

>
Basic principles of AMR (Abstract Meaning Representation):
1. AMRs are rooted, labeled graphs that are easy for people to read, and easy for programs to traverse.
2. AMR aims to abstract away from syntactic idiosyncrasies. We attempt to assign the same AMR to sentences that have the same basic
meaning. For example, the sentences “he described her as a genius”, “his description of her: genius”, and “she was a genius, according to his description” are all assigned the same AMR.
3. AMR makes extensive use of PropBank framesets. For example, we represent a phrase like “bond investor” using the frame “invest-01”, even though no verbs appear in the phrase.
4. AMR is agnostic about how we might want to derive meanings from strings, or vice-versa. In translating sentences to AMR, we do not
dictate a particular sequence of rule applications or provide alignments that reflect such rule sequences. This makes sembanking very
fast, and it allows researchers to explore their own ideas about how strings are related to meanings.
5. AMR is heavily biased towards English. It is not an Interlingua

Point 4 is not clear.

>
In neo-Davidsonian fashion, we introduce variables (or graph nodes) for entities, events, properties, and states. Leaves are labeled
with concepts, so that `(b / boy)` refers to an instance (called b) of the concept boy. Relations link entities, so that `(d / die-01 :location (p / park))` means there was a death (d) in the park (p). When an entity plays multiple roles in a sentence, we employ
re-entrancy in graph notation (nodes with multiple parents) or variable re-use in PENMAN notation.
AMR concepts are either English words (“boy”), PropBank framesets (“want-01”), or special keywords. Keywords include special entity types
(“date-entity”, “world-region”, etc.), quantities (“monetary-quantity”, “distance-quantity”, etc.), and logical conjunctions (“and”, etc).
AMR uses approximately 100 relations:
- Frame arguments, following PropBank conventions. :arg0, :arg1, ... :arg5.
- General semantic relations. :accompanier, :age, :beneficiary, :cause, :comparedto, :concession, :condition, :consist-of, :degree, :destination, :direction, :domain, :duration, :employed-by, :example, :extent, :frequency, :instrument, :li, :location, :manner,
:medium, :mod, :mode, :name, :part, :path, :polarity, :poss, :purpose, :source, :subevent, :subset, :time, :topic, :value.
- Relations for quantities. :quant, :unit, :scale.
- Relations for date-entities. :day, :month, :year, :weekday, :time, :timezone, :quarter, :dayperiod, :season, :year2, :decade, :century,
:calendar, :era.
- Relations for lists. :op1, :op2, ... :op10

For the sentence, `The boy wants to go`
>
LOGIC format:
∃ w, b, g:
instance(w, want-01) ∧ instance(g, go-01) ∧ instance(b, boy) ∧ arg0(w, b) ∧ arg1(w, g) ∧ arg0(g, b)

It seems that `want-01` has two arguments and `go-01` has one. The boy `b` is an argument for both `w` and `g`. `g` is itself an argument for `w`. This Logic (1st order logic?) format seems difficult to use. Indeed the paper mentions that the Graph format is used by the machine. This is probably shown just for completeness.

>
AMR format (based on PENMAN):
```
(w / want-01
  :arg0 (b / boy)
  :arg1 (g / go-01
          :arg0 b))
```

While this is supposed to be human-readable, it seems difficult to get right. The authors have created an [editor](https://amr.isi.edu/editor.html) to create these representations, but they say it takes about 10 minutes to put one sentence into this format.

> GRAPH format:
> <div class="mermaid">
>   graph TD
>   w-->|instance|want-01
>   w-->|Arg0|b-->|instance|boy
>   w-->|Arg1|g-->|Arg0|b
>   g-->|instance|go-01
> </div>

It seems slightly weird that there are extra edges labeled instance. Not sure what purpose these serve.

>
AMR also includes the inverses of all these relations, e.g., :arg0-of, :location-of, and :quant-of. In addition, every relation has an associated reification, which is what we use when we want to modify the relation itself. For example, the reification of :location is the concept “be-located-at-91”

>
AMR abstracts away from co-reference gadgets like pronouns, zero-pronouns, reflexives, control structures, etc. Instead we re-use AMR variables, as with “g” above. AMR annotates sentences independent of context, so if a pronoun has no antecedent in the sentence, its nominative form is used, e.g., “(h / he)”

There are many examples in the paper and more in the [specification](https://www.isi.edu/~ulf/amr/help/amr-guidelines.pdf)
These need to be studied closely.

## Referenced Work to be studied

PropBank framesets
- [ ] Kingsbury and Palmer, 2002
- [ ] Palmer et al., 2005

AMR is equivalent to
- [ ] Feature structures, (Shieber et al., 1986)
- [ ] Conjunctions of logical triples, directed graphs, and PENMAN inputs (Matthiessen and Bateman, 1991)

AMR draws on
- [ ] Davidson, 1969
