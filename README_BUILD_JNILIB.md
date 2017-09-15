<h5>How to build sqlite jnilib (dynamiclib) with support for SQLCipher on MacOSX x86_64.</h5>

This tutorial requires that you have Xcode with gcc installed, this is my output from 'gcc -v':

gcc -v <br /><br />
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.12.sdk/usr/include/c++/4.2.1
<br />Apple LLVM version 8.1.0 (clang-802.0.42)
<br />Target: x86_64-apple-darwin16.7.0
<br />Thread model: posix
<br />InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin


1.Build OpenSSL

a. Download and unzip OpenSSL https://github.com/openssl/openssl/releases (OpenSSL_1_0_2l.zip was the latest at time of writing) to /tmp

b. In /tmp/openssl-OpenSSL_1_0_2l folder run the following commands:

* ./Configure darwin64-x86_64-cc --openssldir=/usr/local/ssl/macosx-x86_64
* make depend
* sudo make install
<br /><br />Now the /usr/local/ssl/macosx-x86_64/lib/libcrypto.a static archive should have been created.

2.Build jnilib in SQLChiper

a. Download and unzip to /tmp/sqlcipher

b. In /tmp/sqlcipher run the commands below, alter to your target version (mine was darwin16.7.0, you can check your target with 'uname -v' or 'gcc -v'):

* ./configure --build=x86_64-apple-darwin16.7.0 --with-crypto-lib=none --disable-tcl --disable-shared --enable-static=yes --enable-tempstore=yes CFLAGS="-DSQLITE_HAS_CODEC -DSQLCIPHER_CRYPTO_OPENSSL -DSQLITE_ENABLE_LOAD_EXTENSION=1 -DSQLITE_HAVE_ISNAN -DSQLITE_HAVE_USLEEP -DSQLITE_ENABLE_UPDATE_DELETE_LIMIT -DSQLITE_ENABLE_COLUMN_METADATA -DSQLITE_CORE -DSQLITE_ENABLE_FTS3 -DSQLITE_ENABLE_FTS3_PARENTHESIS -DSQLITE_ENABLE_FTS5 -DSQLITE_ENABLE_JSON1 -DSQLITE_ENABLE_RTREE -DSQLITE_ENABLE_STAT2 -DSQLITE_THREADSAFE=1 -DSQLITE_DEFAULT_MEMSTATUS=0 -DSQLITE_DEFAULT_FILE_PERMISSIONS=0666 -DSQLITE_MAX_VARIABLE_NUMBER=250000 -DSQLITE_DEFAULT_FOREIGN_KEYS=1 -I/usr/local/ssl/macosx-x86_64/include -arch x86_64" LDFLAGS="/usr/local/ssl/macosx-x86_64/lib/libcrypto.a"

Should give something similar to this output:

    checking build system type... x86_64-apple-darwin16.7.0
    checking host system type... x86_64-apple-darwin16.7.0
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
    checking for a sed that does not truncate output... /usr/bin/sed
    checking for grep that handles long lines and -e... /usr/bin/grep
    checking for egrep... /usr/bin/grep -E
    checking for fgrep... /usr/bin/grep -F
    checking for ld used by gcc... /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ld
    checking if the linker (/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ld) is GNU ld... no
    checking for BSD- or MS-compatible name lister (nm)... /usr/bin/nm -B
    checking the name lister (/usr/bin/nm -B) interface... BSD nm
    checking whether ln -s works... yes
    checking the maximum length of command line arguments... 196608
    checking how to convert x86_64-apple-darwin16.7.0 file names to x86_64-apple-darwin16.7.0 format... func_convert_file_noop
    checking how to convert x86_64-apple-darwin16.7.0 file names to toolchain format... func_convert_file_noop
    checking for /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ld option to reload object files... -r
    checking for objdump... objdump
    checking how to recognize dependent libraries... pass_all
    checking for dlltool... no
    checking how to associate runtime and link libraries... printf %s\n
    checking for ar... ar
    checking for archiver @FILE support... no
    checking for strip... strip
    checking for ranlib... ranlib
    checking for gawk... no
    checking for mawk... no
    checking for nawk... no
    checking for awk... awk
    checking command to parse /usr/bin/nm -B output from gcc object... ok
    checking for sysroot... no
    checking for a working dd... /bin/dd
    checking how to truncate binary pipes... /bin/dd bs=4096 count=1
    checking for mt... no
    checking if : is a manifest tool... no
    checking for dsymutil... dsymutil
    checking for nmedit... nmedit
    checking for lipo... lipo
    checking for otool... otool
    checking for otool64... no
    checking for -single_module linker flag... yes
    checking for -exported_symbols_list linker flag... yes
    checking for -force_load linker flag... yes
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
    checking if gcc supports -fno-rtti -fno-exceptions... yes
    checking for gcc option to produce PIC... -fno-common -DPIC
    checking if gcc PIC flag -fno-common -DPIC works... yes
    checking if gcc static flag -static works... no
    checking if gcc supports -c -o file.o... yes
    checking if gcc supports -c -o file.o... (cached) yes
    checking whether the gcc linker (/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ld) supports shared libraries... yes
    checking dynamic linker characteristics... darwin16.7.0 dyld
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
    checking malloc.h usability... no
    checking malloc.h presence... no
    checking for malloc.h... no
    checking for fdatasync... yes
    checking for gmtime_r... yes
    checking for isnan... yes
    checking for localtime_r... yes
    checking for localtime_s... no
    checking for malloc_usable_size... no
    checking for strchrnul... no
    checking for usleep... yes
    checking for utime... yes
    checking for pread... yes
    checking for pread64... no
    checking for pwrite... yes
    checking for pwrite64... no
    checking for tclsh8.6... no
    checking for tclsh8.5... tclsh8.5
    configure: Version set to 3.15
    configure: Release set to 3.15.2
    configure: Version number set to 3015002
    checking whether to support threadsafe operation... yes
    checking for library containing pthread_create... none required
    checking for library containing pthread_mutexattr_init... none required
    checking for crypto library to use... none
    checking whether to allow connections to be shared across threads... no
    checking whether to support shared library linked as release mode or not... no
    checking whether to use an in-ram database for temporary tables... yes
    checking if executables have the .exe suffix... unknown
    checking for library containing readline... -ledit
    checking for library containing fdatasync... none required
    checking for library containing dlopen... none required
    checking whether to support MEMSYS5... no
    checking whether to support MEMSYS3... no
    configure: creating ./config.status
    config.status: creating Makefile
    config.status: creating sqlcipher.pc
    config.status: creating config.h
    config.status: config.h is unchanged
    config.status: executing libtool commands

* make clean
* make sqlite3.c <br />
(this creates sqlite3.o)

generate sqlcipher executable:

gcc -DSQLITE_HAS_CODEC -DSQLCIPHER_CRYPTO_OPENSSL -DSQLITE_HAVE_ISNAN -DSQLITE_HAVE_USLEEP -DSQLITE_CORE -DSQLITE_THREADSAFE=1 -DSQLITE_DEFAULT_MEMSTATUS=0 -DSQLITE_DEFAULT_FILE_PERMISSIONS=0666 -DSQLITE_MAX_VARIABLE_NUMBER=250000 -DSQLITE_DEFAULT_FOREIGN_KEYS=1 -I/usr/local/ssl/macosx-x86_64/include -arch x86_64 -DSQLITE_OS_UNIX=1 -I. -I/tmp/sqlcipher/src -I/tmp/sqlcipher/ext/rtree -I/tmp/sqlcipher/ext/icu -I/tmp/sqlcipher/ext/fts3 -I/tmp/sqlcipher/ext/async -I/tmp/sqlcipher/ext/session -D_HAVE_SQLITE_CONFIG_H -DBUILD_sqlite -DNDEBUG -DSQLITE_THREADSAFE=1 -DSQLITE_ENABLE_LOAD_EXTENSION=1 -DSQLITE_ENABLE_UPDATE_DELETE_LIMIT -DSQLITE_ENABLE_COLUMN_METADATA -DSQLITE_ENABLE_FTS3 -DSQLITE_ENABLE_FTS3_PARENTHESIS -DSQLITE_ENABLE_FTS5 -DSQLITE_ENABLE_JSON1 -DSQLITE_ENABLE_RTREE -DSQLITE_ENABLE_STAT2 -DHAVE_READLINE=0 -DHAVE_EDITLINE=1 -DSQLITE_ENABLE_JSON1 -DSQLITE_ENABLE_FTS4 -DSQLITE_ENABLE_EXPLAIN_COMMENTS -DSQLITE_ENABLE_UNKNOWN_SQL_FUNCTION -o sqlcipher /tmp/sqlcipher/src/shell.c  /usr/local/ssl/macosx-x86_64/lib/libcrypto.a sqlite3.o -ledit

Now copy the sqlcipher executable over to sqlite-jdbc/shell/Mac/x86_64
<br />(This is for fiddeling with the database from a terminal should you want to.)

Now go to sqlite-jdbc project and create NativeDB.h:

* mvn compile (so that org.sqlite.core.NativeDB class is generated)
* javah -classpath target/classes -jni -o lib/inc_mac/NativeDB.h org.sqlite.core.NativeDB <br /><br />
Run above command from a command prompt with cd sqlite-jdbc project folder, the file NativeDB.h will be generated in the lib/inc_mac-folder.
* copy folder inc_mac with content to /tmp/sqlcipher<br />
cp -R inc_mac /tmp/sqlcipher/inc_mac
* copy file src/main/java/org/sqlite/core/NativeDB.c to /tmp/sqlcipher <br /><br />
cp src/main/java/org/sqlite/core/NativeDB.c /tmp/sqlcipher
* cd to /tmp/sqlcipher and compile NativeDB.c into NativeDB.o: <br /><br />
gcc -I inc_mac -c -o NativeDB.o NativeDB.c

c. Finally, Build the jnilib:
* gcc -dynamiclib -o libsqlitejdbc.jnilib /usr/local/ssl/macosx-x86_64/lib/libcrypto.a sqlite3.o NativeDB.o

libsqlitejdbc.jnilib is now created in /tmp/sqlcipher, copy over to sqlite-jdbc/src/main/resources/org/sqlite/native/Mac/x86_64

That's all folks

-jbilander