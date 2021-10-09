# ASN1C Compiling from Source and Running Examples on WSL (Ubuntu) and Windows

Before all you can check GNU Autotools: a tutorial

https://elinux.org/images/4/43/Petazzoni.pdf

Author Github

[tpetazzoni (Thomas Petazzoni) / Repositories · GitHub](https://github.com/tpetazzoni?tab=repositories)

## Step-1 Install WSL on Windows (Pro)

You can follow the steps in [GitHub - ucoruh/ns3-wsl-win10-setup: ns3 windows 10 WSL2 setup and usage](https://github.com/ucoruh/ns3-wsl-win10-setup) untill NS3 installation. With this installation you will have WSL that have xfce feature for GUI support.

## Step-2 Enter WSL Environment via Powershell

Open Powershell with Administrator Privilege and then run

```batch
wsl  
```

## Step-2 Download ASN1C from Github

Download latest version from (Step-1 should have GIT installation) you can download with WSL or Windows Environment 

In the following example we used wsl and specified a folder 'asn1c-vlm' for download operatin.

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop$ git clone https://github.com/vlm/asn1c.git asn1c-vlm
Cloning into 'asn1c-vlm'...
remote: Enumerating objects: 19932, done.
remote: Counting objects: 100% (7/7), done.
remote: Compressing objects: 100% (7/7), done.
remote: Total 19932 (delta 2), reused 2 (delta 0), pack-reused 19925
Receiving objects: 100% (19932/19932), 15.10 MiB | 1011.00 KiB/s, done.
Resolving deltas: 100% (13561/13561), done.
Updating files: 100% (935/935), done.
```

Or you can just use following in windows git console

```batch
git clone https://github.com/vlm/asn1c
```

Enter the asn1c folder in WSL, in my case it will look like following located on desktop

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1c$
```

## Step-3 Install More Tools for ASN1C

I'll follow travis.yml script for installation

[asn1c/.travis.yml at master · vlm/asn1c · GitHub](https://github.com/vlm/asn1c/blob/master/.travis.yml)

From this script you can understand compile environment and requirements but in [asn1c/REQUIREMENTS.md at master · vlm/asn1c · GitHub](https://github.com/vlm/asn1c/blob/master/REQUIREMENTS.md)  this requirements not pointed.

```yml
language: c
compiler:
  - gcc
  - clang
env:
  - TASK=check CONFIG_FLAGS="--enable-Werror --enable-test-Werror --enable-code-coverage"
  - TASK=distcheck
before_install:
  - sudo apt-get install -y gcc-multilib lcov libasan*
  - gem install coveralls-lcov
script:
  - autoreconf -iv
  - ./configure $CONFIG_FLAGS
  - make $TASK
after_success:
  - test "x$CC" = "xgcc" -o "x$CC" = "xclang" && make code-coverage-capture && coveralls-lcov asn1c-*-coverage.info
after_failure:
  - find . -name test-suite.log -exec tail -v -n +1 {} +
```

Run

```batch
sudo apt-get install -y gcc-multilib lcov 
```

Run

```batch
gem install cover
```

If you <mark>get error</mark> in this step this will related with gem installation just read the error and copy advised command for gem installation and then retry

## Step-4 Configure and Compile ASN1C from Source

There is a bug that related with DOS or UNIX file format in configure.ac file in asn1c folder use following commands to fix this issue (this solved problem in my case)

```batch
git config core.autocrlf false

git rm --cached -r .  # Don’t forget the dot at the end

git reset --hard
```

After this operation you can check version I have following commit

```batch
ugur.coruh@LAPTOP-RQNNS9IG MINGW64 ~/Desktop/asn1c (master)
$ git log
commit 9925dbbda86b436896108439ea3e0a31280a6065 (HEAD -> master, origin/master, origin/HEAD)
Author: Johannes Lode <jyl@gmx.net>
Date:   Fri May 22 01:29:10 2020 +0200

    Improve test coverage by using stderr output in test evaluation of parser and AST testing.
```

Then start configuration...

```batch
autoreconf -iv
```

Sample output

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1c$ autoreconf -iv
autoreconf: Entering directory `.'
autoreconf: configure.ac: not using Gettext
autoreconf: running: aclocal -I m4
autoreconf: configure.ac: tracing
autoreconf: configure.ac: creating directory config
autoreconf: running: libtoolize --copy
libtoolize: putting auxiliary files in AC_CONFIG_AUX_DIR, 'config'.
libtoolize: copying file 'config/ltmain.sh'
libtoolize: putting macros in AC_CONFIG_MACRO_DIRS, 'm4'.
libtoolize: copying file 'm4/libtool.m4'
libtoolize: copying file 'm4/ltoptions.m4'
libtoolize: copying file 'm4/ltsugar.m4'
libtoolize: copying file 'm4/ltversion.m4'
libtoolize: copying file 'm4/lt~obsolete.m4'
autoreconf: running: /usr/bin/autoconf
autoreconf: running: /usr/bin/autoheader
autoreconf: running: automake --add-missing --copy --no-force
configure.ac:17: installing 'config/ar-lib'
configure.ac:9: installing 'config/compile'
configure.ac:24: installing 'config/config.guess'
configure.ac:24: installing 'config/config.sub'
configure.ac:5: installing 'config/install-sh'
configure.ac:5: installing 'config/missing'
parallel-tests: installing 'config/test-driver'
asn1-tools/enber/Makefile.am: installing 'config/depcomp'
autoreconf: Leaving directory `.'
```

If this success run following command

```batch
./configure --enable-Werror --enable-test-Werror --enable-code-coverage
```

Sample output

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1c$ ./configure --enable-Werror --enable-test-Werror --enable-code-coverage
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for a thread-safe mkdir -p... /usr/bin/mkdir -p
checking for gawk... gawk
checking whether make sets $(MAKE)... yes
checking whether make supports nested variables... yes
checking for gcc... gcc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking whether gcc understands -c and -o together... yes
checking whether make supports the include directive... yes (GNU style)
checking dependency style of gcc... gcc3
checking how to run the C preprocessor... gcc -E
checking whether ln -s works... yes
checking whether make sets $(MAKE)... (cached) yes
checking for bison... no
checking for byacc... no
checking for flex... no
checking for lex... no
checking for ar... ar
checking the archiver (ar) interface... ar
checking for g++... g++
checking whether we are using the GNU C++ compiler... yes
checking whether g++ accepts -g... yes
checking dependency style of g++... gcc3
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking how to print strings... printf
checking for a sed that does not truncate output... /usr/bin/sed
checking for grep that handles long lines and -e... /usr/bin/grep
checking for egrep... /usr/bin/grep -E
checking for fgrep... /usr/bin/grep -F
checking for ld used by gcc... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for BSD- or MS-compatible name lister (nm)... /usr/bin/nm -B
checking the name lister (/usr/bin/nm -B) interface... BSD nm
checking the maximum length of command line arguments... 1572864
checking how to convert x86_64-pc-linux-gnu file names to x86_64-pc-linux-gnu format... func_convert_file_noop
checking how to convert x86_64-pc-linux-gnu file names to toolchain format... func_convert_file_noop
checking for /usr/bin/ld option to reload object files... -r
checking for objdump... objdump
checking how to recognize dependent libraries... pass_all
checking for dlltool... no
checking how to associate runtime and link libraries... printf %s\n
checking for archiver @FILE support... @
checking for strip... strip
checking for ranlib... ranlib
checking command to parse /usr/bin/nm -B output from gcc object... ok
checking for sysroot... no
checking for a working dd... /usr/bin/dd
checking how to truncate binary pipes... /usr/bin/dd bs=4096 count=1
checking for mt... mt
checking if mt is a manifest tool... no
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking for dlfcn.h... yes
checking for objdir... .libs
checking if gcc supports -fno-rtti -fno-exceptions... no
checking for gcc option to produce PIC... -fPIC -DPIC
checking if gcc PIC flag -fPIC -DPIC works... yes
checking if gcc static flag -static works... yes
checking if gcc supports -c -o file.o... yes
checking if gcc supports -c -o file.o... (cached) yes
checking whether the gcc linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... yes
checking how to run the C++ preprocessor... g++ -E
checking for ld used by g++... /usr/bin/ld -m elf_x86_64
checking if the linker (/usr/bin/ld -m elf_x86_64) is GNU ld... yes
checking whether the g++ linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking for g++ option to produce PIC... -fPIC -DPIC
checking if g++ PIC flag -fPIC -DPIC works... yes
checking if g++ static flag -static works... yes
checking if g++ supports -c -o file.o... yes
checking if g++ supports -c -o file.o... (cached) yes
checking whether the g++ linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking dynamic linker characteristics... (cached) GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether make supports nested variables... (cached) yes
checking whether C compiler accepts -Wall... yes
checking whether C compiler accepts -Wcast-qual... yes
checking whether C compiler accepts -Wchar-subscripts... yes
checking whether C compiler accepts -Wmissing-prototypes... yes
checking whether C compiler accepts -Wmissing-declarations... yes
checking whether C compiler accepts -Wno-error=attributes... yes
checking whether C compiler accepts -Wno-error=cast-align... yes
checking whether C compiler accepts -Wno-error=visibility... no
checking whether C compiler accepts -Wno-error=parentheses-equality... no
checking whether C compiler accepts -std=gnu99... yes
checking whether C compiler accepts -Wno-error=unused-variable... yes
checking whether to build with code coverage support... yes
checking for gcov... gcov
checking for lcov... lcov
checking for genhtml... genhtml
checking whether C compiler accepts -coverage... yes
checking for library containing gcov_open... no
checking whether C compiler accepts -std=c89... yes
checking whether C compiler accepts -Wpedantic... yes
checking whether C compiler accepts -Wgnu... no
checking whether C compiler accepts -Wno-long-long... yes
checking whether C compiler and linker accept -fsanitize=undefined... yes
checking whether C compiler accepts -fno-sanitize-recover=undefined... yes
checking whether C compiler and linker accept -fsanitize=unsigned-integer-overflow... no
checking whether C compiler accepts -fno-sanitize-recover=unsigned-integer-overflow... no
checking whether C compiler and linker accept -fsanitize=nullability... no
checking whether C compiler accepts -fno-sanitize-recover=nullability... no
checking whether C compiler accepts -fno-omit-frame-pointer... yes
checking whether C compiler and linker accept -fsanitize=address... yes
checking whether C compiler accepts -fno-omit-frame-pointer... (cached) yes
checking for ANSI C header files... (cached) yes
checking sys/param.h usability... yes
checking sys/param.h presence... yes
checking for sys/param.h... yes
checking whether byte ordering is bigendian... no
checking for off_t... yes
checking for size_t... yes
checking whether struct tm is in sys/time.h or time.h... time.h
checking for __int128... yes
checking for intmax_t... yes
checking size of void *... 8
checking for library containing getopt... none required
checking for strtoimax... yes
checking for strtoll... yes
checking for mergesort... no
checking for mkstemps... yes
checking for timegm... yes
checking whether strcasecmp is declared... yes
checking whether vasprintf is declared... no
checking for pandoc... no
checking that generated files are newer than configure... done
configure: creating ./config.status
config.status: creating tests/tests-c-compiler/check-src/Makefile
config.status: creating tests/tests-asn1c-compiler/Makefile
config.status: creating tests/tests-asn1c-smoke/Makefile
config.status: creating tests/tests-randomized/Makefile
config.status: creating tests/tests-c-compiler/Makefile
config.status: creating tests/tests-skeletons/Makefile
config.status: creating asn1-tools/enber/Makefile
config.status: creating asn1-tools/unber/Makefile
config.status: creating asn1-tools/Makefile
config.status: creating libasn1compiler/Makefile
config.status: creating libasn1common/Makefile
config.status: creating libasn1parser/Makefile
config.status: creating libasn1print/Makefile
config.status: creating libasn1fix/Makefile
config.status: creating doc/docsrc/Makefile
config.status: creating skeletons/Makefile
config.status: creating examples/Makefile
config.status: creating doc/man/Makefile
config.status: creating tests/Makefile
config.status: creating asn1c/Makefile
config.status: creating doc/Makefile
config.status: creating Makefile
config.status: creating config.h
config.status: executing depfiles commands
config.status: executing libtool commands
```

then start make operation with make as follow this will run make all recursive

```batch
sudo make
```

Sample output

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1c$ make
make  all-recursive
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c'
Making all in libasn1common
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1common'
  CC       asn1_ref.lo
  CC       asn1_buffer.lo
  CC       asn1_namespace.lo
  CC       genhash.lo
  CCLD     libasn1common.la
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1common'
Making all in libasn1parser
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
  CC       asn1parser.lo
  CC       asn1p_y.lo
  CC       asn1p_l.lo
  CC       asn1p_module.lo
  CC       asn1p_oid.lo
  CC       asn1p_value.lo
  CC       asn1p_expr.lo
  CC       asn1p_xports.lo
  CC       asn1p_constr.lo
  CC       asn1p_param.lo
  CC       asn1p_class.lo
  CC       asn1p_integer.lo
  CCLD     libasn1parser.la
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
Making all in libasn1fix
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
  CC       asn1fix.lo
  CC       asn1fix_misc.lo
  CC       asn1fix_value.lo
  CC       asn1fix_compat.lo
  CC       asn1fix_constr.lo
  CC       asn1fix_cstring.lo
  CC       asn1fix_retrieve.lo
  CC       asn1fix_bitstring.lo
  CC       asn1fix_constraint.lo
  CC       asn1fix_integer.lo
  CC       asn1fix_crange.lo
  CC       asn1fix_dereft.lo
  CC       asn1fix_derefv.lo
  CC       asn1fix_export.lo
  CC       asn1fix_param.lo
  CC       asn1fix_class.lo
  CC       asn1fix_tags.lo
  CC       asn1fix_enum.lo
  CC       asn1fix_cws.lo
  CC       asn1fix_constraint_compat.lo
  CCLD     libasn1fix.la
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
Making all in libasn1print
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1print'
  CC       asn1print.lo
  CCLD     libasn1print.la
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1print'
Making all in libasn1compiler
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1compiler'
  CC       asn1compiler.lo
  CC       asn1c_misc.lo
  CC       asn1c_out.lo
  CC       asn1c_lang.lo
  CC       asn1c_naming.lo
  CC       asn1c_save.lo
  CC       asn1c_C.lo
  CC       asn1c_constraint.lo
  CC       asn1c_compat.lo
  CC       asn1c_ioc.lo
  CC       asn1c_fdeps.lo
  CCLD     libasn1compiler.la
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1compiler'
Making all in asn1-tools
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
Making all in unber
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
  CC       unber.o
  CC       libasn1_unber_tool.lo
  CCLD     libasn1-unber-tool.la
  CCLD     unber
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
Making all in enber
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/enber'
  CC       enber.o
  CCLD     enber
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/enber'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[3]: Nothing to be done for 'all-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
Making all in asn1c
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1c'
  CC       asn1c.o
  CCLD     asn1c
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1c'
Making all in skeletons
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
make[2]: Nothing to be done for 'all'.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
Making all in tests
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
Making all in tests-asn1c-compiler
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[3]: Nothing to be done for 'all'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
Making all in tests-skeletons
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-skeletons'
make[3]: Nothing to be done for 'all'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-skeletons'
Making all in tests-asn1c-smoke
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-smoke'
make[3]: Nothing to be done for 'all'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-smoke'
Making all in tests-c-compiler
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
Making all in check-src
make[4]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler/check-src'
make[4]: Nothing to be done for 'all'.
make[4]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler/check-src'
make[4]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
make[4]: Nothing to be done for 'all-am'.
make[4]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
Making all in tests-randomized
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-randomized'
make[3]: Nothing to be done for 'all'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-randomized'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
make[3]: Nothing to be done for 'all-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
Making all in examples
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
./crfc2asn1.pl ./rfc3280.txt
Found PKIX1Explicit88 at line 5124
=> Saving as rfc3280-PKIX1Explicit88.asn1
Found PKIX1Implicit88 at line 5850
=> Saving as rfc3280-PKIX1Implicit88.asn1
./crfc2asn1.pl ./rfc3525.txt
Found MEDIA-GATEWAY-CONTROL at line 5177
=> Saving as rfc3525-MEDIA-GATEWAY-CONTROL.asn1
./crfc2asn1.pl ./rfc4511.txt
Found Lightweight-Directory-Access-Protocol-V3 at line 2983
=> Saving as rfc4511-Lightweight-Directory-Access-Protocol-V3.asn1
make  all-am
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
make[3]: Nothing to be done for 'all-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
Making all in doc
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
Making all in docsrc
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/docsrc'
make[3]: Nothing to be done for 'all'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/docsrc'
Making all in man
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/man'
make[3]: Nothing to be done for 'all'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/man'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
make[3]: Nothing to be done for 'all-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c'
```

After processing make validate operation with <mark>check</mark>

```batch
sudo make check
```

sample output

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1c$ sudo make check
Making check in libasn1common
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1common'
make[1]: Nothing to be done for 'check'.
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1common'
Making check in libasn1parser
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make  check_asn1p_integer
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make[2]: 'check_asn1p_integer' is up to date.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make  check-TESTS
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
PASS: check_asn1p_integer
============================================================================
Testsuite summary for asn1c 0.9.29
============================================================================
# TOTAL: 1
# PASS:  1
# SKIP:  0
# XFAIL: 0
# FAIL:  0
# XPASS: 0
# ERROR: 0
============================================================================
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
Making check in libasn1fix
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make  check_crange check_fixer
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make[2]: 'check_crange' is up to date.
make[2]: 'check_fixer' is up to date.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make  check-TESTS
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
PASS: check_crange
PASS: check_fixer
============================================================================
Testsuite summary for asn1c 0.9.29
============================================================================
# TOTAL: 2
# PASS:  2
# SKIP:  0
# XFAIL: 0
# FAIL:  0
# XPASS: 0
# ERROR: 0
============================================================================
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
Making check in libasn1print
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1print'
make[1]: Nothing to be done for 'check'.
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1print'
Making check in libasn1compiler
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1compiler'
make[1]: Nothing to be done for 'check'.
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1compiler'
Making check in asn1-tools
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
Making check in unber
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
make  check_unber \
  check_unber.sh
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
make[3]: 'check_unber' is up to date.
make[3]: Nothing to be done for 'check_unber.sh'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
make  check-TESTS
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
make[4]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
PASS: check_unber.sh
============================================================================
Testsuite summary for asn1c 0.9.29
============================================================================
# TOTAL: 1
# PASS:  1
# SKIP:  0
# XFAIL: 0
# FAIL:  0
# XPASS: 0
# ERROR: 0
============================================================================
make[4]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
Making check in enber
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/enber'
make[2]: Nothing to be done for 'check'.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/enber'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make  check-xxber.sh
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[3]: Nothing to be done for 'check-xxber.sh'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make  check-TESTS
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[4]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
PASS: check-xxber.sh
============================================================================
Testsuite summary for asn1c 0.9.29
============================================================================
# TOTAL: 1
# PASS:  1
# SKIP:  0
# XFAIL: 0
# FAIL:  0
# XPASS: 0
# ERROR: 0
============================================================================
make[4]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
Making check in asn1c
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1c'
make[1]: Nothing to be done for 'check'.
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1c'
Making check in skeletons
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
make  check-converter_example check-converter_c89_example  libasn1cskeletons.la libasn1cskeletons_c89.la
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
make[2]: 'check-converter_example' is up to date.
make[2]: 'check-converter_c89_example' is up to date.
make[2]: 'libasn1cskeletons.la' is up to date.
make[2]: 'libasn1cskeletons_c89.la' is up to date.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
Making check in tests
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
Making check in tests-asn1c-compiler
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make  check-parsing.sh
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[3]: Nothing to be done for 'check-parsing.sh'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make  check-TESTS
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[4]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
FAIL: check-parsing.sh
============================================================================
Testsuite summary for asn1c 0.9.29
============================================================================
# TOTAL: 1
# PASS:  0
# SKIP:  0
# XFAIL: 0
# FAIL:  1
# XPASS: 0
# ERROR: 0
============================================================================
See tests/tests-asn1c-compiler/test-suite.log
Please report to vlm@lionet.info
============================================================================
make[4]: *** [Makefile:544: test-suite.log] Error 1
make[4]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[3]: *** [Makefile:652: check-TESTS] Error 2
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[2]: *** [Makefile:726: check-am] Error 2
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[1]: *** [Makefile:387: check-recursive] Error 1
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
make: *** [Makefile:491: check-recursive] Error 1
```

There is  a error but I tried to install with error and it worked but. When we use ASN1C complier there are some bugs that we can fix manually such as not coping required files to complied folder, then you should manually copy this files. This was the issue 

[undefined reference to `BIT_STRING_decode_oer &#39; Error in compiling · Issue #311 · vlm/asn1c · GitHub](https://github.com/vlm/asn1c/issues/311) that I have to fix.

## Step-5 Install ASN1C to WSL

Install the compiler into a standard location:

```batch
sudo make install   
```

Sample output

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1c$ sudo make install
Making install in libasn1common
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1common'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1common'
make[2]: Nothing to be done for 'install-exec-am'.
make[2]: Nothing to be done for 'install-data-am'.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1common'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1common'
Making install in libasn1parser
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make[2]: Nothing to be done for 'install-exec-am'.
make[2]: Nothing to be done for 'install-data-am'.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1parser'
Making install in libasn1fix
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make[2]: Nothing to be done for 'install-exec-am'.
make[2]: Nothing to be done for 'install-data-am'.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1fix'
Making install in libasn1print
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1print'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1print'
make[2]: Nothing to be done for 'install-exec-am'.
make[2]: Nothing to be done for 'install-data-am'.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1print'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1print'
Making install in libasn1compiler
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1compiler'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1compiler'
make[2]: Nothing to be done for 'install-exec-am'.
make[2]: Nothing to be done for 'install-data-am'.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1compiler'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/libasn1compiler'
Making install in asn1-tools
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
Making install in unber
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
 /usr/bin/mkdir -p '/usr/local/bin'
  /bin/bash ../../libtool   --mode=install /usr/bin/install -c unber '/usr/local/bin'
libtool: install: /usr/bin/install -c unber /usr/local/bin/unber
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/unber'
Making install in enber
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/enber'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/enber'
 /usr/bin/mkdir -p '/usr/local/bin'
  /bin/bash ../../libtool   --mode=install /usr/bin/install -c enber '/usr/local/bin'
libtool: install: /usr/bin/install -c enber /usr/local/bin/enber
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/enber'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools/enber'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[3]: Nothing to be done for 'install-exec-am'.
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1-tools'
Making install in asn1c
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1c'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1c'
 /usr/bin/mkdir -p '/usr/local/bin'
  /bin/bash ../libtool   --mode=install /usr/bin/install -c asn1c '/usr/local/bin'
libtool: install: /usr/bin/install -c asn1c /usr/local/bin/asn1c
make[2]: Nothing to be done for 'install-data-am'.
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1c'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/asn1c'
Making install in skeletons
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
make[2]: Nothing to be done for 'install-exec-am'.
 /usr/bin/mkdir -p '/usr/local/share/asn1c'
 /usr/bin/install -c -m 644 ./README ./ANY.c ./ANY.h ./BIT_STRING.c ./BIT_STRING.h ./BIT_STRING_oer.c ./BMPString.c ./BMPString.h ./BOOLEAN.c ./BOOLEAN.h ./ENUMERATED.c ./ENUMERATED.h ./GeneralString.c ./GeneralString.h ./GeneralizedTime.c ./GeneralizedTime.h ./GraphicString.c ./GraphicString.h ./IA5String.c ./IA5String.h ./INTEGER.c ./INTEGER.h ./INTEGER_oer.c ./ISO646String.c ./ISO646String.h ./NULL.c ./NULL.h ./NativeEnumerated.c ./NativeEnumerated.h ./NativeEnumerated_oer.c ./NativeInteger.c ./NativeInteger.h ./NativeInteger_oer.c ./NativeReal.c ./NativeReal.h ./NumericString.c ./NumericString.h ./OBJECT_IDENTIFIER.c ./OBJECT_IDENTIFIER.h ./OCTET_STRING.c '/usr/local/share/asn1c'
 /usr/bin/install -c -m 644 ./OCTET_STRING.h ./OCTET_STRING_oer.c ./OPEN_TYPE.c ./OPEN_TYPE.h ./OPEN_TYPE_oer.c ./ObjectDescriptor.c ./ObjectDescriptor.h ./PrintableString.c ./PrintableString.h ./REAL.c ./REAL.h ./RELATIVE-OID.c ./RELATIVE-OID.h ./T61String.c ./T61String.h ./TeletexString.c ./TeletexString.h ./UTCTime.c ./UTCTime.h ./UTF8String.c ./UTF8String.h ./UniversalString.c ./UniversalString.h ./VideotexString.c ./VideotexString.h ./VisibleString.c ./VisibleString.h ./asn_SEQUENCE_OF.c ./asn_SEQUENCE_OF.h ./asn_SET_OF.c ./asn_SET_OF.h ./asn_application.c ./asn_application.h ./asn_bit_data.c ./asn_bit_data.h ./asn_codecs.h ./asn_codecs_prim.c ./asn_codecs_prim.h ./asn_internal.c ./asn_internal.h '/usr/local/share/asn1c'
 /usr/bin/install -c -m 644 ./asn_ioc.h ./asn_random_fill.c ./asn_random_fill.h ./asn_system.h ./ber_decoder.c ./ber_decoder.h ./ber_tlv_length.c ./ber_tlv_length.h ./ber_tlv_tag.c ./ber_tlv_tag.h ./constr_CHOICE.c ./constr_CHOICE.h ./constr_CHOICE_oer.c ./constr_SEQUENCE.c ./constr_SEQUENCE.h ./constr_SEQUENCE_OF.c ./constr_SEQUENCE_OF.h ./constr_SEQUENCE_oer.c ./constr_SET.c ./constr_SET.h ./constr_SET_OF.c ./constr_SET_OF.h ./constr_SET_OF_oer.c ./constr_TYPE.c ./constr_TYPE.h ./constraints.c ./constraints.h ./converter-example.c ./der_encoder.c ./der_encoder.h ./oer_decoder.c ./oer_decoder.h ./oer_encoder.c ./oer_encoder.h ./oer_support.c ./oer_support.h ./per_decoder.c ./per_decoder.h ./per_encoder.c ./per_encoder.h '/usr/local/share/asn1c'
 /usr/bin/install -c -m 644 ./per_opentype.c ./per_opentype.h ./per_support.c ./per_support.h ./xer_decoder.c ./xer_decoder.h ./xer_encoder.c ./xer_encoder.h ./xer_support.c ./xer_support.h ./file-dependencies '/usr/local/share/asn1c' /usr/bin/mkdir -p '/usr/local/share/asn1c'
 /usr/bin/mkdir -p '/usr/local/share/asn1c/standard-modules'
 /usr/bin/install -c -m 644  standard-modules/README standard-modules/ASN1-Object-Identifier-Module.asn1 standard-modules/ASN1C-UsefulInformationObjectClasses.asn1 '/usr/local/share/asn1c/standard-modules'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/skeletons'
Making install in tests
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
Making install in tests-asn1c-compiler
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[3]: Nothing to be done for 'install-exec-am'.
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-compiler'
Making install in tests-skeletons
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-skeletons'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-skeletons'
make[3]: Nothing to be done for 'install-exec-am'.
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-skeletons'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-skeletons'
Making install in tests-asn1c-smoke
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-smoke'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-smoke'
make[3]: Nothing to be done for 'install-exec-am'.
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-smoke'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-asn1c-smoke'
Making install in tests-c-compiler
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
Making install in check-src
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler/check-src'
make[4]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler/check-src'
make[4]: Nothing to be done for 'install-exec-am'.
make[4]: Nothing to be done for 'install-data-am'.
make[4]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler/check-src'
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler/check-src'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
make[4]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
make[4]: Nothing to be done for 'install-exec-am'.
make[4]: Nothing to be done for 'install-data-am'.
make[4]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-c-compiler'
Making install in tests-randomized
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-randomized'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-randomized'
make[3]: Nothing to be done for 'install-exec-am'.
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-randomized'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests/tests-randomized'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
make[3]: Nothing to be done for 'install-exec-am'.
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/tests'
Making install in examples
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
make  install-am
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
 /usr/bin/mkdir -p '/usr/local/bin'
 /usr/bin/install -c crfc2asn1.pl '/usr/local/bin'
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/examples'
Making install in doc
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
Making install in docsrc
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/docsrc'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/docsrc'
make[3]: Nothing to be done for 'install-exec-am'.
make[3]: Nothing to be done for 'install-data-am'.
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/docsrc'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/docsrc'
Making install in man
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/man'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/man'
make[3]: Nothing to be done for 'install-exec-am'.
 /usr/bin/mkdir -p '/usr/local/share/man/man1'
 /usr/bin/install -c -m 644 asn1c.1 unber.1 enber.1 '/usr/local/share/man/man1'
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/man'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc/man'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
make[3]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
make[3]: Nothing to be done for 'install-exec-am'.
 /usr/bin/mkdir -p '/usr/local/share/doc/asn1c'
 /usr/bin/install -c -m 644 ./asn1c-quick.pdf ./asn1c-usage.pdf '/usr/local/share/doc/asn1c'
make[3]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c/doc'
make[1]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c'
make[2]: Entering directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c'
make[2]: Nothing to be done for 'install-exec-am'.
 /usr/bin/mkdir -p '/usr/local/share/doc/asn1c'
 /usr/bin/install -c -m 644 README.md INSTALL.md REQUIREMENTS.md FAQ ChangeLog BUGS '/usr/local/share/doc/asn1c'
make[2]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c'
make[1]: Leaving directory '/mnt/c/Users/ugur.coruh/Desktop/asn1c'
```

Display the `asn1c` manual page:

```batch
man asn1c
```

Thats all you can run asn1c command in the console of WSL

## Step-6 Create Circle ASN Example

Check asn1c quick start sheet from [asn1c/asn1c-quick.pdf at master · vlm/asn1c · GitHub](https://github.com/vlm/asn1c/blob/master/doc/asn1c-quick.pdf)

This is also in the asn1c/doc/asn1c-quick.pdf

Open a file named <mark>TestModule.asn1</mark> in a sample folder like <mark>asn1-wsl-sample</mark>

Copy content from pdf and create sample asn1 file

```batch
TestModule DEFINITIONS ::= BEGIN -- Module parameters preamble
 Circle ::= SEQUENCE { -- Definition of Circle type
 position-x INTEGER, -- Integer position
 position-y INTEGER, -- Position along Y-axis
 radius INTEGER (0..MAX) -- Positive radius
 } -- End of Circle type
END -- End of TestModule
```

Save file and run following command for this file in working folder

```batch
asn1c TestModule.asn1
```

This will create BER and XER encoder decoder for Circle.  Output will like that

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1-wsl-sample$ asn1c TestModule.asn1
Compiled Circle.c
Compiled Circle.h
Copied /usr/local/share/asn1c/OPEN_TYPE.h       -> OPEN_TYPE.h
Copied /usr/local/share/asn1c/OPEN_TYPE.c       -> OPEN_TYPE.c
Copied /usr/local/share/asn1c/constr_CHOICE.h   -> constr_CHOICE.h
Copied /usr/local/share/asn1c/INTEGER.h -> INTEGER.h
Copied /usr/local/share/asn1c/INTEGER.c -> INTEGER.c
Copied /usr/local/share/asn1c/NativeInteger.h   -> NativeInteger.h
Copied /usr/local/share/asn1c/NativeInteger.c   -> NativeInteger.c
Copied /usr/local/share/asn1c/constr_CHOICE.c   -> constr_CHOICE.c
Copied /usr/local/share/asn1c/constr_SEQUENCE.h -> constr_SEQUENCE.h
Copied /usr/local/share/asn1c/constr_SEQUENCE.c -> constr_SEQUENCE.c
Copied /usr/local/share/asn1c/asn_application.h -> asn_application.h
Copied /usr/local/share/asn1c/asn_application.c -> asn_application.c
Copied /usr/local/share/asn1c/asn_ioc.h -> asn_ioc.h
Copied /usr/local/share/asn1c/asn_system.h      -> asn_system.h
Copied /usr/local/share/asn1c/asn_codecs.h      -> asn_codecs.h
Copied /usr/local/share/asn1c/asn_internal.h    -> asn_internal.h
Copied /usr/local/share/asn1c/asn_internal.c    -> asn_internal.c
Copied /usr/local/share/asn1c/asn_random_fill.h -> asn_random_fill.h
Copied /usr/local/share/asn1c/asn_random_fill.c -> asn_random_fill.c
Copied /usr/local/share/asn1c/asn_bit_data.h    -> asn_bit_data.h
Copied /usr/local/share/asn1c/asn_bit_data.c    -> asn_bit_data.c
Copied /usr/local/share/asn1c/OCTET_STRING.h    -> OCTET_STRING.h
Copied /usr/local/share/asn1c/OCTET_STRING.c    -> OCTET_STRING.c
Copied /usr/local/share/asn1c/BIT_STRING.h      -> BIT_STRING.h
Copied /usr/local/share/asn1c/BIT_STRING.c      -> BIT_STRING.c
Copied /usr/local/share/asn1c/asn_codecs_prim.c -> asn_codecs_prim.c
Copied /usr/local/share/asn1c/asn_codecs_prim.h -> asn_codecs_prim.h
Copied /usr/local/share/asn1c/ber_tlv_length.h  -> ber_tlv_length.h
Copied /usr/local/share/asn1c/ber_tlv_length.c  -> ber_tlv_length.c
Copied /usr/local/share/asn1c/ber_tlv_tag.h     -> ber_tlv_tag.h
Copied /usr/local/share/asn1c/ber_tlv_tag.c     -> ber_tlv_tag.c
Copied /usr/local/share/asn1c/ber_decoder.h     -> ber_decoder.h
Copied /usr/local/share/asn1c/ber_decoder.c     -> ber_decoder.c
Copied /usr/local/share/asn1c/der_encoder.h     -> der_encoder.h
Copied /usr/local/share/asn1c/der_encoder.c     -> der_encoder.c
Copied /usr/local/share/asn1c/constr_TYPE.h     -> constr_TYPE.h
Copied /usr/local/share/asn1c/constr_TYPE.c     -> constr_TYPE.c
Copied /usr/local/share/asn1c/constraints.h     -> constraints.h
Copied /usr/local/share/asn1c/constraints.c     -> constraints.c
Copied /usr/local/share/asn1c/xer_support.h     -> xer_support.h
Copied /usr/local/share/asn1c/xer_support.c     -> xer_support.c
Copied /usr/local/share/asn1c/xer_decoder.h     -> xer_decoder.h
Copied /usr/local/share/asn1c/xer_decoder.c     -> xer_decoder.c
Copied /usr/local/share/asn1c/xer_encoder.h     -> xer_encoder.h
Copied /usr/local/share/asn1c/xer_encoder.c     -> xer_encoder.c
Copied /usr/local/share/asn1c/per_support.h     -> per_support.h
Copied /usr/local/share/asn1c/per_support.c     -> per_support.c
Copied /usr/local/share/asn1c/per_decoder.h     -> per_decoder.h
Copied /usr/local/share/asn1c/per_decoder.c     -> per_decoder.c
Copied /usr/local/share/asn1c/per_encoder.h     -> per_encoder.h
Copied /usr/local/share/asn1c/per_encoder.c     -> per_encoder.c
Copied /usr/local/share/asn1c/per_opentype.h    -> per_opentype.h
Copied /usr/local/share/asn1c/per_opentype.c    -> per_opentype.c
Copied /usr/local/share/asn1c/oer_decoder.h     -> oer_decoder.h
Copied /usr/local/share/asn1c/oer_encoder.h     -> oer_encoder.h
Copied /usr/local/share/asn1c/oer_support.h     -> oer_support.h
Copied /usr/local/share/asn1c/oer_decoder.c     -> oer_decoder.c
Copied /usr/local/share/asn1c/oer_encoder.c     -> oer_encoder.c
Copied /usr/local/share/asn1c/oer_support.c     -> oer_support.c
Copied /usr/local/share/asn1c/OPEN_TYPE_oer.c   -> OPEN_TYPE_oer.c
Copied /usr/local/share/asn1c/INTEGER_oer.c     -> INTEGER_oer.c
Copied /usr/local/share/asn1c/NativeInteger_oer.c       -> NativeInteger_oer.c
Copied /usr/local/share/asn1c/constr_CHOICE_oer.c       -> constr_CHOICE_oer.c
Copied /usr/local/share/asn1c/constr_SEQUENCE_oer.c     -> constr_SEQUENCE_oer.c
Generated Makefile.am.libasncodec
Copied /usr/local/share/asn1c/converter-example.c       -> converter-example.c implicit
Generated pdu_collection.c
Generated converter-example.mk
Copied /usr/local/share/asn1c/converter-example.c       -> converter-example.c implicit
Generated pdu_collection.c
Generated Makefile.am.asn1convert
```

There is Circle PDU files and asn1 related files

When you try following command to generate decoder you will get error. This is related with missing files that asn1c not copied to working folder. Just copy BIT_STRING__oer.c and OCTET_STRING_oer.c to working folder from skeletons folders that located in asn1c git solution

```batch
cc -DPDU=Circle -I. -o CircleDecoder.exe *.c
```

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1-wsl-sample$ cc -DPDU=Circle -I. -o CircleDecoder.exe *.c
/usr/bin/ld: /tmp/ccpoI2Ov.o:(.data.rel+0x38): undefined reference to `BIT_STRING_decode_oer'
/usr/bin/ld: /tmp/ccpoI2Ov.o:(.data.rel+0x40): undefined reference to `BIT_STRING_encode_oer'
/usr/bin/ld: /tmp/ccTz2UTv.o:(.data.rel+0x38): undefined reference to `OCTET_STRING_decode_oer'
/usr/bin/ld: /tmp/ccTz2UTv.o:(.data.rel+0x40): undefined reference to `OCTET_STRING_encode_oer'
collect2: error: ld returned 1 exit status
```

After coping files try command again

```batch
cc -DPDU=Circle -I. -o CircleDecoder.exe *.c
```

You will see CircleDecoder.exe

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1-wsl-sample$ ./CircleDecoder.exe -h
Usage: ./CircleDecoder.exe [options] <datafile> ...
Where options are:
  -iber        Input is in BER (Basic Encoding Rules) or DER
  -ioer        Input is in OER (Octet Encoding Rules)
  -iper        Input is in Unaligned PER (Packed Encoding Rules) (DEFAULT)
  -ixer        Input is in XER (XML Encoding Rules)
  -oder        Output as DER (Distinguished Encoding Rules)
  -ooer        Output as Canonical OER (Octet Encoding Rules)
  -oper        Output as Unaligned PER (Packed Encoding Rules)
  -oxer        Output as XER (XML Encoding Rules) (DEFAULT)
  -otext       Output as plain semi-structured text
  -onull       Verify (decode) input, but do not output
  -per-nopad   Assume PER PDUs are not padded (-iper)
  -1           Decode only the first PDU in file
  -b <size>    Set the i/o buffer size (default is 8192)
  -c           Check ASN.1 constraints after decoding
  -d           Enable debugging (-dd is even better)
  -n <num>     Process files <num> times
  -s <size>    Set the stack usage limit (default is 30000)
```

Note that you can run vscode in wsl, follow the instruction in the links.

[Work in Windows Subsystem for Linux with Visual Studio Code](https://code.visualstudio.com/docs/remote/wsl-tutorial)

[Get Started with C++ and Windows Subsystem for Linux in Visual Studio Code](https://code.visualstudio.com/docs/cpp/config-wsl)

# Quick start example (rectangle.asn) (asn1c-usage.pdf)

I have build and run rectangle sample on both Linux and Windows with some fixes. 

## A “Rectangle” decoder sample

Create a ﬁle named rectangle.asn with the following contents

```batch
RectangleModule DEFINITIONS ::= BEGIN

Rectangle ::= SEQUENCE {
height INTEGER,
width INTEGER
}

END
```

Compile it into the set of .c and .h ﬁles using asn1c compiler

```batch
asn1c -no-gen-example rectangle.asn
```

By this time, you should have gotten multiple ﬁles in the current directory, including
the Rectangle.c and Rectangle.h

Create a sample main.c for decoding operations

```c
#include <stdio.h>
#include <sys/types.h>
#include <Rectangle.h> /* Rectangle ASN.1 type */

/* Write the encoded output into some FILE stream. */
static int write_out(const void *buffer, size_t size, void *app_key) {
FILE *out_fp = app_key;
size_t wrote = fwrite(buffer, 1, size, out_fp);
return (wrote == size) ? 0 : -1;
}

int main(int ac, char **av) {
Rectangle_t *rectangle; /* Type to encode */
asn_enc_rval_t ec; /* Encoder return value */

/* Allocate the Rectangle_t */
rectangle = calloc(1, sizeof(Rectangle_t)); /* not malloc! */
if(!rectangle) {
perror("calloc() failed");
exit(1);
}

/* Initialize the Rectangle members */
rectangle->height = 42; /* any random value */
rectangle->width = 23; /* any random value */

/* BER encode the data if filename is given */
if(ac < 2) {
fprintf(stderr, "Specify filename for BER output\n");
} else {
const char *filename = av[1];
FILE *fp = fopen(filename, "wb"); /* for BER output */

if(!fp) {
perror(filename);
exit(1);
}

/* Encode the Rectangle type as BER (DER) */
ec = der_encode(&asn_DEF_Rectangle, rectangle, write_out, fp);
fclose(fp);
if(ec.encoded == -1) {
fprintf(stderr, "Could not encode Rectangle (at %s)\n",
ec.failed_type ? ec.failed_type->name : "unknown");
exit(1);
} else {
fprintf(stderr, "Created %s with BER encoded Rectangle\n", filename);
}

}

/* Also print the constructed Rectangle XER encoded (XML) */
xer_fprint(stdout, &asn_DEF_Rectangle, rectangle);

return 0; /* Encoding finished successfully */

}
```

## Open VsCode in WSL

in WSL console enter project working directory and open vscode with

```batch
code .
```

## Build, Run and Debug with VsCode on WSL

Run following command in WSL and source folder

```batch
gcc -I. -o rectangle-decoder.out *.c
```

This will provide an executable for decoder sample. 

If you get error for missing files please copy them to source folder from skeletons folders.

Lets build, run and debug this VsCode in WSL

Create tasks.json file to configure our build tasks for  Linux environment

To access tasks use 

```basic
CTRL+SHIFT+P
```

```yaml
{
	"version": "2.0.0",
	"tasks": [
		{
			"type": "cppbuild",
			"label": "C: wsl gcc.exe build all",
			"command": "gcc",
			"args": [
				"-g",
				"-I${workspaceFolder}",
				"-o",
				"${workspaceFolder}//rectangle-decoder.out",
				"${workspaceFolder}/*.c"
			],
			"options": {
				"pwd": "${fileDirname}"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": {
				"kind": "build",
				"isDefault": true
			},
			"detail": "compiler: \"//usr//bin//gcc\""
		}
	]
}
```

In this configuration we include current workspace and added c extension files for our build. Output file is rectangle-decoder.out

For debugging operations I prefer to generate binary first and then start debugging. You will set this parameters in launch.json

To access debug configuration enter

```basic
CTRL+SHIFT+P
```

and search for >Debug:

Start debugging with F5

```yaml
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "C Runner WSL Debug Session",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/rectangle-decoder.out",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${fileDirname}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ]
    }
  ]
}
```

In this setting we target the rectangle-decoder.out, do not forget the refresh build if you make change on source code. 

> You can open terminal in vscode and access WSL environment but its a bit slow. 



If you run task, terminal will process following command for building binary

```batch
Starting build...
gcc -g -I/mnt/c/Users/ugur.coruh/Desktop/asn1c-wsl-sample/rectangle-sample -o /mnt/c/Users/ugur.coruh/Desktop/asn1c-wsl-sample/rectangle-sample//rectangle-decoder.out /mnt/c/Users/ugur.coruh/Desktop/asn1c-wsl-sample/rectangle-sample/*.c
Build finished successfully.
```

Output binary execution will decode our inputs as follow

```batch
ucoruh@LAPTOP-RQNNS9IG:/mnt/c/Users/ugur.coruh/Desktop/asn1c-wsl-sample/rectangle-sample$ ./rectangle-decoder.out 
Specify filename for BER output
<Rectangle>
    <height>42</height>
    <width>23</width>
</Rectangle>
```

## Build, Run and Debug with VsCode on Windows

You should install mingw-w64 for GCC and set this required paths on launch.json and tasks.json for vscode

In my case my installation folder is 

```batch
C:\\Program Files\\mingw-w64\\x86_64-8.1.0-win32-seh-rt_v6-rev0\\mingw64\\bin\\
```

in this bin folder I have gcc, g++ and gdb environment

Tasks.json file with both Linux and Windows will be like that

```yaml
{
	"version": "2.0.0",
	"tasks": [
		{
			"type": "cppbuild",
			"label": "C: windows gcc.exe build all",
			"command": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-win32-seh-rt_v6-rev0\\mingw64\\bin\\gcc.exe",
			"args": [
				"-fdiagnostics-color=always",
				"-I.",
				"-o",
				"${fileDirname}\\${fileBasenameNoExtension}.exe",
				"-Wall",
				"-Wextra",
				"-Wpedantic",
				"*.c"
			],
			"options": {
				"cwd": "${fileDirname}"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": {
				"kind": "build",
				"isDefault": true
			},
			"detail": "compiler: \"C:\\Program Files\\mingw-w64\\x86_64-8.1.0-win32-seh-rt_v6-rev0\\mingw64\\bin\\gcc.exe\""
		},
		{
			"type": "cppbuild",
			"label": "C: wsl gcc.exe build all",
			"command": "gcc",
			"args": [
				"-g",
				"-I${workspaceFolder}",
				"-o",
				"${workspaceFolder}//rectangle-decoder.out",
				"${workspaceFolder}/*.c"
			],
			"options": {
				"pwd": "${fileDirname}"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": {
				"kind": "build",
				"isDefault": true
			},
			"detail": "compiler: \"//usr//bin//gcc\""
		}
	]
}
```

Launch.json also will like that for Windows and WSL (There is one more configuration not required but VsCode default create this configuration record)

```yaml
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "C Runner WSL Debug Session",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/rectangle-decoder.out",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${fileDirname}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ]
    },
    {
      "name": "C Runner: Windows Debug Session",
      "type": "cppdbg",
      "request": "launch",
      "args": [],
      "stopAtEntry": false,
      "cwd": "c:\\Users\\ugur.coruh\\Desktop\\asn1c-wsl-sample\\rectangle-sample",
      "environment": [],
      "program": "c:\\Users\\ugur.coruh\\Desktop\\asn1c-wsl-sample\\rectangle-sample\\build\\Debug\\outDebug",
      "internalConsoleOptions": "openOnSessionStart",
      "MIMode": "gdb",
      "miDebuggerPath": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-win32-seh-rt_v6-rev0\\mingw64\\bin\\gdb.exe",
      "externalConsole": false,
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ]
    },
    {
      "name": "C/C++ Runner: Debug Session",
      "type": "cppdbg",
      "request": "launch",
      "args": [],
      "stopAtEntry": false,
      "cwd": "c:\\Users\\ugur.coruh\\Desktop\\asn1c-wsl-sample\\rectangle-sample",
      "environment": [],
      "program": "c:\\Users\\ugur.coruh\\Desktop\\asn1c-wsl-sample\\rectangle-sample\\build\\Debug\\outDebug",
      "internalConsoleOptions": "openOnSessionStart",
      "MIMode": "gdb",
      "miDebuggerPath": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-win32-seh-rt_v6-rev0\\mingw64\\bin\\gdb.exe",
      "externalConsole": false,
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ]
    }
  ]
}
```



with this setup if you try to build your code you have to fix something in skeleton files

<mark>asn_system.h </mark>after _WIN32_ flag insert following definitions.

```c
#ifdef	_WIN32

#define PRIuMAX "llu"
#define PRIdMAX "lld"
```

In <mark>asn_random_fill.c </mark>insert following includes and replace random() with rand() (This should be configured with macros for each platform but in our case work for both windows and wsl)

```c
...
#include <stdio.h>
#include <stdlib.h>
...
intmax_t
asn_random_between(intmax_t lb, intmax_t rb) {
    if(lb == rb) {
        return lb;
    } else {
        const uintmax_t intmax_max = ((~(uintmax_t)0) >> 1);
        uintmax_t range = asn__intmax_range(lb, rb);
        uintmax_t value = 0;
        uintmax_t got_entropy = 0;

        assert(RAND_MAX > 0xffffff);    /* Seen 7ffffffd! */
        assert(range < intmax_max);

        for(; got_entropy < range;) {
            got_entropy = (got_entropy << 24) | 0xffffff;
            value = (value << 24) | (rand() % 0xffffff);
        }

        return lb + (intmax_t)(value % (range + 1));
    }
}
```

With this modifications you can compile and run application from console or vscode

When we run task it process following command in terminal

```batch
"C:\Program Files\mingw-w64\x86_64-8.1.0-win32-seh-rt_v6-rev0\mingw64\bin\gcc.exe" -fdiagnostics-color=always -I. -o C:\Users\ugur.coruh\Desktop\asn1c-wsl-sample\rectangle-sample\main.exe -Wall -Wextra -Wpedantic *.c
```

If we run exe we can see output 

```batch
PS C:\Users\ugur.coruh\Desktop\asn1c-wsl-sample\rectangle-sample> .\main.exe
Specify filename for BER output
<Rectangle>
    <height>42</height>
    <width>23</width>
</Rectangle>
PS C:\Users\ugur.coruh\Desktop\asn1c-wsl-sample\rectangle-sample>
```

Thats all...
