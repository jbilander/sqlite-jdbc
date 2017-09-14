Fork Notes
==================
This is a fork of a [SQLite JDBC driver](https://github.com/xerial/sqlite-jdbc) that has been modified to work with
[SQLCipher](https://github.com/sqlcipher/sqlcipher), a version of SQLite modified to support encryption.
This version is adapted to Java 9 and the use of the new module feature (project jigsaw).
The only native binaries included here currently are for 64-bit Windows (x86_64) and 64-bit MacOSX (x86_64), 64-bit Linux will be added soon.
At least that is the plan. A lot of files has been removed to reduce the repo-clutter in this version. 
See changelist for a complete list of changes further down.

To create/open an encrypted database, try something like:
```
    private SQLiteConnectionPoolDataSource dataSource = new SQLiteConnectionPoolDataSource();
    private MiniConnectionPoolManager miniConnectionPoolManager = MiniConnectionPoolManager.getInstance();
    
    ConnectionPoolHelper() {
    
        dataSource.setUrl(Util.JDBC_URL);
        dataSource.getConfig().setKey("passphrase"); // <-- SET YOUR PASSPHRASE HERE!
        miniConnectionPoolManager.setConnectionPoolDataSource(dataSource);
    }
```

To include the jar in your project through maven:


        <dependency>
            <groupId>org.xerial</groupId>
            <artifactId>sqlite-jdbc-sqlcipher</artifactId>
            <version>3.20.1-SNAPSHOT</version>
        </dependency>

To use the module in your project add this to your `module-info.java` <br />

        requires sqlite.jdbc;
<br />

This is the only sqlcipher-pragma implemented in this version:
- key <br /><br />
Key used for encrypting/decrypting database, as string "passphrase"
which is converted to a key using PBKDF2 key derivation, or
a 64 character hex string i.e. "x'2DD29CA851E7B56E4697B0E1F08507293D761A05CE4D1B628663F411A8086D99'",
which will be converted directly to 32 bytes (256 bits) of key data.

To use the hex-string as key in Java:

    final String KEY = "\"x'2DD29CA851E7B56E4697B0E1F08507293D761A05CE4D1B628663F411A8086D99'\"";
    dataSource.getConfig().setKey(KEY);

To rekey from inside application, database must first be unlocked with PRAGMA key, then execute a statement with PRAGMA rekey like this:

    stmnt.executeUpdate("PRAGMA rekey = 'new_passphrase'");

Other PRAGMAS and syntax you can find here [sqlcipher-api](https://www.zetetic.net/sqlcipher/sqlcipher-api/)

The included 64-bit windows ddl is currently compiled with static linking `--enable-static=yes` and `--enable-tempstore=yes` and these CFLAGS set because that is how I prefer it:

```
-DSQLITE_HAS_CODEC 
-DSQLCIPHER_CRYPTO_OPENSSL 
-DSQLITE_ENABLE_LOAD_EXTENSION=1 
-DSQLITE_HAVE_ISNAN 
-DSQLITE_HAVE_USLEEP 
-DSQLITE_ENABLE_UPDATE_DELETE_LIMIT 
-DSQLITE_ENABLE_COLUMN_METADATA 
-DSQLITE_CORE 
-DSQLITE_ENABLE_FTS3 
-DSQLITE_ENABLE_FTS3_PARENTHESIS 
-DSQLITE_ENABLE_FTS5 
-DSQLITE_ENABLE_JSON1 
-DSQLITE_ENABLE_RTREE 
-DSQLITE_ENABLE_STAT2 
-DSQLITE_THREADSAFE=1 
-DSQLITE_DEFAULT_MEMSTATUS=0 
-DSQLITE_DEFAULT_FILE_PERMISSIONS=0666 
-DSQLITE_MAX_VARIABLE_NUMBER=250000 
-DSQLITE_DEFAULT_FOREIGN_KEYS=1
```

If your preferences are otherwise then just go ahead and build your own dll and replace the current one.

Here's my tutorial on how to build a dll for 64-bit Windows x86_64 with sqlcipher support:
https://github.com/jbilander/sqlite-jdbc/blob/master/README_BUILD_DLL.md

Here's my tutorial on how to build a jnilib for 64-bit MacOSX x86_64 with sqlcipher support:
https://github.com/jbilander/sqlite-jdbc/blob/master/README_BUILD_JNILIB.md

Windows: If you want to fiddle with a database from the cmd then use the shell/Windows/x86_64 sqlcipher.exe with the libeay32.dll, copy both files to appropriate location.

MacOSX: If you want to fiddle with a database from the cmd then use the shell/Mac/x86_64/sqlcipher, copy file to appropriate location.

    sqlcipher test.db
    sqlite>PRAGMA key = 'passphrase';
    sqlite>create table people (id integer primary key autoincrement not null, name text not null);
    sqlite>insert into people (name) values ('alice'),('bob'),('charlie');
    sqlite>select * from people;
        1|alice
        2|bob
        3|charlie
    sqlite>.exit

Now that we have a database with some values in it let's exit and try to access it without a passphrase.

    sqlcipher test.db
    sqlite>select * from people;
    Error: file is encrypted or is not a database
    sqlite>.exit

And again with the passphrase.

    sqlcipher test.db
    sqlite>PRAGMA key = 'passphrase';
    sqlite>select * from people;
        1|alice
        2|bob
        3|charlie
    sqlite>.exit

-jbilander

Changelist:
==================

Files added: <br />
```
.idea/compiler.xml
.idea/encodings.xml
.idea/misc.xml
.idea/modules.xml
.idea/vcs.xml
sqlite-jdbc-sqlcipher.iml
shell/Windows/x86_64/libeay32.dll
shell/Windows/x86_64/sqlcipher.exe
shell/Mac/x86_64/sqlcipher
src/main/java/module-info.java
lib/inc_win/NativeDB.h
lib/inc_mac/NativeDB.h
README_BUILD_DLL.md
README_BUILD_JNILIB.md
```
Files modified: <br />
```
.gitignore
README.md
pom.xml
src/main/java/org/sqlite/core/NativeDB.c
src/main/java/org/sqlite/SQLiteConfig.java
```
Files replaced: <br />
```
src/main/resources/org/sqlite/native/Windows/x86_64/sqlitejdbc.dll
src/main/resources/org/sqlite/native/Mac/x86_64/libsqlitejdbc.jnilib
```
Files removed: <br />
```
.hgignore
.settings/org.eclipse.jdt.core.prefs
.settings/org.eclipse.ltk.core.refactoring.prefs
.settings/org.eclipse.wst.validation.prefs
.travis.yml
CHANGELOG
README_BUILD.md
SQLiteJDBC.wiki
Usage.md
amalgamation_version.sh
archive/nestedvm-2007-06-30.tgz
archive/nestedvm-2009-08-09.tgz
archive/regex3.8a.tar.gz
demo/AppletDemo.jar
demo/Sample.java
demo/applet-demo.html
docker/Dockerfile.alpine-linux_x86_64
docker/Dockerfile.linux_x86
docker/Dockerfile.linux_x86_64
docker/dockcross-android-arm
docker/dockcross-arm64
docker/dockcross-armv5
docker/dockcross-armv6
docker/dockcross-armv7
docker/dockcross-ppc64
docker/dockcross-windows-x64
docker/dockcross-windows-x86
maven-eclipse.xml
scripts/travis-deploy.sh
settings.xml
src/main/java/org/sqlite/date/package-info.java
src/main/resources/java.sql.Driver
src/main/resources/org/sqlite/native/FreeBSD/x86_64/libsqlitejdbc.so
src/main/resources/org/sqlite/native/Linux/aarch64/libsqlitejdbc.so
src/main/resources/org/sqlite/native/Linux/android-arm/libsqlitejdbc.so
src/main/resources/org/sqlite/native/Linux/arm/libsqlitejdbc.so
src/main/resources/org/sqlite/native/Linux/armv6/libsqlitejdbc.so
src/main/resources/org/sqlite/native/Linux/armv7/libsqlitejdbc.so
src/main/resources/org/sqlite/native/Linux/ppc64/libsqlitejdbc.so
src/main/resources/org/sqlite/native/Linux/x86/libsqlitejdbc.so
src/main/resources/org/sqlite/native/Windows/x86/sqlitejdbc.dll
src/test/java/org/sqlite/AllTests.java
src/test/java/org/sqlite/BackupTest.java
src/test/java/org/sqlite/BusyHandlerTest.java
src/test/java/org/sqlite/ConnectionTest.java
src/test/java/org/sqlite/DBMetaDataTest.java
src/test/java/org/sqlite/ErrorMessageTest.java
src/test/java/org/sqlite/ExtendedCommandTest.java
src/test/java/org/sqlite/ExtensionTest.java
src/test/java/org/sqlite/FetchSizeTest.java
src/test/java/org/sqlite/InsertQueryTest.java
src/test/java/org/sqlite/JDBCTest.java
src/test/java/org/sqlite/JSON1Test.java
src/test/java/org/sqlite/PrepStmtTest.java
src/test/java/org/sqlite/ProgressHandlerTest.java
src/test/java/org/sqlite/QueryTest.java
src/test/java/org/sqlite/RSMetaDataTest.java
src/test/java/org/sqlite/ReadUncommittedTest.java
src/test/java/org/sqlite/ResultSetTest.java
src/test/java/org/sqlite/SQLiteConfigTest.java
src/test/java/org/sqlite/SQLiteConnectionPoolDataSourceTest.java
src/test/java/org/sqlite/SQLiteDataSourceTest.java
src/test/java/org/sqlite/SQLiteJDBCLoaderTest.java
src/test/java/org/sqlite/SavepointTest.java
src/test/java/org/sqlite/StatementTest.java
src/test/java/org/sqlite/TransactionTest.java
src/test/java/org/sqlite/UDFTest.java
src/test/java/org/sqlite/sample.db
src/test/java/org/sqlite/testdb.jar
src/test/java/org/sqlite/util/OSInfoTest.java
```