---
title: Linux 성능 도구
titleSuffix: Azure Kubernetes Service
description: Linux 성능 도구를 사용 하 여 AKS (Azure Kubernetes Service)를 사용할 때 발생 하는 일반적인 문제를 해결 하는 방법에 대해 알아봅니다.
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982041"
---
# <a name="linux-performance-troubleshooting"></a>Linux 성능 문제 해결

Linux 컴퓨터의 리소스 소모는 일반적인 문제 이며 다양 한 증상을 통해 매니페스트 될 수 있습니다. 이 문서에서는 이러한 문제를 진단 하는 데 사용할 수 있는 도구에 대해 개략적인 개요를 제공 합니다.

이러한 도구 중 상당수는 롤링 출력을 생성 하는 간격을 적용 합니다. 이 출력 형식은 일반적으로 발견 패턴을 훨씬 더 쉽게 만듭니다. 허용 되는 경우 예제 호출에는가 포함 됩니다 `[interval]` .

이러한 도구 중 상당수는 광범위 한 기록과 광범위 한 구성 옵션을 포함 합니다. 이 페이지에서는 일반적인 문제를 강조 표시 하는 간단한 호출 하위 집합만 제공 합니다. 정보의 정식 원본은 항상 각 특정 도구에 대 한 참조 설명서입니다. 해당 설명서는 여기에 제공 된 것 보다 훨씬 더 철저 합니다.

## <a name="guidance"></a>지침

성능 문제를 조사 하는 방법을 체계적으로 설명 하세요. 두 가지 일반적인 방법 (사용률, 채도, 오류) 및 빨강 (비율, 오류, 기간)이 사용 됩니다. 빨간색은 일반적으로 요청 기반 모니터링에 대 한 서비스의 컨텍스트에서 사용 됩니다. 사용은 일반적으로 컴퓨터의 각 리소스에 대해 사용률, 채도 및 오류를 모니터링 하는 리소스를 모니터링 하는 데 사용 됩니다. 모든 컴퓨터의 네 가지 주요 리소스는 cpu, 메모리, 디스크 및 네트워크입니다. 이러한 리소스에 대 한 높은 사용률, 포화 또는 오류율은 시스템에 문제가 있을 수 있음을 나타냅니다. 문제가 있는 경우 근본 원인: 디스크 IO 대기 시간이 높은 이유를 조사 하십시오. 디스크 또는 가상 머신 SKU가 정체 되었습니까? 장치에 쓰는 프로세스 및 파일에는 어떤 것이 있나요?

일반적인 문제 및 진단에 대 한 몇 가지 예는 다음과 같습니다.
- IOPS 제한: 용 iostat를 사용 하 여 장치 단위 IOPS를 측정 합니다. 개별 디스크가 해당 한도를 초과 하지 않도록 하 고, 모든 디스크의 합계가 가상 컴퓨터에 대 한 한도 미만 이어야 합니다.
- 대역폭 제한: 용 iostat를 IOPS에 사용 하지만 읽기/쓰기 처리량을 측정 합니다. 장치당 처리량 및 집계 처리량이 모두 대역폭 제한 보다 낮은 지 확인 합니다.
- SNAT 소모: 특별 행정구에서 활성 (아웃 바운드) 연결을 사용할 수 있습니다. 
- 패킷 손실: 송신/수신 수를 기준으로 TCP 재전송 횟수를 통해 프록시로 측정할 수 있습니다. `sar`및 모두 `netstat` 이 정보를 표시할 수 있습니다.

## <a name="general"></a>일반

이러한 도구는 일반적인 용도로 기본 시스템 정보를 포함 합니다. 추가 조사를 위한 좋은 출발점입니다.

### <a name="uptime"></a>시간은

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

작동 시간은 시스템 작동 시간 및 1, 5, 15 분의 로드 평균을 제공 합니다. 이러한 로드 평균 평균은 작업을 수행 하는 스레드 또는 무정전 작업이 완료 될 때까지 대기 하는 것과 같습니다. 절대 이러한 수치는 해석 하기 어려울 수 있지만 시간에 따라 측정 하 여 유용한 정보를 알려줄 수 있습니다.

- 1 분 평균 > 5 분 평균은 부하가 늘어나고 있음을 의미 합니다.
- 1 분 평균 < 5 분 평균은 부하가 감소 함을 의미 합니다.

가동 시간을 통해 정보를 사용할 수 없는 이유를 확인할 수도 있습니다. 즉, 문제가 자체적으로 해결 되거나 사용자가 컴퓨터에 액세스 하기 전에 다시 시작 될 수 있습니다.

사용 가능한 CPU 스레드 수보다 더 높은 로드 평균은 지정 된 워크 로드의 성능 문제를 나타낼 수 있습니다.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg는 커널 버퍼를 덤프 합니다. OOMKill과 같은 이벤트는 커널 버퍼에 항목을 추가 합니다. Dmesg 로그에서 OOMKill 또는 기타 리소스 소모 메시지를 찾는 것은 문제에 대 한 강력한 지표입니다.

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

`top` 현재 시스템 상태에 대 한 광범위 한 개요를 제공 합니다. 헤더는 다음과 같은 몇 가지 유용한 집계 정보를 제공 합니다.

- 작업 상태: 실행 중, 중지 중, 중지 됨입니다.
- CPU 사용률 (이 경우에는 대부분 유휴 시간을 표시)
- 사용 가능한 전체 시스템 메모리입니다.

`top` 수명이 짧은 프로세스를 놓칠 수 있습니다. 및와 같은 대안은 `htop` `atop` 이러한 단점 중 일부를 해결 하는 동시에 비슷한 인터페이스를 제공 합니다.

## <a name="cpu"></a>CPU

이러한 도구는 CPU 사용률 정보를 제공 합니다. 패턴을 쉽게 찾을 수 있게 하는 롤링 출력에 특히 유용 합니다.

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

`mpstat` 는 비슷한 CPU 정보를 위쪽에 인쇄 하지만 CPU 스레드를 기준으로 세분화 됩니다. 모든 코어를 한 번에 표시 하는 것은 단일 스레드 응용 프로그램이 100% 사용률에서 코어 하나를 사용 하는 경우와 같이 불균형 CPU 사용량을 검색 하는 데 유용할 수 있습니다. 이 문제는 시스템의 모든 Cpu에 대해 집계할 때 더 어려울 수 있습니다.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` 비슷한 정보를 제공 하 `mpstat` 고 `top` cpu (r 열)를 대기 중인 프로세스의 수, 메모리 통계 및 각 작업 상태에 소요 된 cpu 시간의 백분율을 열거 합니다.

## <a name="memory"></a>메모리

메모리는 추적 하는 데 매우 중요 하 고 다행 스럽게는 리소스입니다. 일부 도구는와 같은 CPU와 메모리를 모두 보고할 수 있습니다 `vmstat` . 그러나와 같은 도구 `free` 는 빠른 디버깅에도 유용할 수 있습니다.

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` 사용 된 메모리와 사용 가능한 메모리 뿐만 아니라 총 메모리에 대 한 기본 정보를 제공 합니다. `vmstat` 는 롤링 출력을 제공 하는 기능 때문에 기본 메모리 분석에도 유용할 수 있습니다.

## <a name="disk"></a>디스크

이러한 도구는 디스크 IOPS, 대기 큐 및 총 처리량을 측정 합니다. 

### <a name="iostat"></a>용 iostat

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

`iostat` 디스크 사용률에 대 한 심층 정보를 제공 합니다. 이 호출은 `-x` 확장 된 통계를 전달 `-y` 하 여 부팅 이후의 초기 출력 인쇄 시스템 평균을 건너뛰고 `1 1` 1 초 간격을 지정 하 여 출력 블록 하나를 종료 합니다. 

`iostat` 많은 유용한 통계를 노출 합니다.

- `r/s` 및 `w/s` 는 초당 읽기 및 쓰기 수/초입니다. 이러한 값의 합계는 IOPS입니다.
- `rkB/s` 및 `wkB/s` 는 초당 읽기/쓰기 킬로바이트입니다. 이러한 값의 합은 처리량입니다.
- `await` 대기 중인 요청에 대 한 평균 iowait 시간 (밀리초)입니다.
- `avgqu-sz` 제공 된 간격을 초과 하는 평균 큐 크기입니다.

Azure VM에서:

- `r/s`개별 블록 장치의 및의 합계는 `w/s` 해당 디스크의 SKU 제한을 초과 하지 않을 수 있습니다.
- `rkB/s` `wkB/s` 개별 블록 장치의 및의 합계가 해당 디스크의 SKU 제한을 초과 하지 않을 수 있습니다.
- `r/s` `w/s` 모든 블록 장치에 대 한 및의 합계가 VM SKU에 대 한 제한을 초과 하지 않을 수 있습니다.
- `rkB/s`모든 블록 장치에 대 한 및 ' wkB/s '의 합계가 VM SKU에 대 한 제한을 초과 하지 않을 수 있습니다.

OS 디스크는 용량에 해당 하는 최소 SKU의 관리 디스크로 계산 됩니다. 예를 들어 1024GB OS 디스크는 P30 디스크에 해당 합니다. 삭제 된 OS 디스크 및 임시 디스크에는 개별 디스크 제한이 없습니다. 전체 VM 제한에 의해서만 제한 됩니다.

Wait 또는 avgqu-sz의 0이 아닌 값은 IO 경합에 대 한 좋은 지표 이기도 합니다.

## <a name="network"></a>네트워크

이러한 도구는 처리량, 전송 실패, 사용률 등의 네트워크 통계를 측정 합니다. 심층 분석은 혼잡 및 삭제 된 패킷에 대 한 세분화 된 TCP 통계를 노출할 수 있습니다.

### <a name="sar"></a>변형

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

`sar` 는 광범위 한 분석을 위한 강력한 도구입니다. 이 예제에서는 네트워크 통계를 측정 하는 기능을 사용 하지만 CPU 및 메모리 소비량을 측정 하는 것과 동일한 성능을 제공 합니다. 이 예제에서는 `sar` 플래그를 사용 하 여를 호출 `-n` 하 고 `DEV` (network device) 키워드를 지정 하 여 장치별로 네트워크 처리량을 표시 합니다.

- 및의 합계 `rxKb/s` 는 `txKb/s` 지정 된 장치에 대 한 총 처리량입니다. 이 값이 프로 비전 된 Azure NIC의 제한을 초과 하는 경우 컴퓨터의 워크 로드에서 네트워크 대기 시간이 증가 합니다.
- `%ifutil` 지정 된 장치에 대 한 사용률을 측정 합니다. 이 값이 100%에 도달 하면 워크 로드의 네트워크 대기 시간이 증가 합니다.

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

이 호출에서는 `sar` 키워드를 사용 하 여 `TCP,ETCP` TCP 연결을 검사 합니다. 마지막 행 "retrans"의 세 번째 열은 초당 TCP 재전송 횟수입니다. 이 필드의 값이 높으면 네트워크 연결이 불안정 함을 의미 합니다. 첫 번째 및 세 번째 행에서 "활성"은 로컬 장치에서 연결을 시작 하는 반면, "remote"는 들어오는 연결을 나타냅니다.  Azure에서 발생 하는 일반적인 문제는 SNAT 포트 소모 이며 검색 하는 `sar` 데 도움이 될 수 있습니다. SNAT 포트 소모는 높은 "활성" 값으로 매니페스트 되므로, 로컬에서 시작 된 아웃 바운드 TCP 연결의 비율이 높습니다.

는 `sar` 간격을 사용 하 여 롤링 출력을 출력 한 다음 호출의 평균 결과를 포함 하는 출력의 마지막 행을 인쇄 합니다.

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

`netstat` 에서는 다양 한 네트워크 통계를 introspect 수 있습니다. 여기에서 요약 출력을 사용 하 여 호출 합니다. 여기에는 문제에 따라 유용한 여러 필드가 있습니다. TCP 섹션에서 유용한 필드 중 하나는 "실패 한 연결 시도"입니다. 이는 SNAT 포트 소모 또는 아웃 바운드 연결을 만드는 다른 문제가 있음을 나타낼 수 있습니다. 재전송 된 세그먼트의 비율이 높으면 (TCP 섹션 에서도) 패킷 배달에 문제가 있음을 나타낼 수 있습니다. 
