---
title: "Base for related notes"
source: "https://www.reddit.com/r/ObsidianMD/comments/1ndiq2x/base_for_related_notes/"
author:
  - "[[kepano]]"
published: 2025-09-10
created: 2025-09-23
description:
tags:
  - "clippings"
---
One of my favorite use cases for Obsidian Bases is custom sidebar views. Here's one I made for "Related notes".

It finds notes that have overlapping links and tags with the active note. The notes are sorted by a count of overlaps. You can tailor this by counting and sorting other kinds of overlaps, like note titles, embeds, etc — possibly even creating a formula that adds weighting to the overlaps.

[https://github.com/kepano/kepano-obsidian/blob/main/Templates/Bases/Related.base](https://github.com/kepano/kepano-obsidian/blob/main/Templates/Bases/Related.base)

[stephango.com/vault](https://stephango.com/vault "https://stephango.com/vault")

filters:
  and:
    - file.path != this.file.path
formulas:
  LinksOverlap: formula.Related.length
  Related: list(this.file.links).filter(list(file.links).containsAny(value)).unique()
  TagsOverlap: list(this.file.tags).filter(list(file.tags).containsAny(value)).unique().length
properties:
  formula.LinksOverlap:
    displayName: Links overlap
  file.name:
    displayName: Name
  formula.TagsOverlap:
    displayName: Tags overlap
  formula.Related:
    displayName: Links
views:
  - type: table
    name: Related
    filters:
      or:
        - formula.LinksOverlap > 2
        - file.hasLink(this)
        - this.file.hasLink(file)
    order:
      - file.name
      - formula.Related
    sort:
      - property: formula.LinksOverlap
        direction: DESC
      - property: formula.TagsOverlap
        direction: DESC
    limit: 20
    columnSize:
      file.name: 220

![r/ObsidianMD - Base for related notes](https://preview.redd.it/base-for-related-notes-v0-zcl4ibth4dof1.png?width=640&crop=smart&auto=webp&s=ed886b59ed7b29b0889a962e3094362d0b18f330)

---

