# 编译问题

## 常见错误
```
chess.c:11:10: fatal error: 'malloc.h' file not found
#include <malloc.h>
         ^
```


```
h.o -MD -MP -MF .deps/binaryattach.Tpo -c -o binaryattach.o binaryattach.c
binaryattach.c:7: error: conflicting types for ‘versionsort’
/usr/include/dirent.h:340: note: previous declaration of ‘versionsort’ was here
```

修复 https://github.com/lytsing/ytht/commit/2620ad1f837aade82b2dd04f87eb80bb3afde7a7

nju09编译错误：

```
bbsscanreg.c:2: error: conflicting types for ‘versionsort’
/usr/include/dirent.h:340: note: previous declaration of ‘versionsort’ was here
```

