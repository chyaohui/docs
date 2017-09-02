### 命令行运行pktgen-dpdk
```bash
$ cd pktgen
$ ./app/app/x86_64-native-linuxapp-gcc/pktgen -c 0xf -n 1 \
        --proc-type auto --socket-mem 1024 --huge-dir /mnt/huge \
        --file-prefix pg -- -P -T -m '1.0,2.1,3.2'
```
