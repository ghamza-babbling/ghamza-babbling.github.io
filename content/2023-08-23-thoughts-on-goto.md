---
title: "Thoughts on goto"
taxonomies:
  tags: ["tech_thoughts", "programming"]
---
I wanted to learn production C programming and was pointed towards reading simple FreeBSD utils, like `echo.c` and `cat.c`.

[`echo.c`](http://git.savannah.gnu.org/gitweb/?p=coreutils.git;a=blob_plain;f=src/echo.c;hb=HEAD) should be easier so lemme start with that. Well, the `goto`s were a deal breaker. Who uses `goto` in 2023?

```c
for (i = 0; temp[i]; i++)
    switch (temp[i])
    {
    case 'e': case 'E': case 'n':
        break;
    default:
        goto just_echo;
    }

if (i == 0)
    goto just_echo;

/* ... */

just_echo:

  if (do_v9 || posixly_correct)
    {
      while (argc > 0)
        {
          char const *s = argv[0];
          unsigned char c;
/* ... */
```

This is madness!

Well, no. `goto` is a tool. Indeed it breaks the control flow. But sometimes, we do need to break it.

## try-catch blocks

Well, a `try-catch` block or `do-catch` in languages like Swift are an application of a `goto`. We go to the `catch` block if a failable operation fails. It crealy interrupts the control flow same as `goto` does, but people accept that because it has good deeds and can't be abused as much as `goto`.

```java
static int failableFunc() throws Exception {
    Exception up = new Exception();
    throw up;
}

public static void main(String[] args) {
    try {
        failableFunc();
        System.out.println("Program will `goto` the catch block, so I'm skipped");
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

Rust took another approach by making failable operations return a `Result` that can either be an `Ok(value)` or `Err(why)`. So we either have to handle the error on the same line and not interrupt the control flow or bubble up the error so we can handle the error in the high-level modules.

## labeled loops

Rust also breaks the control flow by having labeled loops, so we can break from the entirety of nested loops without having flags and smelly code.

```rust
'outer: loop {
    println!("Entered the outer loop");

    'inner: loop {
        println!("Entered the inner loop");

        // This would break only the inner loop
        //break;

        // This breaks the outer loop
        break 'outer;
    }

    println!("This point will never be reached");
}
```

Java also can label loops.

```java
outer: for(;;) {
   inner: for(;;) {
     break outer;
   }
}
```

## Cleanup

Swift and Go uses `defer`, a block that runs at the end of a function to clean up resources, for example.

```swift
func writeLog() {
    let file = openFile()
    defer { closeFile(file) } /* Will be executed at the end */

    /* write to file */
}
```

```go
func writeLog() {
    f := createFile("/tmp/defer.txt")
    defer closeFile(f)

    /* write to file */
}
```

Why Swift and Go have such a keyword? To have a single block of cleaning resources while multiple branches that lead to exit the function.

## Conclution

`goto`s are similar to Rust's unsafe, we should avoid them in our day-to-day, but when the time comes, they're tools to help us solve problems.

In fact, [Chris Gillum summarized the possible uses of `goto`](https://stackoverflow.com/a/24476/8943195), which are:

- Cleanly exiting a function (what Swift does)
- Exiting nested loops (what Java and Rust do)
- Low-level performance improvements (what system and game devs do)

And maybe:

- Error Handling using `try-catch` (But languages are adopting the function model with `Result`)
