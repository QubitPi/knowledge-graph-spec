# Overview

In
[knowledge representation and reasoning](https://en.wikipedia.org/wiki/Knowledge_representation_and_reasoning),
knowledge graph is a
[knowledge base](https://en.wikipedia.org/wiki/Knowledge_base) that uses a
graph-structured data model or topology to integrate data. Knowledge graphs are
often used to store interlinked descriptions of entities – objects, events,
situations or abstract concepts - while also encoding the semantics underlying
the used terminology.

For example, this knowledge graph represents

![Error loading example-conceptual-diagram.png](https://en.wikipedia.org/wiki/File:Conceptual_Diagram_-_Example.svg)

is represented using the following JSON:

```json example
{
  "nodes": [
    {
      "id": "living-things",
      "fields": {
        "name": "Living Things"
      }
    },
    {
      "id": "animals",
      "fields": {
        "name": "Animals"
      }
    },
    {
      "id": "plants",
      "fields": {
        "name": "Plants"
      }
    },
    {
      "id": "dogs",
      "fields": {
        "name": "Dogs"
      }
    },
    {
      "id": "cows",
      "fields": {
        "name": "Cows"
      }
    },
    {
      "id": "herb",
      "fields": {
        "name": "Herbs"
      }
    }
  ],
  "links": [
    {
      "source": "dogs",
      "target": "animals",
      "fields": {
        "name": "is"
      }
    },
    {
      "source": "animals",
      "target": "living-things",
      "fields": {
        "name": "is"
      }
    },
    {
      "source": "cows",
      "target": "animals",
      "fields": {
        "name": "is"
      }
    },
    {
      "source": "plants",
      "target": "living-things",
      "fields": {
        "name": "is"
      }
    },
    {
      "source": "herbs",
      "target": "plants",
      "fields": {
        "name": "is"
      }
    },
    {
      "source": "cows",
      "target": "herbs",
      "fields": {
        "name": "eats"
      }
    }
  ]
}
```
