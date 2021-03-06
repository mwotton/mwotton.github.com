---
layout: post
title: "the unreasonable effectiveness of suffix trees"
description: ""
category:
tags: [haskell]
---

I needed to find the longest substrings of two separate pages, for
some clustering we're doing on [meanpath](https://meanpath.com). Sadly,
there seemed to be no package for it on Hackage, so I decided to
contribute one.

There may be cleverer ways of doing this, and I'd welcome pull
requests showing better solutions. For now, benchmarks are showing it
running in 1.2ms for ~200 character strings (where the naive version
takes 800ms), 14ms for 2000 characters, and 160ms for 20000
characters, which (extremely unscientifically) looks roughly O(n log n).

Source on [github](https://github.com/mwotton/string-similarity),
package is on [Hackage](https://hackage.haskell.org/package/string-similarity).
Thanks to [Bryan O'Sullivan](https://www.serpentine.com) for the excellent suffix tree library.

