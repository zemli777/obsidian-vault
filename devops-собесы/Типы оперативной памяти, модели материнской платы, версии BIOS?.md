- Модель процессора `cat /proc/cpuinfo`

```
model name	: Intel(R) Xeon(R) CPU E5-2620 0 @ 2.00GHz
model		: 45
```

Та же модель процессора через `lscpu`

```
Model name:          Intel(R) Xeon(R) CPU E5-2620 0 @ 2.00GHz
```

Ядра физические и логические

```
vm13 : ~ [0] # grep "cpu cores" /proc/cpuinfo |sort -u |cut -d":" -f2
 4
vm13 : ~ [0] # grep -c "processor" /proc/cpuinfo
4
```

Поддерживаемые инструкции /proc/cpuinfo и lscpu

Но возможно стоит также в спецификацию посмотреть

```
Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss ht syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon rep_good nopl xtopology pni pclmulqdq vmx ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt tsc_deadline_timer aes xsave avx hypervisor lahf_lm kaiser tpr_shadow vnmi flexpriority ept vpid tsc_adjust xsaveopt arat
```

режимы работы процессора

`find / -name scaling_governor`

`find / -name scaling_max_freq`

`cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor`

- **powersave** — режим энергосбережения, ядро будет работать на пониженных частотах
    
- **ondemand** — режим зависящей от текущей нагрузки на ядро
    
- **performance** — режим максимальной мощности, выставляет максимально возможную частоту
    
- Физические ядра - это число физических ядер, реальных аппаратных компонентов.
    

Логические ядра - это число физических ядер, умноженное на количество потоков, которые могут выполняться на каждом ядре с помощью гиперпотока. например, мой 4-ядерный процессор запускает два потока на ядро, поэтому у меня есть 8 логических процессоров.

Узнать сколько ядер доступно можно командой:

```
dmidecode -t processor | grep "Core Enabled:"
Core Enabled: 6
Core Enabled: 6
```

Видим, что на данной системе находится 12 физических ядер (6+6). Соответственно, нормальный показатель LA должен быть менее 12. Однако, на процессорах Intel используется технология [Hyper-Threading](http://www.intel.ru/content/www/ru/ru/architecture-and-technology/hyper-threading/hyper-threading-technology.html), которая делит одно физическое ядро на два логических.

```
dmidecode -t processor | grep "Thread Count:"
Thread Count: 12
Thread Count: 12
```

Соответственно, в данном случае в системе может быть одновременно 24 виртуальных процессора (потока).

Технология Turbo Boost позволяет процессору «разгоняться» и работать на частоте выше заявленной (т.е. выше 100%, выше единицы). Какой показатель LA считать нормальным в данном случае является предметом споров.