---
layout :post
category :lessons
tagline : "linux kernel 4.6.2 升级导致导致的vmware启动失败问题"
tags : [linux,kernel-4.6.2,vmware]
---
{% include JB/setup %}
于linux内核升级导致的VMware启动失败的问题，本人使用4.6.0-kali1-amd64,之前的内核版本为4.4,前一段时间升级kernel到4.6.2,导致vm启动失败，报错如下：


> (vmware-modconfig:26736): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“adwaita”，
/usr/share/themes/Adwaita/gtk-2.0/main.rc:728: error: unexpected identifier `direction', expected character `}'

(vmware-modconfig:26736): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“adwaita”，
Gtk-Message: Failed to load module "canberra-gtk-module": libcanberra-gtk-module.so: 无法打开共享对象文件: 没有那个文件或目录
Stopping VMware services:
   VMware Authentication Daemon                                        done
   VM communication interface socket family                            done
   Virtual machine communication interface                             done
   Virtual machine monitor                                             done
   Blocking file system                                                done
make: Entering directory '/tmp/modconfig-RcZ2cU/vmmon-only'
Using kernel build system.
/usr/bin/make -C /lib/modules/4.6.0-kali1-amd64/build/include/.. SUBDIRS=$PWD SRCROOT=$PWD/. \
  MODULEBUILDDIR= modules
make[1]: Entering directory '/usr/src/linux-headers-4.6.0-kali1-amd64'
  CC [M]  /tmp/modconfig-RcZ2cU/vmmon-only/linux/driver.o
  CC [M]  /tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.o
  CC [M]  /tmp/modconfig-RcZ2cU/vmmon-only/linux/driverLog.o
  CC [M]  /tmp/modconfig-RcZ2cU/vmmon-only/common/apic.o
  CC [M]  /tmp/modconfig-RcZ2cU/vmmon-only/common/memtrack.o
  CC [M]  /tmp/modconfig-RcZ2cU/vmmon-only/common/vmx86.o
  CC [M]  /tmp/modconfig-RcZ2cU/vmmon-only/common/cpuid.o
/tmp/modconfig-RcZ2cU/vmmon-only/linux/driver.c:1283:1: warning: always_inline function might not be inlinable [-Wattributes]
 LinuxDriverSyncReadTSCs(uint64 *delta) // OUT: TSC max - TSC min
 ^
In file included from /usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/processor.h:15:0,
                 from /usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/cpufeature.h:4,
                 from /usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/thread_info.h:52,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/thread_info.h:54,
                 from /usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/preempt.h:6,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/preempt.h:59,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/spinlock.h:50,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/mmzone.h:7,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/gfp.h:5,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/mm.h:9,
                 from /tmp/modconfig-RcZ2cU/vmmon-only/./include/compat_page.h:23,
                 from /tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c:32:
/tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c: In function ‘HostIFGetUserPages’:
/usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/current.h:17:17: warning: passing argument 1 of ‘get_user_pages’ makes integer from pointer without a cast [-Wint-conversion]
 #define current get_current()
                 ^
/tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c:1165:28: note: in expansion of macro ‘current’
    retval = get_user_pages(current, current->mm, (unsigned long)uvAddr,
                            ^
In file included from /tmp/modconfig-RcZ2cU/vmmon-only/./include/compat_page.h:23:0,
                 from /tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c:32:
/usr/src/linux-headers-4.6.0-kali1-common/include/linux/mm.h:1288:6: note: expected ‘long unsigned int’ but argument is of type ‘struct task_struct *’
 long get_user_pages(unsigned long start, unsigned long nr_pages,
      ^
In file included from /usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/processor.h:15:0,
                 from /usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/cpufeature.h:4,
                 from /usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/thread_info.h:52,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/thread_info.h:54,
                 from /usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/preempt.h:6,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/preempt.h:59,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/spinlock.h:50,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/mmzone.h:7,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/gfp.h:5,
                 from /usr/src/linux-headers-4.6.0-kali1-common/include/linux/mm.h:9,
                 from /tmp/modconfig-RcZ2cU/vmmon-only/./include/compat_page.h:23,
                 from /tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c:32:
/usr/src/linux-headers-4.6.0-kali1-common/arch/x86/include/asm/current.h:17:17: warning: passing argument 2 of ‘get_user_pages’ makes integer from pointer without a cast [-Wint-conversion]
 #define current get_current()
                 ^
/tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c:1165:37: note: in expansion of macro ‘current’
    retval = get_user_pages(current, current->mm, (unsigned long)uvAddr,
                                     ^
In file included from /tmp/modconfig-RcZ2cU/vmmon-only/./include/compat_page.h:23:0,
                 from /tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c:32:
/usr/src/linux-headers-4.6.0-kali1-common/include/linux/mm.h:1288:6: note: expected ‘long unsigned int’ but argument is of type ‘struct mm_struct *’
 long get_user_pages(unsigned long start, unsigned long nr_pages,
      ^
/tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c:1165:13: error: too many arguments to function ‘get_user_pages’
    retval = get_user_pages(current, current->mm, (unsigned long)uvAddr,
             ^
In file included from /tmp/modconfig-RcZ2cU/vmmon-only/./include/compat_page.h:23:0,
                 from /tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.c:32:
/usr/src/linux-headers-4.6.0-kali1-common/include/linux/mm.h:1288:6: note: declared here
 long get_user_pages(unsigned long start, unsigned long nr_pages,
      ^
/usr/src/linux-headers-4.6.0-kali1-common/scripts/Makefile.build:296: recipe for target '/tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.o' failed
make[4]: *** [/tmp/modconfig-RcZ2cU/vmmon-only/linux/hostif.o] Error 1
make[4]: *** 正在等待未完成的任务....
/usr/src/linux-headers-4.6.0-kali1-common/Makefile:1446: recipe for target '_module_/tmp/modconfig-RcZ2cU/vmmon-only' failed
make[3]: *** [_module_/tmp/modconfig-RcZ2cU/vmmon-only] Error 2
Makefile:146: recipe for target 'sub-make' failed
make[2]: *** [sub-make] Error 2
Makefile:8: recipe for target 'all' failed
make[1]: *** [all] Error 2
make[1]: Leaving directory '/usr/src/linux-headers-4.6.0-kali1-amd64'
Makefile:120: recipe for target 'vmmon.ko' failed
make: *** [vmmon.ko] Error 2
make: Leaving directory '/tmp/modconfig-RcZ2cU/vmmon-only'
Starting VMware services:
   Virtual machine monitor                                            failed
   Virtual machine communication interface                             done
   VM communication interface socket family                            done
   Blocking file system                                                done
   Virtual ethernet                                                   failed
   VMware Authentication Daemon                                        done


#### 后来找到方法如下，实际为打补丁，经测试有效：

1. cd /usr/lib/vmware/modules/source

2. tar -xf vmnet.tar
 tar -xf vmmon.tar

3. mv vmnet.tar vmnet.tar.bak
mv vmmon.tar vmmon.tar.bak
备份

4. vim ./vmmon-only/linux/hostif.c
vim ./vmnet-only/userif.c
分别修改 get_user_pages 为 get_user_pages_remote

5. tar -uf vmnet.tar vmnet-only
tar -uf vmmon.tar vmmon-only
打包修改过的文件

6. vmware-modconfig --console --install-all 
重新编译内核模块


###over
