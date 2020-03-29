---
title: 리눅스 성능 도구
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)를 사용 할 때 발생하는 일반적인 문제를 해결하는 방법을 알아봅니다.
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925606"
---
# <a name="linux-performance-troubleshooting"></a>리눅스 성능 문제 해결

Linux 컴퓨터에서 리소스 소모는 일반적인 문제이며 다양한 증상을 통해 나타날 수 있습니다. 이 문서에서는 이러한 문제를 진단하는 데 사용할 수 있는 도구에 대한 개요를 제공합니다.

이러한 도구의 대부분은 롤링 출력을 생성하는 간격을 허용합니다. 이 출력 형식은 일반적으로 패턴을 훨씬 쉽게 찾아볼 수 있게 합니다. 허용되는 경우 예제 호출에는 `[interval]`.

이러한 도구의 대부분은 광범위한 역사와 다양한 구성 옵션을 가지고 있습니다. 이 페이지에서는 일반적인 문제를 강조 표시하는 간단한 호출 하위 집합만 제공합니다. 정보의 표준 소스는 항상 각 특정 도구에 대한 참조 문서입니다. 이 문서는 여기에 제공된 문서보다 훨씬 더 철저할 것입니다.

## <a name="guidance"></a>지침

성능 문제를 조사하는 접근 방식을 체계적으로 수행하십시오. 두 가지 일반적인 방법은 USE(사용률, 채도, 오류) 및 RED(속도, 오류, 기간)입니다. RED는 일반적으로 요청 기반 모니터링을 위한 서비스의 컨텍스트에서 사용됩니다. USE는 일반적으로 컴퓨터의 각 리소스에 대해 사용률, 채도 및 오류를 모니터링하는 데 사용됩니다. 모든 컴퓨터의 네 가지 주요 리소스는 CPU, 메모리, 디스크 및 네트워크입니다. 이러한 리소스에 대한 높은 사용률, 채도 또는 오류율은 시스템에 문제가 있음을 나타냅니다. 문제가 있는 경우 근본 원인을 조사합니다: 디스크 IO 대기 시간이 높은 이유는 무엇입니까? 디스크 또는 가상 컴퓨터 SKU가 제한됩니까? 어떤 프로세스가 장치에 쓰고 있으며 어떤 파일에 기록됩니까?

일반적인 문제 및 지표의 몇 가지 예는 그들을 진단:
- IOPS 제한: iostat를 사용하여 장치당 IOPS를 측정합니다. 개별 디스크가 한도를 초과하지 않고 모든 디스크의 합계가 가상 컴퓨터의 제한보다 작지 않은지 확인합니다.
- 대역폭 제한: IOPS에 iostat를 사용하지만 읽기/쓰기 처리량을 측정합니다. 장치별 처리량과 집계 처리량이 대역폭 제한 보다 낮게 유지되도록 합니다.
- SNAT 고갈: 이것은 SAR에서 높은 활성(아웃바운드) 연결로 나타날 수 있습니다. 
- 패킷 손실: 전송/수신 된 수를 기준으로 TCP 재전송 수를 통해 프록시로 측정할 수 있습니다. 둘 `sar` `netstat` 다 이 정보를 표시할 수 있습니다.

## <a name="general"></a>일반

이러한 도구는 범용이며 기본 시스템 정보를 다룹니다. 그들은 추가 조사를위한 좋은 출발점입니다.

### <a name="uptime"></a>가동

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

가동 시간은 시스템 가동 시간과 1, 5 및 15분 부하 평균을 제공합니다. 이러한 로드 평균은 작업을 수행하거나 중단 없는 작업이 완료되기를 기다리는 스레드에 대략 해당합니다. 절대에서 이 숫자는 해석하기 어려울 수 있지만 시간이 지남에 따라 측정하면 유용한 정보를 알 수 있습니다.

- 평균 > 1분 평균 5분은 부하가 증가하고 있다는 것을 의미합니다.
- 평균 < 5분 평균은 부하가 감소한다는 것을 의미합니다.

가동 시간은 또한 정보를 사용할 수 없는 이유를 비출 수 있습니다: 사용자가 컴퓨터에 액세스하기 전에 문제가 자체적으로 또는 다시 시작하여 해결되었을 수 있습니다.

사용 가능한 CPU 스레드 수보다 로드 평균이 높음은 지정된 워크로드의 성능 문제를 나타낼 수 있습니다.

### <a name="dmesg"></a>Dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg는 커널 버퍼를 덤프합니다. OOMKill과 같은 이벤트는 커널 버퍼에 항목을 추가합니다. dmesg 로그에서 OOMKill 또는 기타 리소스 소모 메시지를 찾는 것은 문제의 강력한 지표입니다.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`현재 시스템 상태에 대한 광범위한 개요를 제공합니다. 헤더는 몇 가지 유용한 집계 정보를 제공합니다.

- 작업의 상태: 실행, 자 고, 중지.
- CPU 사용률,이 경우 주로 유휴 시간을 표시합니다.
- 총, 무료 및 사용된 시스템 메모리.

`top`단명한 프로세스를 놓칠 수 있습니다. 이러한 단점 `htop` `atop` 중 일부를 수정하는 동안 유사한 인터페이스를 제공합니다.

## <a name="cpu"></a>CPU

이러한 도구는 CPU 사용률 정보를 제공합니다. 이는 패턴이 쉽게 발견될 수 있는 롤링 출력에 특히 유용합니다.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`CPU 스레드별로 세분화하여 유사한 CPU 정보를 맨 위에 인쇄합니다. 한 번에 모든 코어를 보는 것은 단일 스레드 응용 프로그램이 100% 사용률에서 하나의 코어를 사용하는 경우와 같은 불균형이 높은 CPU 사용량을 감지하는 데 유용할 수 있습니다. 이 문제는 시스템의 모든 CPU를 통해 집계될 때 발견하기가 더 어려울 수 있습니다.

### <a name="vmstat"></a>Vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`유사한 정보를 `mpstat` `top`제공하며, CPU(r 열), 메모리 통계 및 각 작업 상태에 소요된 CPU 시간의 백분율을 열거합니다.

## <a name="memory"></a>메모리

메모리는 매우 중요하고 고맙게도 쉽게 추적 할 수있는 리소스입니다. 일부 도구는 CPU와 메모리를 `vmstat`모두 보고할 수 있습니다. 그러나 빠른 `free` 디버깅에 여전히 유용할 수 있습니다.

### <a name="free"></a>제한 없음

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`총 메모리뿐만 아니라 사용 및 사용 가능한 메모리에 대한 기본 정보를 제공합니다. `vmstat`롤링 출력을 제공하는 기능으로 인해 기본 메모리 분석에도 더 유용할 수 있습니다.

## <a name="disk"></a>디스크

이러한 도구는 디스크 IOPS, 대기 대기열 및 총 처리량을 측정합니다. 

### <a name="iostat"></a>Iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`디스크 사용률에 대한 심층적인 통찰력을 제공합니다. 이 호출은 `-x` 부팅 이후 `-y` 초기 출력 인쇄 시스템 평균을 건너뛰고 `1 1` 1초 간격을 지정하여 출력 블록 한 블록 후에 끝나는 확장 된 통계를 전달합니다. 

`iostat`많은 유용한 통계를 노출합니다.

- `r/s``w/s` 초당 읽기및 초당 쓰기입니다. 이러한 값의 합계는 IOPS입니다.
- `rkB/s`초당 `wkB/s` 읽혀/기록된 킬로바이트입니다. 이러한 값의 합계는 처리량입니다.
- `await`는 대기 대기 요청의 평균 iowait 시간(밀리초)입니다.
- `avgqu-sz`은 제공된 간격에 대한 평균 큐 크기입니다.

Azure VM에서:

- 개별 블록 `r/s` `w/s` 장치의 합계는 해당 디스크의 SKU 제한을 초과할 수 없습니다.
- 개별 블록 `rkB/s` `wkB/s` 장치의 합계가 해당 디스크의 SKU 제한을 초과할 수 없습니다.
- 모든 블록 `r/s` `w/s` 장치의 합계는 VM SKU의 제한을 초과할 수 없습니다.
- 모든 블록 `rkB/s` 장치에 대한 'wkB/s의 합계는 VM SKU의 제한을 초과할 수 없습니다.

OS 디스크는 용량에 해당하는 가장 작은 SKU의 관리 디스크로 계산됩니다. 예를 들어 1024GB OS 디스크는 P30 디스크에 해당합니다. 임시 OS 디스크 및 임시 디스크에는 개별 디스크 제한이 없습니다. 전체 VM 제한에 의해서만 제한됩니다.

await 또는 avgqu-sz의 0이 아닌 값은 IO 경합의 좋은 지표이기도 합니다.

## <a name="network"></a>네트워크

이러한 도구는 처리량, 전송 실패 및 사용률과 같은 네트워크 통계를 측정합니다. 심층 분석을 통해 혼잡 및 삭제된 패킷에 대한 세분화된 TCP 통계를 노출할 수 있습니다.

### <a name="sar"></a>특별 행정구

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`다양한 분석을 위한 강력한 도구입니다. 이 예제에서는 네트워크 통계를 측정하는 기능을 사용하지만 CPU 및 메모리 소비를 측정하는 데에도 똑같이 강력합니다. 이 예제에서는 `sar` (네트워크 장치) `DEV` 키워드를 지정하는 플래그를 사용하여 `-n` 장치별로 네트워크 처리량을 표시합니다.

- 의 `rxKb/s` 합계와 `txKb/s` 지정된 장치에 대한 총 처리량입니다. 이 값이 프로비저닝된 Azure NIC의 제한을 초과하면 컴퓨터의 워크로드에 네트워크 대기 시간이 증가합니다.
- `%ifutil`특정 장치에 대한 사용률을 측정합니다. 이 값이 100%에 가까워지면 워크로드는 네트워크 대기 시간이 증가합니다.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

이 호출은 `sar` 키워드를 `TCP,ETCP` 사용하여 TCP 연결을 검사합니다. 마지막 행의 세 번째 열인 "retrans"는 초당 TCP 재전송 횟수입니다. 이 필드의 값이 높음은 신뢰할 수 없는 네트워크 연결을 나타냅니다. 첫 번째 및 세 번째 행에서 "활성"은 로컬 장치에서 시작된 연결을 의미하고 "원격"은 들어오는 연결을 나타냅니다.  Azure에서 일반적인 문제는 감지하는 데 도움이 `sar` 될 수 있는 SNAT 포트 소모입니다. SNAT 포트 소모는 로컬에서 시작된 아웃바운드 TCP 연결의 높은 비율로 인해 문제가 발생하기 때문에 높은 "활성" 값으로 나타납니다.

간격이 걸리면 `sar` 롤링 출력을 인쇄한 다음 호출의 평균 결과를 포함하는 최종 출력 행을 인쇄합니다.

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`여기에 요약 출력으로 호출, 네트워크 통계의 다양한 introspect 수 있습니다. 문제에 따라 여기에 많은 유용한 필드가 있습니다. TCP 섹션의 유용한 필드 중 하나는 "연결 실패 시도"입니다. 이는 SNAT 포트가 고갈되거나 아웃바운드 연결을 만드는 다른 문제의 표시일 수 있습니다. 재전송된 세그먼트의 비율이 높음(TCP 섹션 아래)은 패킷 전달 문제를 나타낼 수 있습니다. 
