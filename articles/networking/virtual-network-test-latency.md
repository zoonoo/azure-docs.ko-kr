---
title: Azure Virtual Network에서 Azure 가상 머신 네트워크 대기 시간 테스트 | Microsoft Docs
titleSuffix: Azure Virtual Network latency
description: 가상 네트워크에서 Azure Vm 간의 네트워크 대기 시간을 테스트 하는 방법 알아보기
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: d5efc1b802246597b4ee545b4d805e4f0d10ebb5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166565"
---
# <a name="testing-network-latency"></a>네트워크 대기 시간 테스트

작업에 맞게 설계 된 도구를 사용 하 여 네트워크 대기 시간을 측정 하면 가장 정확한 결과를 얻을 수 있습니다. SockPerf (Linux 용) 및 Latte (Windows 용)와 같은 공개적으로 사용 가능한 도구는 응용 프로그램 대기 시간과 같은 다른 유형의 대기 시간을 제외 하 고 네트워크 대기 시간을 구분 하 고 측정할 수 있는 도구의 예입니다. 이러한 도구는 응용 프로그램 성능, 즉 TCP 및 UDP에 영향을 주는 네트워크 트래픽 종류에 초점을 둡니다. Ping과 같은 다른 일반적인 연결 도구는 대기 시간 측정에 사용 될 수도 있지만 이러한 결과는 실제 워크 로드에서 사용 되는 네트워크 트래픽을 나타내지 않을 수 있습니다. &#39;이러한 도구는 대부분 ICMP 프로토콜을 사용 하기 때문에 응용 프로그램 트래픽과 다르게 처리할 수 있으며, 결과는 TCP 및 UDP를 사용 하는 작업에 적용 되지 않을 수 있습니다. 대부분의 응용 프로그램에서 사용 하는 프로토콜의 정확한 네트워크 대기 시간 테스트를 위해 SockPerf (Linux) 및 Latte (Windows 용)에서 가장 관련성이 높은 결과를 생성 합니다. 이 문서에서는 이러한 두 도구에 대해 설명 합니다.

## <a name="overview"></a>개요

두 개의 Vm (보낸 사람 및 받는 사람)을 사용 하 여 양방향 통신 채널을 사용 하 여 RTT (왕복 시간)를 측정 하기 위해 양방향 통신 채널을 사용 하 여 양쪽 방향으로 패킷을 보내고 받습니다.

이러한 단계를 사용 하 여 두 Vm (Virtual Machines) 간의 네트워크 대기 시간을 측정 하거나 두 개의 물리적 컴퓨터 간에 네트워크 대기 시간을 측정할 수 있습니다. 대기 시간 측정은 다음과 같은 경우에 유용할 수 있습니다.

- 배포 된 Vm 간의 네트워크 대기 시간에 대 한 벤치 마크 설정
- 관련 변경 내용이 적용 된 후 네트워크 대기 시간 변경의 영향을 비교 합니다.
  - 구성 변경 내용을 포함 한 OS 또는 네트워크 스택 소프트웨어
  - 영역 또는 PPG에 배포 하는 것과 같은 VM 배포 방법
  - 가속 네트워킹 또는 크기 변경과 같은 VM 속성
  - 라우팅 또는 필터링 변경 등의 가상 네트워크

### <a name="tools-for-testing"></a>테스트용 도구
대기 시간을 측정 하기 위해 두 가지 옵션이 있습니다. 하나는 Windows 기반 시스템용 이며 다른 하나는 Linux 기반 시스템용입니다.

Windows: latte (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Linux: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

이러한 도구를 사용 하면 TCP 또는 UDP 페이로드 배달 시간만 측정 되 고 ICMP (Ping) 또는 응용 프로그램에서 사용 되지 않으며 성능에 영향을 주지 않는 다른 패킷 유형은 측정 됩니다.

### <a name="tips-for-an-optimal-vm-configuration"></a>최적의 VM 구성을 위한 팁:

- 최신 버전의 Windows 또는 Linux 사용
- 최상의 결과를 위해 가속화 네트워킹 사용
- [Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) 을 사용 하 여 vm 배포
- 일반적으로 더 큰 Vm은 더 작은 Vm 보다 성능이 우수 합니다.

### <a name="tips-for-analysis"></a>분석 팁

- 배포, 구성 및 최적화가 완료 되는 즉시 초기 계획 수립
- 제어 된 변경 내용을 사용 하 여 항상 새 결과를 기준선과 비교 하거나 한 테스트에서 다른 테스트로 비교
- 변경 내용이 관찰 되거나 계획 될 때마다 테스트 반복


## <a name="testing-vms-running-windows"></a>Windows를 실행 하는 Vm 테스트:

## <a name="get-latteexe-onto-the-vms"></a>Vm에 Latte 가져오기

최신 버전 다운로드: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

C:\tools와 같이 별도의 폴더에 Latte를 배치 하는 것이 좋습니다.

## <a name="allow-latteexe-through-the-windows-firewall"></a>Windows 방화벽을 통해 Latte를 허용 합니다.

수신기에서 Windows 방화벽에 대 한 허용 규칙을 만들어 Latte 트래픽이 도착 하도록 허용 합니다. 특정 TCP 포트 인바운드를 허용 하는 대신 이름으로 전체 Latte 프로그램을 허용 하는 것이 가장 쉽습니다.

다음과 같이 Windows 방화벽을 통해 Latte를 허용 합니다.

netsh advfirewall firewall add rule program =\<PATH\>\\latte name =&quot;Latte&quot; protocol = any dir = in action = allow enable = yes profile = ANY


예를 들어 latte를 &quot;c:\\tools&quot; 폴더로 복사한 경우 명령은 다음과 같습니다.

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>대기 시간 테스트 실행

수신기에서 latte를 시작 합니다 (PowerShell이 아닌 CMD에서 실행).

latte-&lt;받는 사람 IP 주소&gt;:&lt;포트&gt;-i &lt;반복&gt;

약 65k 반복은 대표적인 결과를 반환할 만큼 길어야 합니다.

사용 가능한 포트 번호는 모두 충분 합니다.

VM의 IP 주소가 10.0.0.4 인 경우 다음과 같습니다.

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

보낸 사람에 대해 latte를 시작 합니다 (PowerShell이 아닌 CMD에서 실행).

latte-a \<받는 사람 IP 주소\>:\<포트\>-i \<반복\>


결과 명령은 &quot;-c&quot; &quot;&quot;를 추가 하는 경우를 제외 하 고는 받는 사람에 대 한 것과 동일 합니다.

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

결과를 기다립니다. Vm이 얼마나 떨어져 있는지에 따라 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 더 긴 테스트를 실행 하기 전에 성공 여부를 테스트 하려면 더 짧은 반복으로 시작 하는 것이 좋습니다.



## <a name="testing-vms-running-linux"></a>Linux를 실행 하는 Vm 테스트

SockPerf를 사용 합니다. [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf) 에서 사용할 수 있습니다.

### <a name="install-sockperf-on-the-vms"></a>Vm에 SockPerf 설치

Linux Vm (발신자와 수신자 모두)에서 다음 명령을 실행 하 여 Vm에서 SockPerf을 준비 합니다. 주요 배포판에 대해 명령이 제공 됩니다.

#### <a name="for-rhel--centos-follow-these-steps"></a>RHEL/CentOS의 경우 다음 단계를 수행 합니다.
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Ubuntu의 경우 다음 단계를 수행 합니다.
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>모든 배포판의 경우 다음 단계에 따라 SockPerf를 복사 하 고 컴파일하고 설치 합니다.
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Vm에서 SockPerf 실행

SockPerf 설치가 완료 되 면 Vm은 대기 시간 테스트를 실행할 준비가 된 것입니다. 

먼저 수신자에 대해 SockPerf를 시작 합니다.

사용 가능한 포트 번호는 모두 충분 합니다. 이 예제에서는 포트 12345을 사용 합니다.
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
서버에서 수신 대기 하 고 있으므로 클라이언트는 수신 대기 중인 포트의 서버에 패킷을 전송 하기 시작할 수 있습니다 .이 경우 12345입니다.

다음 예에 표시 된 것 처럼 대표적인 결과를 반환할 때까지 약 100 초 정도 짧습니다.
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

결과를 기다립니다. Vm이 얼마나 떨어져 있는지에 따라 반복 횟수는 달라 집니다. 더 긴 테스트를 실행 하기 전에 성공 여부를 테스트 하는 데 약 5 초 정도의 짧은 테스트로 시작 하는 것이 좋습니다.

이 SockPerf 예제에서는 일반적인 평균 크기 패킷 이므로 350 바이트 메시지 크기를 사용 합니다. 메시지 크기를 더 높거나 낮게 조정 하 여 Vm에서 실행 되는 작업을 보다 정확 하 게 나타내는 결과를 달성할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) 을 사용 하 여 대기 시간 향상
* 시나리오를 위해 [vm에 대 한 네트워킹을 최적화](../virtual-network/virtual-network-optimize-network-bandwidth.md) 하는 방법에 대해 알아봅니다.
* [가상 머신에 대역폭이 할당되는 방법](../virtual-network/virtual-machine-network-throughput.md)을 알아봅니다.
* [Azure Virtual Network FAQ(질문과 대답)](../virtual-network/virtual-networks-faq.md)에 대해 자세히 알아보기
