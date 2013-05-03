---
layout: post
title: "guard for haskell/cabal/hspec projects"
description: ""
category: 
tags: [haskell]
---
{% include JB/setup %}

I've recently moved most of my development work to Haskell, and in the
main, it's been extremely successful. 
High-concurrency work has been a breeze: the only difficulties I've
hit have been to do with operating system limits, not Haskell's. 

Ruby still has an amazing set of developer niceties, however, and I
miss them in Haskell. So, here's how to get some of the love back,
with Guard.

If you haven't seen it, Guard is a tool that watches your code and
tests directories for file changes. When a test changes, the test is
run again (and only that test); when code changes, all the tests for
that piece of code are run. Being Ruby, it has to rely on conventions
to determine that relationship: in general, it expects a one-to-one
mapping from spec/foo_spec.rb to lib/foo.rb, and covers its arse by
running the whole test suite whenever the set of tests it's been
instructed to run come up green.

Now, we could do better in Haskell, because we statically know all of
our code dependencies. However, the build process is a bit involved
and I'd need to dig into hspec a little - for the moment I'm just
going to run "cabal build && cabal test" every time I detect a change
in ./test. Brute force works first time, every time. We will add one
little tweak in: I've set a flag in my cabal file

       Flag onlyTests
            Default: False

and

       if flag(onlyTests)
         Buildable: False

in the stanza for my executable, as linking it takes a fair while.

Then, when I run

    cabal configure -fonlyTests --enable-tests --disable-shared --disable-executable-profiling --disable-library-profiling

so that everything I don't absolutely need to run the tests is turned
off,

    cabal build && cabal test

takes 7 seconds from a standing start, and 5 the second time (where
the test executable is already built). This could be improved, but
I'll cope for now.

Finally, I install guard (I assume you have a ruby interpreter on your
system)


    gem install guard guard-shell

and throw in a Guardfile at the base of my Cabal project.

    guard :shell do
      watch(%r{.*\.cabal$}) do
        `cabal build && cabal test`
      end

      watch(%r{src/.*hs}) do
        `cabal build && cabal test`
      end

      watch(%r{test/.*hs}) do
        `cabal build && cabal test`
      end

    end


Now, when i run "guard" in my haskell project, my tests run every time
I save.

This could be improved in many ways - we could run just the tests that
guard tells us have actually changed, we could have a final step where
we try to build the executable as well (or at least check that it
compiles, since the slow step is linking). This can also collide in an
unfortunate way with ghc-mod - ghc-mod will periodically save files to
check if they compile. If anyone's looking for a fun little project
that would be of active benefit to the community, here's a place to
start.