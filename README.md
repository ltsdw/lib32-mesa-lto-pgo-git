# lib32-mesa-lto-pgo-git

# how to use

 - change the PGO value at the PKGBUILD to "generate" at the first time compiling.
 - compile and install the mesa with PGO="generate".
 - run some graphic related things, like gaming, editing (videos, image, etc), run some benchmarks, etc.
 - after you run some applications to feed the profile recompile with PGO="use" and install again.
