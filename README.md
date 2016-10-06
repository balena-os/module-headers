# module_headers

Generates kernel module headers from linux source.

This is based on the [arch linux script][arch-script] which performs the same
task. We expand it to better handle cross-compiling to other architectures.

## Usage

```
usage: gen_mod_headers [target dir] [linux source dir] [objects dir] <arch> <x-compile-prefix> <customcc>
```

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
./gen_mod_headers /tmp/headers ~/kerndev/kernels/linux ~/kerndev/linux-obj
```

Where `/tmp/headers` is the desired output directory, `~/kerndev/kernels/linux`
is your linux source code dir, and `~/kerndev/linux-obj` contains `.config`
and `Module.symvers`.

For cross compile you need to specify architecture and the prefix for your
cross-compiler binaries, e.g.:

```bash
./gen_mod_headers /tmp/headers ~/kerndev/kernels/linux-arm ~/kerndev/linux-arm-obj arm arm-linux-gnueabihf-
```

__IMPORTANT:__ Note that the script will generate headers that can only be used
to compile modules on the target architecture.

### Advanced

If you need to use a specific custom cross-compiler when building binaries in
the `scripts/` directory, set `customcc` accordingly.

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
