<h5>How to build sqlite shared object (so) with support for SQLCipher on Linux x86_64.</h5>

This tutorial requires that you have make and gcc installed, this is my output from 'gcc -v':

gcc -v <br /><br />
Using built-in specs.
<br />COLLECT_GCC=gcc
<br />COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/5/lto-wrapper
<br />Target: x86_64-linux-gnu
<br />Configured with: ../src/configure -v --with-pkgversion='Ubuntu 5.4.0-6ubuntu1~16.04.4' --with-bugurl=file:///usr/share/doc/gcc-5/README.Bugs --enable-languages=c,ada,c++,java,go,d,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-5 --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-libmpx --enable-plugin --with-system-zlib --disable-browser-plugin --enable-java-awt=gtk --enable-gtk-cairo --with-java-home=/usr/lib/jvm/java-1.5.0-gcj-5-amd64/jre --enable-java-home --with-jvm-root-dir=/usr/lib/jvm/java-1.5.0-gcj-5-amd64 --with-jvm-jar-dir=/usr/lib/jvm-exports/java-1.5.0-gcj-5-amd64 --with-arch-directory=amd64 --with-ecj-jar=/usr/share/java/eclipse-ecj.jar --enable-objc-gc --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu
<br />Thread model: posix
<br />gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.4)


1.Build OpenSSL

Due to an error I got when using OpenSSL_1_0_2l.tar.gz complaining about HMac, see below, I went with using version 1.1.0f instead:
sqlite3.o: In function \`HMAC_CTX_new\':
sqlite3.c:(.text+0x48ef): undefined reference to `CRYPTO_malloc'


a. Download and unpack (tar xvzf openssl-1.1.0f.tar.gz) OpenSSL https://github.com/openssl/openssl/releases (openssl-1.1.0f.tar.gz was the latest at time of writing) to /tmp

b. Install some libs we need:

* sudo apt-get install libc6-dev
* sudo apt-get install libz-dev 

c. In /tmp/openssl-1.1.0f folder run the following commands:

* ./config
* make
* sudo make install
<br /><br />Now the /usr/local/lib/libcrypto.so archive should have been created.

2.Build so (shared object) in SQLChiper

a. Download and unzip to /tmp/sqlcipher

b. sudo apt-get install libedit-dev

c. In /tmp/sqlcipher run the commands:

* ./configure --build=x86_64-linux-gnu --with-crypto-lib=none --disable-tcl --disable-shared --enable-static=yes --enable-tempstore=yes CFLAGS="-DSQLITE_HAS_CODEC -DSQLCIPHER_CRYPTO_OPENSSL -DSQLITE_ENABLE_LOAD_EXTENSION=1 -DSQLITE_HAVE_ISNAN -DSQLITE_HAVE_USLEEP -DSQLITE_ENABLE_UPDATE_DELETE_LIMIT -DSQLITE_ENABLE_COLUMN_METADATA -DSQLITE_CORE -DSQLITE_ENABLE_FTS3 -DSQLITE_ENABLE_FTS3_PARENTHESIS -DSQLITE_ENABLE_FTS5 -DSQLITE_ENABLE_JSON1 -DSQLITE_ENABLE_RTREE -DSQLITE_ENABLE_STAT2 -DSQLITE_THREADSAFE=1 -DSQLITE_DEFAULT_MEMSTATUS=0 -DSQLITE_DEFAULT_FILE_PERMISSIONS=0666 -DSQLITE_MAX_VARIABLE_NUMBER=250000 -DSQLITE_DEFAULT_FOREIGN_KEYS=1 -I/usr/local/include -lm" LDFLAGS="-lcrypto -L/usr/local/lib"

Should give something similar to this output:

    checking build system type... x86_64-pc-linux-gnu
    checking host system type... x86_64-pc-linux-gnu
    checking how to print strings... printf
    checking for gcc... gcc
    checking whether the C compiler works... yes
    checking for C compiler default output file name... a.out
    checking for suffix of executables... 
    checking whether we are cross compiling... no
    checking for suffix of object files... o
    checking whether we are using the GNU C compiler... yes
    checking whether gcc accepts -g... yes
    checking for gcc option to accept ISO C89... none needed
    checking for a sed that does not truncate output... /bin/sed
    checking for grep that handles long lines and -e... /bin/grep
    checking for egrep... /bin/grep -E
    checking for fgrep... /bin/grep -F
    checking for ld used by gcc... /usr/bin/ld
    checking if the linker (/usr/bin/ld) is GNU ld... yes
    checking for BSD- or MS-compatible name lister (nm)... /usr/bin/nm -B
    checking the name lister (/usr/bin/nm -B) interface... BSD nm
    checking whether ln -s works... yes
    checking the maximum length of command line arguments... 1572864
    checking how to convert x86_64-pc-linux-gnu file names to x86_64-pc-linux-gnu format... func_convert_file_noop
    checking how to convert x86_64-pc-linux-gnu file names to toolchain format... func_convert_file_noop
    checking for /usr/bin/ld option to reload object files... -r
    checking for objdump... objdump
    checking how to recognize dependent libraries... pass_all
    checking for dlltool... no
    checking how to associate runtime and link libraries... printf %s\n
    checking for ar... ar
    checking for archiver @FILE support... @
    checking for strip... strip
    checking for ranlib... ranlib
    checking for gawk... gawk
    checking command to parse /usr/bin/nm -B output from gcc object... ok
    checking for sysroot... no
    checking for a working dd... /bin/dd
    checking how to truncate binary pipes... /bin/dd bs=4096 count=1
    checking for mt... mt
    checking if mt is a manifest tool... no
    checking how to run the C preprocessor... gcc -E
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
    checking dynamic linker characteristics... GNU/Linux ld.so
    checking how to hardcode library paths into programs... immediate
    checking whether stripping libraries is possible... yes
    checking if libtool supports shared libraries... yes
    checking whether to build shared libraries... no
    checking whether to build static libraries... yes
    checking for a BSD-compatible install... /usr/bin/install -c
    checking for special C compiler options needed for large files... no
    checking for _FILE_OFFSET_BITS value needed for large files... no
    checking for int8_t... yes
    checking for int16_t... yes
    checking for int32_t... yes
    checking for int64_t... yes
    checking for intptr_t... yes
    checking for uint8_t... yes
    checking for uint16_t... yes
    checking for uint32_t... yes
    checking for uint64_t... yes
    checking for uintptr_t... yes
    checking for sys/types.h... (cached) yes
    checking for stdlib.h... (cached) yes
    checking for stdint.h... (cached) yes
    checking for inttypes.h... (cached) yes
    checking malloc.h usability... yes
    checking malloc.h presence... yes
    checking for malloc.h... yes
    checking for fdatasync... yes
    checking for gmtime_r... yes
    checking for isnan... yes
    checking for localtime_r... yes
    checking for localtime_s... no
    checking for malloc_usable_size... yes
    checking for strchrnul... yes
    checking for usleep... yes
    checking for utime... yes
    checking for pread... yes
    checking for pread64... yes
    checking for pwrite... yes
    checking for pwrite64... yes
    checking for tclsh8.6... tclsh8.6
    configure: Version set to 3.15
    configure: Release set to 3.15.2
    configure: Version number set to 3015002
    checking whether to support threadsafe operation... yes
    checking for library containing pthread_create... -lpthread
    checking for library containing pthread_mutexattr_init... none required
    checking for crypto library to use... none
    checking whether to allow connections to be shared across threads... no
    checking whether to support shared library linked as release mode or not... no
    checking whether to use an in-ram database for temporary tables... yes
    checking if executables have the .exe suffix... unknown
    checking for library containing readline... -ledit
    checking for library containing fdatasync... none required
    checking for library containing dlopen... -ldl
    checking whether to support MEMSYS5... no
    checking whether to support MEMSYS3... no
    configure: creating ./config.status
    config.status: creating Makefile
    config.status: creating sqlcipher.pc
    config.status: creating config.h
    config.status: executing libtool commands



* make clean
* make <br />

Now copy the sqlcipher executable over to sqlite-jdbc/shell/Linux/x86_64
<br />(This is for fiddeling with the database from a terminal should you want to.)

Now go to sqlite-jdbc project and create NativeDB.h:

* mvn compile (so that org.sqlite.core.NativeDB class is generated)
* javah -classpath target/classes -jni -o lib/inc_linux/NativeDB.h org.sqlite.core.NativeDB <br /><br />
Run above command from a command prompt with cd sqlite-jdbc project folder, the file NativeDB.h will be generated in the lib/inc_linux-folder.
* copy folder inc_linux with content to /tmp/sqlcipher<br />
cp -R inc_linux /tmp/sqlcipher/inc_linux
* copy file src/main/java/org/sqlite/core/NativeDB.c to /tmp/sqlcipher <br /><br />
cp src/main/java/org/sqlite/core/NativeDB.c /tmp/sqlcipher
* cd to /tmp/sqlcipher and compile NativeDB.c into NativeDB.o: <br /><br />
gcc -I inc_linux -Wall -fPIC -c -o NativeDB.o NativeDB.c

c. Finally, Build the so-lib:

* gcc -shared sqlite3.o NativeDB.o /usr/local/lib/libcrypto.a -o libsqlitejdbc.so

libsqlitejdbc.so is now created in /tmp/sqlcipher, copy over to sqlite-jdbc/src/main/resources/org/sqlite/native/Linux/x86_64

That's all folks

-jbilander