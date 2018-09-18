---
title: Star UML
date: 2018-09-17 17:54:10
categories: "更多"
tags:
    - Star UML
keywords: Star UML
---

## Class Diagram

1. Model Explorer
2. Add Diagram => Class Diagram

### Interface Format

1. Click `Interface` in workspace
2. Editors => Styles => Format => Label, Icon and so on

### Add Parameter for Operation

1. Model Explorer
2. Find Operation => Add Parameter
3. Editors
    - name
    - type (Find and relate type already defined)
    - direction (in, input, out, return => in is default, return is return)

### Aggregation or Composition or Dependency or Association

- Aggregation: Has-a => animals and animal => animals TO animal
- Composition: Contains-a => animal and its head => animal TO its head
- Dependency: Animal dependency water and air => animal TO water and air
- Association: Animal associate with climate => animal TO climate

**Note** It seems that class A points to B (`A => B`) while A contains B (`A extends B`)


