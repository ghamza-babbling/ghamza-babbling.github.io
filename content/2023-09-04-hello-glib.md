---
title: "Hello glib"
taxonomies:
  tags: ["glib", "programming", "c"]
---

I have minimal knowledge of `C` from university: Algorithms and Data Structure, Operating systems, and compiler design courses. But to be accurate, I haven't felt I'm writing `C` correctly. I've been hearing about something called `glib` but didn't bother having a look; I mean, it is a low-level `lib`. But after working with `GStreamer` that depends on `glib`, I became curious.

First things first, linking sucks. Working with `C` will make me increasingly appreciate `Rust`.

The `main.c` file:

```c
#include <glib.h>
#include <stdio.h>

int main() {
  GList* list = NULL;
  list = g_list_append(list, "Hello world!");
  printf("The first item is '%s'\n", g_list_first(list)->data);
  return 0;
}
```

Then my `Makefile`:

```makefile
prog_name = trying-glib

.PHONY: all

all: compile run

compile:
	@mkdir -p build
	@gcc -Wall main.c -o ./build/$(prog_name) $$(pkg-config --cflags --libs glib-2.0)

run:
	@./build/$(prog_name)

clean:
	@rm -rf ./build
```

Now I can run `make`; the program should compile and run.

And that's a wrap!
