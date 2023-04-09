# incharcerated (misc)

Simple Ruby jail with character limitations and also preventing calls to `system` and `spawn`.

Probably many ways to solve, but very simple one is just running `eval gets`, letting us input any command and have it be evaluated.

`system` was blacklisted, but command execution with `%x{}` works fine, so inputting `print(%x{cat flag})` prints the flag.
