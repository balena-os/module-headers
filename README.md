# module_headers

Generates kernel module headers from linux source.

This is based on the [arch linux script][arch-script] which does the same task,
expanded to better handle other architectures.

## Usage

```
usage: gen_mod_headers [linux source dir] [target dir] <karch?=x86> <make directives>
```

Firstly, copy `.config` and `Module.symvers` into your kernel source
directory. This files are required for external module building. If you don't
have Module.symvers, you can generate it with a kernel build.

If you want to cross-compile, ensure you have the appropriate cross-compile
version of gcc available. Google 'linaro cross compile' if you need to get hold
of one.

If you're generating a typical x86 on x86 build, you can simply run something
like:

```bash
./gen_mod_headers ~/linux /tmp/headers
```

Where `~/linux` here is your linux source code dir, and `/tmp/headers` the
output directory.

For cross compile you should specify architecture and some make parameters, e.g.:

```bash
./gen_mod_headers ~/linux /tmp/headers arm ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-
```

Here we specify arm and pass the `make` parameters `ARCH=arm` and
`CROSS_COMPILE=arm-linux-gnueabihf-` which specify the target architecture and
the prefix for your cross-compile gcc binaries, in this example they are
`arm-linux-gnueabihf-gcc`, `arm-linux-gnueabihf-as`, etc.

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
