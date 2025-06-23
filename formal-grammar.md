One of major inspirations behind the Zpär-25 linearization is RDF, and we can use RDF to formally define all of its operations. In addition to graph itself, it requires a *machine* to manage its state and some additional registers, including keeping the order of added RDF triples, which is used for backreference.

Note that while existence of RDF triple as an element of RDF graph called an *assertion* in RDF, Zpär-25 assertion is a different thing which is expressed through a specific triple.

The Zpär-25 fragments in the examples are to be interpreted as directly following one another in a single text, e.g., it goes from empty state to `adt` to `adt so`, etc.

### 1. Empty contentive phrase = vague referent
Referents can be expressed with blank nodes in RDF. There are no way in RDF to add a single node to the graph without mentioning any triples it participates in, but we can use a resource representing graph itself for this:

ε (empty text) →<br>
```
_:r0
_:r0 void:inDataset example:graph1 .
```

The referent node `_:r0` is set as *current referrent*.

### 2. Content word
Each content word adds a semantic tags to the current referrent. The verb relating the referrent and the tag is itself a referent node:

`adt` →<br>
```
_:r0 _:r1 zparTag:adt .
_:r1 a zpar:semanticTag .
_:r1 void:inDataset example:graph1 .
```

This allows to modify the referent/contentive relationship with `q`. Without it, though, node to which the tag is added (`_:r0` in this case) stays current.

### 3. Relational
Relational adds itself and its right referrent as new nodes to the graph, the current referrent is added as the left to the relational, and then the right one becomes the current:

`od` →<br>
```
_:r0 _:r2 _:r3 .
_:r2 a zparRelational:od .
_:r2 void:inDataset example:graph1 .
_:r3 void:inDataset example:graph1 .
```

`_:r3` is now current.

### 4. `l`-anaphora

Microcode particles are purely part of linearization and do not appear on the graph as such, but we can illustrate their working, too.

Using `l` takes all the tags added to the current referent node so far, both semantic and microcode (`a`, `e`...), find the most recent referent node mentioned which has all of them, and identifies (merges) the found node and the current one (which stays current).

`adt l` will search for the most recently added "feline" node, and fill find `_:r0`. It will then make the current referrent one and the same as the found one, merging their links together; in our example, we have to edit `_:r0 _:r2 _:r3 .` into `_:r0 _:r2 _:r0 .`, and also remove `_:r3 void:inDataset example:graph1 .` alltogether.
