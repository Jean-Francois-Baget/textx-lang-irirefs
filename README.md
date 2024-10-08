# textx-lang-irirefs

![logos](https://github.com/Jean-Francois-Baget/textx-lang-irirefs/blob/main/img/logos.jpg?raw=true)


**textx-lang-irirefs** is a python implementation of the IETF standards [RFC 3987 Internationalized Resource Identifiers](https://www.ietf.org/rfc/rfc3987.txt) and [RFC 3986  Uniform Resource Identifiers](https://www.ietf.org/rfc/rfc3986.txt), relying upon the meta-language [textX](https://github.com/textX/textX). Though many python libraries allow for the parsing of IRIRefs, it is a good alternative when you want to use those IRIRefs in your own textX grammar

The `textxirirefs` package provides a parser (basically a *textX* *metamodel*), able to build a *textx* *model* from a string representing an IRIRef. This model can be visualized, for educational purpose, but more importantly can be *normalized*, *recomposed*, or *resolved* against a base IRI.

**textx-lang-irirefs** has been created by Jean-François Baget at the [Boreal](https://team.inria.fr/boreal/) team ([Inria](https://www.inria.fr/fr) and [LIRMM](https://www.lirmm.fr/)). It is part of the [textx-lang-dlgpe](https://github.com/Jean-Francois-Baget/textx-lang-dlgpe) project.

### Walkthrough

The following code demonstrates, in python, how to build a `parser`, generate a `model` from a python string respecting the RFC 3987 or  RFC 3986 standards, resolve the model against a base IRI, normalize then recompose the result.


```python
from textx import metamodel_for_language

parser = metamodel_for_language('textxirirefs') # building the parser


base = 'http://www.lirmm.fr'
relative = '~baget'

basemodel = parser.model_from_str(base) # basemodel is an IRIRefContainer object
relativemodel = parser.model_from_str(relative) # as is relativemodel

resolved = basemodel.resolve(relativemodel) # and so is resolved
recomposed = resolved.recompose() # recomposed is now a string

test = recomposed == 'http://www.lirmm.fr/~baget' # test is true
```


## Installation

```
pip install textx-lang-irirefs
```

### Testing

You can test that everything behaves correctly (but first you have to clone the whole repository).

```
git clone https://github.com/Jean-Francois-Baget/textx-lang-irirefs.git
cd textx-lang-irirefs
python -m unittest
```

```
.....
----------------------------------------------------------------------
Ran 5 tests in 0.179s

OK
```

### irirefresolve

The installation also generates the `irirefresolve` command, that can be used to resolve a relative IRI against a base IRI without having to write any python code.

`irirefresolve --help`

```
usage: irirefresolve [-h] [-c] base relative

This command returns the resolution of a relative IRI against a base IRI, 
according to the algorithm standardized in [RFC 3986] URI Generic Syntax. 
When the --compatibility flag is set, an non strict resolution algorithm 
is applied according to prior specifications of partial URI [RFC1630]

positional arguments:
  base                 the IRI against which is resolved the relative
  relative             the relativeIRI to resolve against a base

options:
  -h, --help           show this help message and exit
  -c, --compatibility  non strict resolution when in compatibility mode

From textx-lang-irirefs (0.0dev7), (c)2023 Jean-François Baget, Inria.
```

For instance, `irirefresolve -c "http://www.w3.org/2001/XMLSchema#" "integer"` returns *http://www.w3.org/2001/integer*, an unexpected (but correct) behaviour when one is used to the turtle syntax.

## Usage

### Building the parser

The first thing to do is to build the Json parser. This can be done with the following code.

```python
from textx import metamodel_for_language

parser = metamodel_for_language('textxirirefs')
```

#### Visualizing the grammar

This parser can be used to obtain a graphical representation of the grammar [iri.tx](https://raw.githubusercontent.com/Jean-Francois-Baget/textx-lang-irirefs/refs/heads/main/src/textxirirefs/iri.tx). For more details on textx visualization, see https://textx.github.io/textX/visualization.html.

```python
from textx.export import metamodel_export

metamodel_export(parser, 'iri.dot')
```
This codes generates a file `iri.dot` that can be visualized with [Graphviz](https://graphviz.org/).

### Parsing an IRIRef

Most importantly, the parser can be used to generate a *model* from a python string encoding an IRIRef.


```python
full = "http://foo@www.lirmm.fr:8080/~baget/data#fragment?query=true"
base = "http://www.lirmm.fr/~baget#fragment"
relative = "data?query=true"

fullmodel = parser.model_from_str(full)
basemodel = parser.model_from_str(base)
relativemodel = parser.model_from_str(relative)
```

#### Visualizing the model

As for the parser, the model can be visualized.

```python
from textx.export import model_export

model_export(fullmodel, 'fullmodel.dot')
```
This file `fullmodel.dot` can also be visualized with [Graphviz](https://graphviz.org/).


![model](https://github.com/Jean-Francois-Baget/textx-lang-irirefs/blob/main/img/model.png?raw=true)

### Resolving the model of a Relative IRI against a base


```python
resolvedmodel = basemodel.resolve(relativemodel)

test = resolvedmodel.recompose() == 'http://www.lirmm.fr/data?query=true' # test is True
```

## TO DO

* implement correctly the `normalize()` method
* classify correctly the parsed IRIRefs


