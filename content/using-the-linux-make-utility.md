Title: Using the Linux `make` Utility
Date: 2019-06-15
Category: Technology
Tags: linux, devops, cli

# Using the Linux `make` Utility

The `make` utility in linux provides a way to define command line level
instructions for creating various artifacts based on specified
dependencies. Although this was originally developed (presumably) for
managing the build process for computer code written in the `c`
language, the tool is sufficiently general to be used for many similar
tasks.

The key element to effective use of `make` is the `Makefile`, which is a
text file that includes instructions on the various targets, their
dependencies, and the \"recipe\" for building the targets. By default,
this file should be named as either `makefile` or `Makefile`, although
command line arguments to the `make` utility can specify different
files.

Each `makefile` includes patterns of the following form:

    <target(s)>: <dependency(s)>
        <recipe instructions...>

Generally, the specified `target` is the name of a file that will be
produced from the `recipe` instructions when the `make` utility is run
and changes are detected in the `dependencies`. There can be multiple
dependencies specified as space-separated file names. Similarly, the
`recipe` can consist of multiple lines, each starting with a `tab`
character by default.

It is important to note, however, that each individual line in the
`recipe` will be executed in the shell in its own process, so changes on
earlier lines might not be create the desired effect. For instance, you
cannot change the directory on one line, and then work from within this
directory on the next line. To handle this issue, you can use all of the
shell\'s syntax to create compound lines, for example, enclosing in
parentheses, using a continuation character, etc.

For example, if the `makefile` includes the following rule, which would
update (or create) the file `makefile-changed.txt` whenever a change is
detected in the `makefile`, you might be surprised to find that the
updated file is *not* in the parent of the current directory, but rather
in the current directory itself, and both `pwd` commands will output the
location of this same directory.

    makefile-changed.txt: makefile
        @pwd
        @cd ..
        @touch makefile-changed.txt
        @pwd

By contrast, the following `makefile` would update the text file in the
parent directory as was probably intended.

    makefile-changed.txt: makefile
        @pwd
        @cd .. ;\
        touch makefile-changed.txt ;\
        pwd

A single `tab` character is required to precede each line in the recipe,
however this is an unfortunate choice, since it is generally
indistinguishable from a series of spaces. Alternately, you can include
the `.RECIPEPREFIX` instruction in the `makefile` to specify a single
character to use in place of the `tab`.

    .RECIPEPREFIX = :   # This sets the recipe prefix character to a colon (`:`)

    makefile-changed.txt: makefile
    :@pwd ;\
    :cd .. && touch makefile-changed.txt && pwd
