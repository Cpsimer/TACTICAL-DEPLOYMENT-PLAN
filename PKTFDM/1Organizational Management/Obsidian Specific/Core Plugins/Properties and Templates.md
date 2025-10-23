Almost every note I create starts from a [template](https://github.com/kepano/kepano-obsidian/tree/main/Templates). I use templates heavily because they allow me to lazily add information that will help me find the note later. I have a template for every category with [properties](https://help.obsidian.md/properties) at the top, to capture data such as:

- **Dates** — created, start, end, published
- **People** — author, director, artist, cast, host, guests
- **Themes** — grouping by genre, type, topic, related notes
- **Locations** — neighborhood, city, coordinates
- **Ratings** — more on this below

A few rules I follow for properties:

- Property names and values should aim to be reusable across categories. This allows me to find things across categories, e.g. `genre` is shared across all media types, which means I can see an archive of _Sci-fi_ books, movies and shows in one place.
- Templates should aim to be composable, e.g. _Person_ and _Author_ are two different templates that can be added to the same note.
- Short property names are faster to type, e.g. `start` instead of `start‑date`.
- Default to `list` type properties instead of `text` if there is any chance it might contain more than one link or value in the future.

The [.obsidian/types.json](https://github.com/kepano/kepano-obsidian/blob/main/.obsidian/types.json) file lists which properties are assigned to which types (i.e. `date`, `number`, `text`, etc).

## Rating system

Anything with a `rating` uses an integer from 1 to 7:

- 7 — **Perfect**, must try, life-changing, go out of your way to seek this out
- 6 — **Excellent**, worth repeating
- 5 — **Good**, don’t go out of your way, but enjoyable
- 4 — **Passable**, works in a pinch
- 3 — **Bad**, don’t do this if you can
- 2 — **Atrocious**, actively avoid, repulsive
- 1 — **Evil**, life-changing in a bad way

Why this scale? I like rating out of 7 better than 4 or 5 because I need more granularity at the top, for the good experiences, and 10 is too granular.