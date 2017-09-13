# GCC4 编译问题

## 常见错误

libythtbbs/binaryattach.c:

```
h.o -MD -MP -MF .deps/binaryattach.Tpo -c -o binaryattach.o binaryattach.c
binaryattach.c:7: error: conflicting types for ‘versionsort’
/usr/include/dirent.h:340: note: previous declaration of ‘versionsort’ was here
```


nju09编译错误：

```
bbsscanreg.c:2: error: conflicting types for ‘versionsort’
/usr/include/dirent.h:340: note: previous declaration of ‘versionsort’ was here
```

修复方法，`gcc4.diff`:

```
diff --git a/libythtbbs/binaryattach.c b/libythtbbs/binaryattach.c
index bc1f609..484f25d 100644
--- a/libythtbbs/binaryattach.c
+++ b/libythtbbs/binaryattach.c
@@ -4,7 +4,10 @@
 #include <netinet/in.h>
 #include <stdio.h>
 #include "ythtbbs.h"
-int versionsort(const void *a, const void *b);
+
+#ifndef HAVE_VERSIONSORT
+extern int versionsort(const void *a, const void *b);
+#endif

 static int
 appendonebinaryattach(char *filename, char *attachname, char *attachfname)
diff --git a/nju09/bbsscanreg.c b/nju09/bbsscanreg.c
index 937f623..9ccc969 100644
--- a/nju09/bbsscanreg.c
+++ b/nju09/bbsscanreg.c
@@ -1,7 +1,10 @@
 #include <dirent.h>
-int versionsort(const void *a, const void *b);
 #include "bbslib.h"

+#ifndef HAVE_VERSIONSORT
+extern int versionsort(const void *a, const void *b);
+#endif
+
 const static char *field[] =
     { "usernum", "userid", "realname", "dept", "addr", "phone", "assoc",
        "rereg",

```


见： https://github.com/lytsing/ytht/commit/2620ad1f837aade82b2dd04f87eb80bb3afde7a7

