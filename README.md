A GNU wc clone in Rust
======================
Rust-wc is a GNU wc clone written in rust. This project was a way for me to
learn some Rust and play around with the language. I also wanted to see if my
rust implementation could perform as good as or better than GNU wc.

Usage
-----
```
Usage:
  rust-wc [OPTION]... [FILE]...
  rust-wc [OPTION]... --files0-from F

Print newline, word, and byte counts for each FILE, and a total if more than
one FILE is specified. If no FILEs are provide or if FILE is -, then read from
stdin. A word is a sequence of characters delimited by white space. The
characters count is a count of valid UTF-8 encoded unicode characters.

Which counts are printed can be filtered using the options below. The counts
are always printed in the follwoing order: newline, word, character, byte,
longest line. Counts are separated by whitespace followed by the file name.

Options:
    -c, --bytes         print the byte counts
    -m, --chars         print unicode character counts
    -l, --lines         print the newline counts
    -L, --max-line-length 
                        print the length of the longest line
    -w, --words         print the word counts
        --files0-from F read input file list from the specified file
                        containing a NUL-terminated list of file names; use -
                        to read from stdin
    -h, --help          display this help text and exit
    -v, --show-version  output version information and exit
```

Differences from GNU wc
-----------------------
Rust-wc and GNU wc have the same command line interface and should behave the
same given the same command line options. There are some slight differences
with the whitespace in the output between the two programs. The error messages
between the two programs are also slightly different. GNU wc is faster for the
`-c` (bytes) and `-l` (lines) options, rust-wc is slightly faster for
everything else.

GNU wc is licenced under the GPLv3+ and rust-wc is licenced under the MIT
license.

Future Improvements
-------------------
There is an `mmap` branch that contains an implementation using mmap instead of
standard file io that is even faster than the implementation in `master`.

Presumably I could copy the same optimizations that GNU wc does and be about as
fast, but I wanted to challenge myself so I didn't look at the GNU wc code.

Some Lessons Learned
--------------------

- Buffering is your friend. Using `io::BufReader` and `io::BufWriter` provided
some noticeable speedups.
- `io::StdinLock` and `io::StdoutLock` are also your friends.
- `str::from_utf8` isn't super quick. For my usage, I realized I could count unicode characters by inspecting individual bytes while iterating rather than collecting bytes in a buffer and calling `str::from_utf8` to find whole characters. This also provided noticeable speedups.
- Mmap is even better than buffering. I implemented mmap support in a branch. I didn't land it to master because the error messages for missing files and directories change significantly from GNU wc.

License
-------
Rust-wc is distributed under the MIT license. See [LICENSE](LICENSE).
