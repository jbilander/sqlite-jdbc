<h5>How to compile SQLCipher 64-bit dll for Windows Desktop using Visual Studio 2012 or 2013 and TDM-GCC-64 MinGW Compiler.</h5>

1.First we need to build OpenSSL and we do that here with Visual Studio:

a. Install Visual Studio 2012 or 2013. Express version may be sufficient. We want to be able to run the following bat-file in a cmd-prompt: 
"C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin\x86_amd64\vcvarsx86_amd64.bat" 

b. Download and Install ActivePerl, latest was ActivePerl-5.24.1.2402-MSWin32-x64-401627.exe at the time of writing.

c. Get the most recent stable source for openssl (OpenSSL_1_0_2l.zip at this time) and extract to C:\opensslsrc

d. Run the vcvarsx86_amd64.bat file in a cmd-prompt:
"C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin\x86_amd64\vcvarsx86_amd64.bat"

cd to C:\opensslsrc

Once you are in the correct directory (C:\opensslsrc) run the following commands in order:

* perl Configure no-asm VC-WIN64A --prefix=C:\opensslbuild64
* ms\do_win64a.bat
* nmake -f ms\nt.mak
* nmake -f ms\nt.mak install
* nmake -f ms\ntdll.mak
* nmake -f ms\ntdll.mak install

In your openssl build directory (c:\opensslbuild64) you will now have: lib\libeay32.lib and bin\libeay32.dll among some other files.

2.Building SQLCipher

a. Extract the source of sqlcipher to C:\sqlcipher

b. Copy from opensslbuild64 lib\libeay32.lib and bin\libeay32.dll to C:\sqlcipher

c. Download and install TDM-GCC MinGW Compiler (tdm64-gcc-5.1.0-2.exe) https://sourceforge.net/projects/tdm-gcc/

Choose MinGW-w64/TDM64 (32-bit and 64-bit)

Installation Directory:
C:\TDM-GCC-64
Select the type of install: TDM-GCC Recommended, C/C++

d. Download and install msys2 https://sourceforge.net/projects/msys2/

Specify the directory where MSYS2 64bit will be installed:
C:\msys64

e. Set PATH to C Compiler

export PATH=$PATH:/c/TDM-GCC-64/bin

f. install som dev-utils

* pacman -S diffutils
* pacman -S make
* pacman -S tcl
* pacman -S vim

g. run ./configure with the following parameters, alter to your preference...

./configure --build=x86_64 --with-crypto-lib=none --disable-tcl --disable-shared --enable-static=yes --enable-tempstore=yes CFLAGS="-DSQLITE_HAS_CODEC -DSQLCIPHER_CRYPTO_OPENSSL -DSQLITE_ENABLE_LOAD_EXTENSION=1 -DSQLITE_HAVE_ISNAN -DSQLITE_HAVE_USLEEP -DSQLITE_ENABLE_UPDATE_DELETE_LIMIT -DSQLITE_ENABLE_COLUMN_METADATA -DSQLITE_CORE -DSQLITE_ENABLE_FTS3 -DSQLITE_ENABLE_FTS3_PARENTHESIS -DSQLITE_ENABLE_FTS5 -DSQLITE_ENABLE_JSON1 -DSQLITE_ENABLE_RTREE -DSQLITE_ENABLE_STAT2 -DSQLITE_THREADSAFE=1 -DSQLITE_DEFAULT_MEMSTATUS=0 -DSQLITE_DEFAULT_FILE_PERMISSIONS=0666 -DSQLITE_MAX_VARIABLE_NUMBER=250000 -DSQLITE_DEFAULT_FOREIGN_KEYS=1 -I/c/opensslbuild64/include /c/sqlcipher/libeay32.dll -L/c/sqlcipher/ -static-libgcc" LDFLAGS="-leay32"

Should give similar to this output:

    configure: loading site script /etc/config.site
    checking build system type... x86_64-pc-none
    checking host system type... x86_64-pc-none
    checking how to print strings... printf
    checking for gcc... gcc
    checking whether the C compiler works... yes
    checking for C compiler default output file name... a.exe
    checking for suffix of executables... .exe
    checking whether we are cross compiling... no
    checking for suffix of object files... o
    checking whether we are using the GNU C compiler... yes
    checking whether gcc accepts -g... yes
    checking for gcc option to accept ISO C89... none needed
    checking for a sed that does not truncate output... /usr/bin/sed
    checking for grep that handles long lines and -e... /usr/bin/grep
    checking for egrep... /usr/bin/grep -E
    checking for fgrep... /usr/bin/grep -F
    checking for ld used by gcc... C:/TDM-GCC-64/x86_64-w64-mingw32/bin/ld.exe
    checking if the linker (C:/TDM-GCC-64/x86_64-w64-mingw32/bin/ld.exe) is GNU ld... yes
    checking for BSD- or MS-compatible name lister (nm)... /c/TDM-GCC-64/bin/nm
    checking the name lister (/c/TDM-GCC-64/bin/nm) interface... BSD nm
    checking whether ln -s works... no, using cp -pR
    checking the maximum length of command line arguments... 24000
    checking how to convert x86_64-pc-none file names to x86_64-pc-none format... func_convert_file_noop
    checking how to convert x86_64-pc-none file names to toolchain format... func_convert_file_noop
    checking for C:/TDM-GCC-64/x86_64-w64-mingw32/bin/ld.exe option to reload object files... -r
    checking for objdump... objdump
    checking how to recognize dependent libraries... unknown
    checking for dlltool... dlltool
    checking how to associate runtime and link libraries... printf %s\n
    checking for ar... ar
    checking for archiver @FILE support... @
    checking for strip... strip
    checking for ranlib... ranlib
    checking for gawk... gawk
    checking command to parse /c/TDM-GCC-64/bin/nm output from gcc object... ok
    checking for sysroot... no
    checking for a working dd... /usr/bin/dd
    checking how to truncate binary pipes... /usr/bin/dd bs=4096 count=1
    checking for mt... no
    checking if : is a manifest tool... no
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
    checking for dlfcn.h... no
    checking for objdir... .libs
    checking if gcc supports -fno-rtti -fno-exceptions... no
    checking for gcc option to produce PIC... -fPIC -DPIC
    checking if gcc PIC flag -fPIC -DPIC works... no
    checking if gcc static flag -static works... no
    checking if gcc supports -c -o file.o... yes
    checking if gcc supports -c -o file.o... (cached) yes
    checking whether the gcc linker (C:/TDM-GCC-64/x86_64-w64-mingw32/bin/ld.exe) supports shared libraries... yes
    checking dynamic linker characteristics... no
    checking how to hardcode library paths into programs... immediate
    checking whether stripping libraries is possible... yes
    checking if libtool supports shared libraries... no
    checking whether to build shared libraries... no
    checking whether to build static libraries... yes
    checking for a BSD-compatible install... /usr/bin/install -c
    checking for special C compiler options needed for large files... no
    checking for _FILE_OFFSET_BITS value needed for large files... 64
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
    checking for fdatasync... no
    checking for gmtime_r... no
    checking for isnan... yes
    checking for localtime_r... no
    checking for localtime_s... no
    checking for malloc_usable_size... no
    checking for strchrnul... no
    checking for usleep... yes
    checking for utime... yes
    checking for pread... no
    checking for pread64... no
    checking for pwrite... no
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
    checking for library containing readline... no
    checking for library containing tgetent... no
    checking for readline in -lreadline... no
    checking readline.h usability... no
    checking readline.h presence... no
    checking for readline.h... no
    checking for /usr/include/readline.h... no
    checking for /usr/include/readline/readline.h... no
    checking for /usr/local/include/readline.h... no
    checking for /usr/local/include/readline/readline.h... no
    checking for /usr/local/readline/include/readline.h... no
    checking for /usr/local/readline/include/readline/readline.h... no
    checking for /usr/contrib/include/readline.h... no
    checking for /usr/contrib/include/readline/readline.h... no
    checking for /mingw/include/readline.h... no
    checking for /mingw/include/readline/readline.h... no
    checking for library containing fdatasync... no
    checking for library containing dlopen... no
    checking whether to support MEMSYS5... no
    checking whether to support MEMSYS3... no
    configure: creating ./config.status
    config.status: creating Makefile
    config.status: creating sqlcipher.pc
    config.status: creating config.h
    config.status: config.h is unchanged
    config.status: executing libtool commands

h. Run the following commands
* make clean
* make sqlite3.c
* make

Now in order to create the dll we need first to compile NativeDB.c to get NativeDB.o in the sqlite-jdbc project.

To create NativeDB.h:
* mvn compile  (so that org.sqlite.core.NativeDB class is generated)
* javah -classpath target/classes -jni -o lib/inc_win/NativeDB.h org.sqlite.core.NativeDB<br /><br />
Run above command from a command prompt with cd sqlite-jdbc project folder, the file NativeDB.h will be generated in the lib\inc_win-folder.
* copy folder inc_win with content to c:\sqlcipher
* copy file src/main/java/org/sqlite/core/NativeDB.c to c:\sqlcipher
* compile into NativeDB.o: <br />
gcc -I inc_win -c -o NativeDB.o NativeDB.c


j. Build the dll

Copy sqlite3.o and NativeDB.o to c:\sqlcipher\\.libs

content of c:\sqlcipher\\.libs:

* libsqlcipher.a
* libsqlcipher.la
* libsqlcipher.lai
* NativeDB.o
* sqlite3.o

Edit Makefile in Windows section (dll section) last in file, replace %.o with *.o

REAL_LIBOBJ = $(LIBOBJ:%.lo=.libs/*.o) <--- REPLACE HERE

Now build the dll:

* make dll

Outputs:

echo 'EXPORTS' >sqlite3.def
nm .libs/*.o | grep ' T ' | grep ' _sqlite3_' \
        | sed 's/^.* _//' >>sqlite3.def
gcc -DSQLITE_HAS_CODEC -DSQLCIPHER_CRYPTO_OPENSSL -DSQLITE_HAVE_ISNAN -DSQLITE_HAVE_USLEEP -DSQLITE_CORE -DSQLITE_THREADSAFE=1 -DSQLITE_DEFAULT_MEMSTATUS=0 -DSQLITE_DEFAULT_FILE_PERMISSIONS=0666 -DSQLITE_MAX_VARIABLE_NUMBER=250000 -DSQLITE_DEFAULT_FOREIGN_KEYS=1 -I/c/opensslbuild64/include /c/sqlcipher/libeay32.dll -L/c/sqlcipher/ -static-libgcc -DSQLITE_OS_WIN=1 -I. -I/c/sqlcipher/src -I/c/sqlcipher/ext/rtree -I/c/sqlcipher/ext/icu -I/c/sqlcipher/ext/fts3 -I/c/sqlcipher/ext/async -I/c/sqlcipher/ext/session -D_HAVE_SQLITE_CONFIG_H -DBUILD_sqlite -DNDEBUG  -DSQLITE_THREADSAFE=1 -DSQLITE_ENABLE_LOAD_EXTENSION=1 -DSQLITE_ENABLE_UPDATE_DELETE_LIMIT -DSQLITE_ENABLE_COLUMN_METADATA -DSQLITE_ENABLE_FTS3 -DSQLITE_ENABLE_FTS3_PARENTHESIS -DSQLITE_ENABLE_FTS5 -DSQLITE_ENABLE_JSON1 -DSQLITE_ENABLE_RTREE -DSQLITE_ENABLE_STAT2  -shared -o sqlite3.dll sqlite3.def \
        -Wl,"--strip-all" .libs/*.o



sqlite3.dll is now created in c:\sqlcipher, rename to sqlitejdbc.dll and copy over to sqlite-jdbc\src\main\resources\org\sqlite\native\Windows\x86_64

That's all folks

-jbilander