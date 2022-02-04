# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

(1)

```
sudo nano /etc/systemd/system/node_exporter.service
root@vagrant:~# systemctl daemon-reload
root@vagrant:~# systemctl enable node_exporter
root@vagrant:~# systemctl start node_exporter
root@vagrant:~# systemctl enable --now node_exporter
root@vagrant:~# systemctl status node_exporter
● node_exporter.service - Node Exporter Service
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-01-24 14:26:33 UTC; 1min 22s ago
   Main PID: 1460 (node_exporter)
      Tasks: 4 (limit: 1071)
     Memory: 2.4M
     CGroup: /system.slice/node_exporter.service
             └─1460 /usr/local/bin/node_exporter

Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:115 level=info collector=thermal_zone
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:115 level=info collector=time
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:115 level=info collector=timex
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:115 level=info collector=udp_queues
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:115 level=info collector=uname
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:115 level=info collector=vmstat
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:115 level=info collector=xfs
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:115 level=info collector=zfs
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.595Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
Jan 24 14:26:33 vagrant node_exporter[1460]: ts=2022-01-24T14:26:33.596Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
root@vagrant:~# ss -pnltu | grep 9100
tcp     LISTEN   0        4096                    *:9100                *:*      users:(("node_exporter",pid=1460,fd=3))
root@vagrant:~# ps -e |grep node_exporter
   1460 ?        00:00:00 node_exporter
root@vagrant:~# systemctl stop node_exporter
root@vagrant:~# ps -e |grep node_exporter
root@vagrant:~# systemctl start node_exporter
root@vagrant:~# ps -e |grep node_exporter
   2387 ?        00:00:00 node_exporter
```

Прописан конфигурационный файл - /etc/systemd/system/node_exporter.service :

```
[Unit]
Description=Node Exporter Service
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Перменнная окружения:

```
root@vagrant:/# ss -pnltu | grep 9100
tcp     LISTEN   0        4096                    *:9100                *:*      users:(("node_exporter",pid=1751,fd=3))
root@vagrant:/# cat /proc/1751/environ
LANG=en_US.UTF-8PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/binHOME=/home/nodeusrLOGNAME=nodeusrUSER=nodeusrINVOCATION_ID=3e9068b3beb141ef8acaec481c4fbf73JOURNAL_STREAM=9:46043root@vagrant:/#
```



(2)

CPU:

`curl http://localhost:9100/metrics | grep node_ | grep cpu`

```
# TYPE node_cpu_guest_seconds_total counter
node_cpu_guest_seconds_total{cpu="0",mode="nice"} 0
node_cpu_guest_seconds_total{cpu="0",mode="user"} 0
node_cpu_guest_seconds_total{cpu="1",mode="nice"} 0
node_cpu_guest_seconds_total{cpu="1",mode="user"} 0
# HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
# TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 20136.19
node_cpu_seconds_total{cpu="0",mode="iowait"} 13.79
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0.23
node_cpu_seconds_total{cpu="0",mode="softirq"} 3.48
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 39.89
node_cpu_seconds_total{cpu="0",mode="user"} 34.6
node_cpu_seconds_total{cpu="1",mode="idle"} 20133.64
node_cpu_seconds_total{cpu="1",mode="iowait"} 13.02
node_cpu_seconds_total{cpu="1",mode="irq"} 0
node_cpu_seconds_total{cpu="1",mode="nice"} 0.08
node_cpu_seconds_total{cpu="1",mode="softirq"} 4.12
node_cpu_seconds_total{cpu="1",mode="steal"} 0
node_cpu_seconds_total{cpu="1",mode="system"} 37.64
node_cpu_seconds_total{cpu="1",mode="user"} 32.5
```

Memory:

```
# TYPE node_memory_MemAvailable_bytes gauge
node_memory_MemAvailable_bytes 7.18544896e+08
# TYPE node_memory_MemFree_bytes gauge
node_memory_MemFree_bytes 2.29728256e+08
```

Disk:

```
# TYPE node_disk_io_time_seconds_total counter
node_disk_io_time_seconds_total{device="dm-0"} 59.648
node_disk_io_time_seconds_total{device="sda"} 59.896
# HELP node_disk_read_time_seconds_total The total number of seconds spent by all reads.
# TYPE node_disk_read_time_seconds_total counter
node_disk_read_time_seconds_total{device="dm-0"} 26.964000000000002
node_disk_read_time_seconds_total{device="sda"} 18.655
# TYPE node_disk_read_bytes_total counter
node_disk_read_bytes_total{device="dm-0"} 3.86343936e+08
node_disk_read_bytes_total{device="sda"} 3.95819008e+08
# TYPE node_disk_write_time_seconds_total counter
node_disk_write_time_seconds_total{device="dm-0"} 118.372
node_disk_write_time_seconds_total{device="sda"} 113.619
```

Network:

```
# TYPE node_network_receive_errs_total counter
node_network_receive_errs_total{device="eth0"} 0
node_network_receive_errs_total{device="lo"} 0
# TYPE node_network_receive_bytes_total counter
node_network_receive_bytes_total{device="eth0"} 1.1066699e+07
node_network_receive_bytes_total{device="lo"} 702148
# TYPE node_network_transmit_bytes_total counter
100 61254    0 61254    0     0  3987k      0 --:--:-- --:--:-- --:--:-- 3987k
node_network_transmit_bytes_total{device="eth0"} 652794
node_network_transmit_bytes_total{device="lo"} 765678
# TYPE node_network_transmit_errs_total counter
node_network_transmit_errs_total{device="eth0"} 0
node_network_transmit_errs_total{device="lo"} 0
```

(3)

![2.jpg](./assets/2.jpg)

(4)

Да, ОС осознает что загружена в системе виртуализации:

```
root@vagrant:~# dmesg |grep -i virtual
[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006
[    0.003730] CPU MTRRs all blank - virtualized system.
[    0.106118] Booting paravirtualized kernel on KVM
[    3.116593] systemd[1]: Detected virtualization oracle.
```

(5)

Лимит на количество открытых дескрипторов.

```
root@vagrant:~# /sbin/sysctl -n fs.nr_open
1048576
```

-n        the maximum number of open file descriptors

(6)

В первом терминале:

```
root@vagrant:~# unshare -f --pid --mount-proc sleep 5m
```

Во втором терминале:

```
root@vagrant:~# ps -e | grep sleep                                           
   1559 pts/0    00:00:00 sleep                                              
root@vagrant:~# nsenter --target 1559 --mount --uts --ipc --net --pid ps aux   
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND   
root           1  0.0  0.0   5476   596 pts/0    S+   16:39   0:00 sleep 5m  
root           2  0.0  0.3   8892  3364 pts/1    R+   16:41   0:00 ps aux    
root@vagrant:~#                                                              
```

(7)

Команда :(){ :|:& };: -  является логической бомбой. Она оперирует определением функции с именем ‘:‘, которая вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, пока система не зависнет.

механизм защиты:

```
 dmesg | grep fork
[ 3100.953725] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-1.scope
```

```
limit -u	the maximum number of user processes - настраивает количество процессов на пользователя
```

значения по умолчанию:

```
root@vagrant:~# ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 3571
max locked memory       (kbytes, -l) 65536
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 3571
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```
