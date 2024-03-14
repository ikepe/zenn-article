---
title: "dkms install px4_drv/0.2.1でエラーが発生した件"
emoji: "🎥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["proxmox","録画サーバ"]
published: true
---

Proxmox VEで録画鯖を作成する際に詰まった箇所があったので覚え書き

https://blog.yakijake.net/lxc-dtv-px4_drv-1/

```
sudo dkms install px4_drv/0.2.1
```
を実行すると
```shell
Sign command: /lib/modules/6.5.13-1-pve/build/scripts/sign-file
Signing key: /var/lib/dkms/mok.key
Public certificate (MOK): /var/lib/dkms/mok.pub
Certificate or key are missing, generating self signed certificate for MOK...

Building module:
Cleaning build area...
cd ./driver; make KVER=6.5.13-1-pve px4_drv.ko...(bad exit status: 2)
Error! Bad return status for module build on kernel: 6.5.13-1-pve (x86_64)
Consult /var/lib/dkms/px4_drv/0.2.1/build/make.log for more information.
```

とエラーが。make.logを見ると
```log
DKMS make.log for px4_drv-0.2.1 for kernel 6.5.13-1-pve (x86_64)
Thu Mar 14 08:21:08 JST 2024
'revision.h' was updated.
make[1]: Entering directory '/usr/src/linux-headers-6.5.13-1-pve'
  CC [M]  /var/lib/dkms/px4_drv/0.2.1/build/driver/driver_module.o
  CC [M]  /var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.o
In file included from ./include/linux/linkage.h:7,
                 from ./include/linux/preempt.h:10,
                 from ./include/linux/spinlock.h:56,
                 from ./include/linux/kref.h:16,
                 from /var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.h:13,
                 from /var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.c:9:
/var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.c: In function 'ptx_chrdev_context_create':
./include/linux/export.h:29:22: error: passing argument 1 of 'class_create' from incompatible pointe>
   29 | #define THIS_MODULE (&__this_module)
      |                     ~^~~~~~~~~~~~~~~
      |                      |
      |                      struct module *
/var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.c:573:35: note: in expansion of macro 'THIS_MODU>
  573 |         ctx->class = class_create(THIS_MODULE, name);
      |                                   ^~~~~~~~~~~
In file included from ./include/linux/device.h:31,
                 from ./include/linux/cdev.h:8,
                 from /var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.h:16:
./include/linux/device/class.h:230:54: note: expected 'const char *' but argument is of type 'struct>
  230 | struct class * __must_check class_create(const char *name);
      |                                          ~~~~~~~~~~~~^~~~
/var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.c:573:22: error: too many arguments to function >
  573 |         ctx->class = class_create(THIS_MODULE, name);
      |                      ^~~~~~~~~~~~
./include/linux/device/class.h:230:29: note: declared here
  230 | struct class * __must_check class_create(const char *name);
      |                             ^~~~~~~~~~~~
cc1: some warnings being treated as errors
make[3]: *** [scripts/Makefile.build:251: /var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.o] Err>
make[2]: *** [/usr/src/linux-headers-6.5.13-1-pve/Makefile:2039: /var/lib/dkms/px4_drv/0.2.1/build/d>
make[1]: *** [Makefile:234: __sub-make] Error 2
make[1]: Leaving directory '/usr/src/linux-headers-6.5.13-1-pve'
make: *** [Makefile:19: px4_drv.ko] Error 2
```


class_create関数はTHIS_MODULEを引数として受け取らず、最初の引数としてconst char *型の名前を受け取るようになっているので修正。

しかし/var/lib/dkms/px4_drv/0.2.1/build/driver/ptx_chrdev.cを編集しても、dkms installコマンドを実行することで上書きされてしまうので、/usr/src/px4_drv-0.2.1/driver/ptx_chrdev.cを修正します

```diff c:/usr/src/px4_drv-0.2.1/driver/ptx_chrdev.c
- ctx->class = class_create(THIS_MODULE, name);
+ ctx->class = class_create(name);
```

これでインストールが完了しました。