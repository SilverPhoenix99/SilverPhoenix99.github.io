---
title: "Firebird Embedded in SQuirreL SQL in Windows"
date:   2009-02-16 20:29:00 GMT
tags:   firebird jaybird sql squirrel
---
After 5 days I have finally been able to setup Firebird Embedded in SQuirreL SQL. And I call myself a programmer... Shame on me!

Since I haven't found any concise tutorial on how this can be achieved (for newbies in Firebird, at least), I'll write here on how to do it.

You will need three things:
- [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)
- [Jaybird](http://www.firebirdsql.org/index.php?op=files&amp;id=jaybird) (a.k.a., Firebird JDBC)
- [Firebird Embedded](http://www.firebirdsql.org/index.php?op=files&amp;id=engine) "edition" (Follow the current release link, then select an "embed" installation)

Unpack both Firebird zips into the same folder.

From the Jaybird archive, you will need to extract the following:
- jaybird-full-*.jar
- jaybird21.dll

From the Firebird embedded archive, you will need to extract:
- fbembed.dll
- ib_util.dll
- icudt30.dll
- icuin30.dll
- icuuc30.dll
- firebird.conf (if you wish to provide custom configuration)
- firebird.msg
- intl\\* (everything inside the folder)

After the libraries and SQuirreL SQL are installed, you have to setup a connection and some paths for the libraries.

Before opening SQuirreL, go to the directory where it is installed. In there you will find the main runnable batch file `squirrel-sql.bat`. Edit the file and insert the red colored text in the last line:

<pre><code>start "SQuirreL SQL Client" /B "%LOCAL_JAVA%w" <span style="color: red; font-weight: bold;">-Djava.library.path=/path/to/jaybird_dll</span> -Xmx256m -cp %TMP_CP% net.sourceforge.squirrel_sql.client.Main %TMP_PARMS%</code></pre>

Replace <span style="color: red; font-weight: bold;"><path dll="" to=""></path></span> with the path where the file jaybird21.dll is installed. It will typically be in the same folder as jaybird-full-*.jar.<br />

Open SQuirreL and choose "Firebird JayBird" from drivers. On "Extra Class Path", point to the `jaybird-full.jar` that was unpacked from the archive you downloaded.

Since it is the embedded version, make sure your database string is:

```
jdbc:firebirdsql:embedded:<database_path>
```
