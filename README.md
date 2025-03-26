# mips32-toolchain
## Dependencies:
Install Clang. Relatively old versions are fine.
Install qemu (user) (`sudo apt install qemu-system-mips qemu-user`).
Install Make.

### Building
1. Download and extract musl:
`wget https://musl.libc.org/releases/musl-1.2.4.tar.gz`
`tar -xvf musl-1.2.4.tar.gz`

2. Build musl:
```
CLANG_VERSION = clang

all: musl_build

musl_build:
	mkdir -p musl_build
	cd musl_build && \
		RANLIB=llvm-ranlib \
		AR=llvm-ar \
		CC=$(CLANG_VERSION) \
		CFLAGS="-target mips-linux-gnu -march=mips32" \
		../musl-1.2.4/configure \
		--target=mips-linux-gnu \
		--disable-shared \
		--prefix=`pwd`/install && \
		make -j`nproc` && \
		make install

clean:
	rm -rf musl_build

```

3. Compiling a test program:
Create a simple program like:
```C
#include <stdio.h>

int main() {
    printf("Hello, mips32!\n");
    return 0;
}
```
Build with:
```
clang --target=mips-linux-gnu -march=mips32 -static \
    -Lmusl_build/install/lib -Imusl_build/install/include \
    hello.c -o hello
```

Test in qemu:
qemu-mips ./hello
