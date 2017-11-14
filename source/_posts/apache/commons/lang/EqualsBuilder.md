---
title: Apache Commons Lang - EqualsBuilder
date: 2017-11-14 15:24:20
categories: "Java"
tags:
    - Java
    - Apache
keywords: EqualsBuilder, Apache Commons Lang, Java
---

代码片段来自 [dddsample-core](https://github.com/citerus/dddsample-core)

```
@Override
public boolean sameEventAs(final HandlingEvent other) {
    return other != null && new EqualsBuilder().
            append(this.cargo, other.cargo).
            append(this.voyage, other.voyage).
            append(this.completionTime, other.completionTime).
            append(this.location, other.location).
            append(this.type, other.type).
            isEquals();
}
```
