---
layout: post
title: Dark Mode
tags: [Katex, Mermaid, Markdown]
categories: Demo
---

Let's demo some code snippet, with some mermaid diagrams.
Because if you put some code in your blog, you would at least make it:
1. Searchable
2. Good-looking

## Search on code

Search should be working even for complicated escape symbols.

```bash
sed -i 's/\"hostname\"\:.*$/\"hostname\"\: \"'$IPADDR'\"\,/g' open-falcon/agent/config/cfg.json
```

Or try searching for partial of a command, like this article should be returned when looking for "find grep"

```bash
find /etc -type f -exec cat '{}' \; | tr -c '.[:digit:]' '\n' | grep '^[^.][^.]*\.[^.][^.]*\.[^.][^.]*\.[^.][^.]*$'
```

## Code highlighting examples

Because you might put code in your blog post, and you want to make sure it will look good in here. Plus that the search
function will still be working!

### XML

Example from [W3C]
```xml
<part number="1976">
  <name>Windscreen Wiper</name>
  <description>The Windscreen wiper
    automatically removes rain
    from your windscreen, if it
    should happen to splash there.
    It has a rubber <ref part="1977">blade</ref>
    which can be ordered separately
    if you need to replace it.
  </description>
</part>
```

### Java

java example

```java
import java.util.*;

@Example
public class Demo {
  private static final String CONSTANT = "String";
  private Object o;
  /**
   * Creates a new demo.
   * @param o The object to demonstrate.
   */
  public Demo(Object o) {
    this.o = o !== null ? o : new Object();
    String s = CONSTANT + "Other example of text";
    int i = 123 - 33 % 11;
  }
  public static void main(String[] args) {
    Demo demo = new Demo();
    System.out.println(demo.o.toString())
  }
}
```

### Javascript

```javascript
/**
 * Does a thing
 */
function helloWorld(param1, param2) {
    const example = `hello ${param1}`
    var something = {
        key: "value",
        number: 1
    };

    // Do something
    if (2.0 % 2 == something) {
        console.log('Hello, world!');
    } else {
        return null;
    }

    // TODO comment
}
```

### JSON

```json
{
  "animals": {
    "tiger": {
      "name": "tiger",
      "images": ["🐯", "🐅", "⻁"]
    },
    "turtle": {
      "age": 126,
      "image": "🐢"
    },
    "unicorn": {
      "doesExist": true,
      "image": "🦄"
    }
  }
}
```

### Python

```python
import os


def some_function(param_one="", param_two=0):
    r'''A docstring'''
    if param_one > param_two:  # interesting
        print("Greater")
    return (param_two - param_one + 1 + 0b10) or None


class SomeClass:
    """ dunno what I am doing """

    def __init__(self):
        pass
```

### YAML

You can also render some yaml, like this `_config.yml`:

```yml
