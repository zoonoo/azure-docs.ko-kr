---
title: Linux 성능 도구
titleSuffix: Azure Kubernetes Service
description: Linux 성능 도구를 사용하여 AKS(Azure Kubernetes Service) 사용 시 발생하는 일반적인 문제를 해결하는 방법을 알아봅니다.
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90982041"
---
# <a name="linux-performance-troubleshooting"></a>Linux 성능 문제 해결

Linux 머신의 리소스 소모는 일반적인 이슈이며 다양한 증상을 통해 나타날 수 있습니다. 이 문서에서는 해당 이슈를 진단하는 데 사용할 수 있는 도구를 개괄적으로 설명합니다.

대부분의 도구는 롤링 출력을 생성하는 간격을 적용합니다. 일반적으로 이 출력 형식을 통해 패턴을 훨씬 쉽게 파악할 수 있습니다. 적용할 경우 예제 호출에 `[interval]`이 포함됩니다.

대부분의 도구는 광범위한 기록과 다양한 구성 옵션 집합을 포함합니다. 이 페이지에서는 일반적인 문제를 강조하기 위해 간단한 호출 하위 집합만 제공합니다. 표준 정보 소스는 항상 각 특정 도구의 참조 설명서입니다. 해당 설명서가 여기에 제공된 내용보다 훨씬 자세한 정보를 제공합니다.

## <a name="guidance"></a>지침

체계적인 방법으로 성능 이슈를 조사해야 합니다. 두 가지 일반적인 방법은 USE(사용률, 포화, 오류) 및 RED(비율, 오류, 기간)입니다. RED는 일반적으로 요청 기반 모니터링을 위해 서비스 컨텍스트에서 사용됩니다. USE는 일반적으로 리소스를 모니터링하는 데 사용됩니다. 머신에 있는 각 리소스의 사용률, 포화, 오류를 모니터링합니다. 모든 머신에서 네 가지 주요 리소스는 CPU, 메모리, 디스크, 네트워크입니다. 해당 리소스의 높은 사용률, 포화 또는 오류 비율은 시스템에 문제가 있을 수 있음을 나타냅니다. 문제가 있을 경우 근본 원인인 디스크 IO 대기 시간이 높은 이유를 조사합니다. 디스크 또는 가상 머신 SKU가 제한되나요? 어떤 프로세스가 디바이스에 쓰고 어떤 파일에 쓰나요?

일반적인 이슈 및 진단 지표의 몇 가지 예:
- IOPS 제한: iostat를 사용하여 디바이스당 IOPS를 측정합니다. 해당 한도를 초과하는 개별 디스크가 없고 모든 디스크의 합계가 가상 머신의 한도 미만인지 확인합니다.
- 대역폭 제한: IOPS와 마찬가지로 iostat를 사용하지만 읽기/쓰기 처리량을 측정합니다. 디바이스당 처리량과 집계 처리량이 모두 대역폭 한도 미만인지 확인합니다.
- SNAT 소모: SAR의 높은 활성(아웃바운드) 연결로 나타날 수 있습니다. 
- 패킷 손실: 송신/수신 수 대비 TCP 재전송 수를 통해 프록시에서 측정할 수 있습니다. `sar`과 `netstat` 모두 이 정보를 표시할 수 있습니다.

## <a name="general"></a>일반

이 도구는 범용으로, 기본 시스템 정보를 다룹니다. 추가 조사를 위한 좋은 시작점입니다.

### <a name="uptime"></a>작동 시간

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

작동 시간은 시스템 작동 시간과 1분, 5분, 15분 부하 평균을 제공합니다. 부하 평균은 작업을 수행하거나 무중단 작업이 완료될 때까지 대기 중인 스레드에 해당합니다. 절대값인 수치는 해석하기 어려울 수 있지만 시간에 따라 측정된 수치는 유용한 정보를 알려줄 수 있습니다.

- 1분 평균이 5분 평균보다 크면 부하가 증가하고 있는 것입니다.
- 1분 평균이 5분 평균보다 작으면 부하가 감소하고 있는 것입니다.

작동 시간을 통해 정보를 사용할 수 없는 이유를 파악할 수도 있습니다. 즉, 사용자가 머신에 액세스하기 전에 이슈가 자체적으로 또는 다시 시작을 통해 해결되었을 수 있습니다.

부하 평균이 사용 가능한 CPU 스레드 수보다 높으면 지정된 워크로드의 성능 이슈를 나타낼 수 있습니다.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg는 커널 버퍼를 덤프합니다. OOMKill과 같은 이벤트는 커널 버퍼에 항목을 추가합니다. dmesg 로그에서 OOMKill 또는 다른 리소스 소모 메시지를 발견하면 문제를 나타내는 강력한 지표가 됩니다.

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

`top`은 현재 시스템 상태를 개괄적으로 설명합니다. 헤더는 다음과 같은 몇 가지 유용한 집계 정보를 제공합니다.

- 작업 상태: 실행 중, 절전 중, 중지됨
- CPU 사용률(이 경우 대부분 유휴 시간 표시)
- 총 시스템 메모리, 사용 가능한 시스템 메모리, 사용된 시스템 메모리

`top`에서 수명이 짧은 프로세스는 누락될 수 있습니다. `htop` 및 `atop`과 같은 대안은 몇 가지 단점을 수정하는 동시에 비슷한 인터페이스를 제공합니다.

## <a name="cpu"></a>CPU

이 도구는 CPU 사용률 정보를 제공합니다. 패턴을 쉽게 찾을 수 있게 하는 롤링 출력에서 특히 유용합니다.

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

`mpstat`는 top과 유사한 CPU 정보를 인쇄하지만 CPU 스레드별로 분석합니다. 모든 코어를 한 번에 확인하는 기능은 단일 스레드 애플리케이션이 코어 하나를 100% 사용률로 사용하는 경우와 같은 불균형 CPU 사용량을 검색하는 데 유용할 수 있습니다. 이 문제는 시스템의 모든 CPU에 대해 집계할 경우 발견하기 더 어려울 수 있습니다.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`는 `mpstat` 및 `top`과 유사한 정보를 제공하고 CPU를 대기 중인 프로세스 수(r 열), 메모리 통계, 각 작업 상태에서 소요된 CPU 시간 퍼센트를 열거합니다.

## <a name="memory"></a>메모리

메모리는 매우 중요하며 다행히 추적이 용이한 리소스입니다. `vmstat`와 같은 일부 도구는 CPU와 메모리를 둘 다 보고할 수 있습니다. 그러나 `free`와 같은 도구는 빠른 디버깅에 여전히 유용할 수 있습니다.

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`는 사용된 메모리와 사용 가능한 메모리뿐 아니라 총 메모리에 대한 기본 정보를 제공합니다. `vmstat`는 롤링 출력을 제공할 수 있으므로 기본 메모리 분석에도 더 유용할 수 있습니다.

## <a name="disk"></a>디스크

이 도구는 디스크 IOPS, 대기 큐, 총 처리량을 측정합니다. 

### <a name="iostat"></a>iostat

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

`iostat`는 디스크 사용률에 대한 심층 인사이트를 제공합니다. 호출에서 `-x`를 전달하면 확장된 통계가 반환되고, `-y`를 전달하면 초기 출력을 건너뛰고 부팅 이후의 시스템 평균이 인쇄되며, `1 1`을 전달하면 1초 간격 및 하나의 출력 블록 후 종료가 지정됩니다. 

`iostat`는 다음과 같은 많은 유용한 통계를 노출합니다.

- `r/s` 및 `w/s`는 초당 읽기와 초당 쓰기입니다. 두 값의 합계가 IOPS입니다.
- `rkB/s` 및 `wkB/s`는 초당 처리된 읽기/쓰기(킬로바이트)입니다. 두 값의 합계가 처리량입니다.
- `await`는 큐에 대기 중인 요청의 평균 iowait 시간(밀리초)입니다.
- `avgqu-sz`는 제공된 간격의 평균 큐 크기입니다.

Azure VM:

- 개별 블록 디바이스 `r/s` 및 `w/s`의 합계는 해당 디스크의 SKU 한도를 초과할 수 없습니다.
- 개별 블록 디바이스 `rkB/s` 및 `wkB/s`의 합계는 해당 디스크의 SKU 한도를 초과할 수 없습니다.
- 모든 블록 디바이스 `r/s` 및 `w/s`의 합계는 VM SKU의 한도를 초과할 수 없습니다.
- 모든 블록 디바이스 `rkB/s` 및 `wkB/s의 합계는 VM SKU의 한도를 초과할 수 없습니다.

OS 디스크는 용량에 해당하는 최소 SKU의 관리 디스크로 계산됩니다. 예를 들어 1024GB OS 디스크는 P30 디스크에 해당합니다. 임시 OS 디스크와 임시 디스크에는 개별 디스크 한도가 없습니다. 전체 VM 한도로만 제한됩니다.

0이 아닌 await 또는 avgqu-sz 값은 IO 경합을 나타내는 좋은 지표이기도 합니다.

## <a name="network"></a>네트워크

이 도구는 처리량, 전송 실패, 사용률 등의 네트워크 통계를 측정합니다. 심층 분석을 통해 정체 및 삭제된 패킷에 대한 세분화된 TCP 통계를 노출할 수 있습니다.

### <a name="sar"></a>sar

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

`sar`은 광범위한 분석을 위한 강력한 도구입니다. 이 예제에서는 네트워크 통계를 측정하지만 CPU 및 메모리 사용량을 측정하는 경우에도 유용합니다. 예제에서는 `-n` 플래그로 `DEV`(네트워크 디바이스) 키워드를 지정하여 `sar`을 호출하고 디바이스별 네트워크 처리량을 표시합니다.

- `rxKb/s` 및 `txKb/s`의 합계가 지정된 디바이스의 총 처리량입니다. 이 값이 프로비저닝된 Azure NIC의 한도를 초과할 경우 머신에서 워크로드의 네트워크 대기 시간이 증가합니다.
- `%ifutil`은 지정된 디바이스의 사용률을 측정합니다. 이 값이 100%에 접근하면 워크로드의 네트워크 대기 시간이 증가합니다.

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

`sar` 호출은 `TCP,ETCP` 키워드를 사용하여 TCP 연결을 검사합니다. 마지막 행의 세 번째 열인 “retrans”는 초당 TCP 재전송 수입니다. 이 필드의 값이 높으면 네트워크 연결이 불안정함을 의미합니다. 첫 번째 및 세 번째 행에서 “active”는 로컬 디바이스에서 시작된 연결을 의미하는 반면, “remote”는 들어오는 연결을 나타냅니다.  Azure에서 발생하는 일반적인 문제는 SNAT 포트 소모로, `sar`을 통해 검색할 수 있습니다. SNAT 포트 소모는 높은 “active” 값으로 나타납니다. 로컬에서 시작된 아웃바운드 TCP 연결 비율이 높아서 문제가 발생하기 때문입니다.

`sar`은 간격을 사용하므로 롤링 출력을 인쇄한 다음, 호출의 평균 결과를 포함하는 출력의 마지막 행을 인쇄합니다.

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

`netstat`은 요약 출력을 사용하여 호출된 다양한 네트워크 통계를 성찰할 수 있습니다. 여기에는 이슈에 따라 유용한 여러 필드가 있습니다. TCP 섹션에서 유용한 필드 중 하나는 “연결 시도 실패 횟수”입니다. 이 필드는 SNAT 포트 소모 또는 아웃바운드 연결 시 발생하는 기타 이슈를 나타낼 수 있습니다. TCP 섹션도 포함하여 재전송된 세그먼트의 비율이 높으면 패킷 배달 이슈를 나타낼 수 있습니다. 
