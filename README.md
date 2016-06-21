# module_headers

Generates kernel module headers from linux source.

This is based on the [arch linux script][arch-script] which does the same task,
expanded to better handle other architectures.

## Usage

```
usage: gen_mod_headers [target dir] [linux source dir] <objects dir> <arch> <x-compile-prefix>
```

Note that `<objects dir>` defaults to the linux source dir (i.e. for the usual
case where objects are generated in the same directory as the source), and
`arch` defaults to x86.

Ensure `.config` and `Module.symvers` are in the objects directory (this can be
the same as the source directory.) If you don't have Module.symvers, you can
generate it with a kernel build.

If you want to cross-compile, ensure you have the appropriate cross-compile
version of gcc available. Google 'linaro cross compile' if you need to get hold
of one. Then specify the arch, and the prefix of the cross-compiler as shown
above.

If you're generating a typical x86 on x86 build, you can simply run something
like:

```bash
./gen_mod_headers /tmp/headers ~/linux
```

Where `~/linux` here is your linux source code dir, and `/tmp/headers` the
output directory.

For cross compile you should specify architecture and some make parameters,
e.g.:

```bash
./gen_mod_headers /tmp/headers ~/linux ~/linux arm arm-linux-gnueabihf-
```

Here we specify arm and the `arm-linux-gnueabihf-` prefix.

## Compiling against the headers

Typically an external module will expect these header files to be in
`/lib/modules/$(uname -r)/build`, however you can typically override this with a
parameter in the module's `Makefile` like `KERNELDIR` or similar, e.g. `make
KERNELDIR=/tmp/headers`. You will need to check the module's Makefile to see if
they provide an optional argument like:

```make
KERNELDIR ?= /lib/modules/$(shell uname -r)/build
```

The `?=` here means it's optional and can be overridden as described. If this is
not set it isn't too difficult to make whatever variable is pointing at
`/lib/modules/$(uname -r)/build` optional using `?=` so you can override it.

[arch-script]:https://www.archlinux.org/packages/core/x86_64/linux/
