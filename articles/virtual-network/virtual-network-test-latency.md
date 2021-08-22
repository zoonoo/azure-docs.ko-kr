---
title: Azure 가상 네트워크에서 Azure 가상 머신 네트워크 대기 시간 테스트 | Microsoft Docs
description: 가상 네트워크에서 Azure 가상 머신 간의 네트워크 대기 시간을 테스트하는 방법 알아보기
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: bd316ce7a868f639cdfd80293d5d6189e2942f6f
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988178"
---
# <a name="test-vm-network-latency"></a>VM 네트워크 대기 시간 테스트

가장 정확한 결과를 얻으려면 작업을 위해 설계된 도구를 사용하여 Azure VM(가상 머신) 네트워크 대기 시간을 측정합니다. SockPerf(Linux용) 및 latte.exe(Windows용)와 같은 공개적으로 사용 가능한 도구는 애플리케이션 대기 시간과 같은 다른 유형의 대기 시간을 제외하고 네트워크 대기 시간을 분리하고 측정할 수 있습니다. 이러한 도구는 애플리케이션 성능에 영향을 주는 네트워크 트래픽 종류(즉, TCP(Transmission Control Protocol) 및 UDP(사용자 데이터그램 프로토콜) 트래픽)에 중점을 둡니다. 

Ping과 같은 다른 일반적인 연결 도구는 대기 시간을 측정할 수 있지만 실제 작업에서 사용되는 네트워크 트래픽을 표시하지 않을 수 있습니다. 이러한 도구의 대부분은 ICMP(Internet Control Message Protocol)를 사용하기 때문입니다. ICMP는 애플리케이션 트래픽과 다르게 처리할 수 있으며 그 결과는 TCP 및 UDP를 사용하는 작업에 적용되지 않을 수 있습니다. 

대부분의 애플리케이션에서 사용하는 프로토콜의 정확한 네트워크 대기 시간 테스트를 위해 SockPerf(Linux용) 및 latte.exe(Windows용)에서 가장 관련성이 높은 결과를 생성합니다. 이 문서에서는 이러한 도구를 모두 다룹니다.

## <a name="overview"></a>개요

두 VM을 발신자로, 하나는 수신자로 사용하여 양방향 통신 채널을 만듭니다. 이 접근 방식을 사용하면 양방향으로 패킷을 보내고 받을 수 있으며 RTT(왕복 시간)를 측정할 수 있습니다.

이 방법을 사용하여 두 VM 간의 네트워크 대기 시간을 측정하거나 두 개의 물리적 컴퓨터 간에 네트워크 대기 시간을 측정할 수 있습니다. 대기 시간 측정은 다음과 같은 경우에 유용할 수 있습니다.

- 배포된 VM 간의 네트워크 대기 시간에 대한 벤치마크를 설정합니다.
- 관련 변경 내용이 적용된 후 네트워크 대기 시간 변경의 영향을 비교합니다.
  - OS(운영 체제) 또는 네트워크 스택 소프트웨어(구성 변경 포함)
  - VM 배포 방법(예: 가용성 영역 또는 PPG(근접 배치 그룹)에 배포)
  - 가속화된 네트워킹 또는 크기 변경과 같은 VM 속성
  - 라우팅 또는 필터링 변경 등의 가상 네트워크

### <a name="tools-for-testing"></a>테스트 도구
대기 시간을 측정하기 위한 두 가지 도구 옵션은 다음과 같습니다.

* Windows 기반 시스템용: [latte.exe(Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* Linux 기반 시스템용: [SockPerf(Linux)](https://github.com/mellanox/sockperf)

이러한 도구를 사용하여 TCP 또는 UDP 페이로드 배달 시간만 측정되고 ICMP(Ping) 또는 애플리케이션에서 사용되지 않으며 성능에 영향을 주지 않는 다른 패킷 유형이 아닌 것을 확인할 수 있습니다.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>최적의 VM 구성 만들기에 대한 팁

VM 구성을 만들 때 다음 권장 사항을 염두에 두어야 합니다.
- 최신 버전의 Windows 또는 Linux를 사용합니다.
- 최상의 결과를 위해 가속 네트워킹을 사용하도록 설정합니다.
- [Azure 근접 배치 그룹](../virtual-machines/co-location.md)을 사용하여 VM을 배포합니다.
- 일반적으로 더 큰 VM은 더 작은 VM보다 성능이 우수합니다.

### <a name="tips-for-analysis"></a>분석 팁

테스트 결과를 분석하는 동안 다음과 같은 권장 사항을 염두에 두어야 합니다.

- 배포, 구성 및 최적화가 완료되는 즉시 초기 계획을 수립합니다.
- 항상 새 결과를 기준선과 비교하거나, 제어된 변경 내용이 있는 한 테스트에서 다른 테스트로 비교합니다.
- 변경 내용이 관찰되거나 계획될 때마다 테스트를 반복합니다.


## <a name="test-vms-that-are-running-windows"></a>Windows를 실행하는 VM 테스트

### <a name="get-latteexe-onto-the-vms"></a>VM에 대한 latte.exe 가져오기

[최신 버전의 latte.exe](https://github.com/microsoft/latte/releases/download/v0/latte.exe)를 다운로드합니다.

*c:\tools* 와 같이 별도의 폴더에 latte.exe를 배치하는 것이 좋습니다.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Windows Defender 방화벽을 통한 latte.exe 허용

*수신기* 에서 Windows Defender 방화벽에 latte.exe 트래픽이 도착하도록 허용 규칙을 만듭니다. 특정 TCP 포트에서 인바운드를 허용하는 것보다 이름으로 전체 latte.exe 프로그램을 허용하는 것이 가장 쉽습니다.

다음 명령을 실행하여 Windows Defender 방화벽을 통해 latte.exe를 허용합니다.

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

예를 들어 latte.exe를 *c:\tools* 폴더에 복사한 경우 명령은 다음과 같습니다.

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>대기 시간 테스트 실행

* *수신기* 에서 latte.exe를 시작합니다(PowerShell이 아닌 CMD 창에서 실행).

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    65,000회 반복은 대표적인 결과를 반환하기에 충분히 깁니다.

    사용 가능한 포트 번호는 모두 충분합니다.

    VM의 IP 주소가 10.0.0.4이면 명령은 다음과 같이 표시됩니다.

    `latte -a 10.0.0.4:5005 -i 65100`

* *송신기* 에서 latte.exe를 시작합니다(PowerShell이 아닌 CMD 창에서 실행).

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    결과 명령은 *클라이언트* 또는 *송신기* 임을 나타내도록 &nbsp; *-c* 를 추가하는 것을 제외하고는 수신기에서와 동일합니다.

    `latte -c -a 10.0.0.4:5005 -i 65100`

결과를 기다립니다. VM이 얼마나 떨어져 있는지에 따라 테스트를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 더 긴 테스트를 실행하기 전에 성공 여부를 테스트하려면 더 짧은 반복으로 시작하는 것이 좋습니다.

## <a name="test-vms-that-are-running-linux"></a>Linux를 실행하는 VM 테스트

Linux를 실행하는 VM을 테스트하려면 [SockPerf](https://github.com/mellanox/sockperf)를 사용합니다.

### <a name="install-sockperf-on-the-vms"></a>VM에 SockPerf 설치

*송신기* 과 *수신기* 모두 Linux VM에서 다음 명령을 실행하여 VM에 대한 SockPerf를 준비합니다. 주요 배포판에 대해 명령이 제공됩니다.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>RHEL(Red Hat Enterprise Linux)/CentOS

다음 명령을 실행합니다.

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Ubuntu

다음 명령을 실행합니다.

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>모든 배포판

다음 단계에 따라 SockPerf를 복사, 컴파일 및 설치합니다.

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>VM에서 SockPerf 실행

SockPerf 설치가 완료되면 VM은 대기 시간 테스트를 실행할 준비가 된 것입니다. 

먼저 *수신자* 에 대해 SockPerf를 시작합니다.

사용 가능한 포트 번호는 모두 충분합니다. 이 예제에서는 포트 12345를 사용합니다.

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

이제 서버가 수신 대기 중이므로 클라이언트는 수신 대기 중인 포트(이 경우 12345)에서 서버로 패킷을 전송하기 시작할 수 있습니다.

다음 예제에 표시된 것처럼 약 100초는 대표적인 결과를 반환하기에 충분한 시간입니다.

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

결과를 기다립니다. VM이 얼마나 떨어져 있는지에 따라 반복 횟수는 달라집니다. 더 긴 테스트를 실행하기 전에 성공 여부를 테스트하려면 약 5초 동안 짧은 테스트로 시작하는 것이 좋습니다.

이 SockPerf 예제에서는 평균 패킷에 일반적으로 사용되는 350바이트 메시지 크기를 사용합니다. 크기를 더 높거나 낮게 조정하여 VM에서 실행 중인 작업을 보다 정확하게 나타내는 결과를 달성할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [Azure 근접 배치 그룹](../virtual-machines/co-location.md)을 사용하여 대기 시간을 개선합니다.
* 시나리오를 위해 [VM에 대한 네트워킹 최적화](../virtual-network/virtual-network-optimize-network-bandwidth.md) 방법을 알아봅니다.
* [가상 머신에 대역폭이 할당되는 방법](../virtual-network/virtual-machine-network-throughput.md)에 대해 알아봅니다.
* 자세한 내용은 [Azure Virtual Network FAQ](../virtual-network/virtual-networks-faq.md)를 참조하세요.
