---
title: Azure virtual network에서 Azure 가상 머신 네트워크 대기 시간 테스트 | Microsoft Docs
description: 가상 네트워크에서 Azure virtual machines 간의 네트워크 대기 시간을 테스트 하는 방법 알아보기
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
ms.openlocfilehash: 77ea14097538f722569acb5a0371674776aac8e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84687806"
---
# <a name="test-vm-network-latency"></a>VM 네트워크 대기 시간 테스트

가장 정확한 결과를 얻으려면 작업을 위해 설계 된 도구를 사용 하 여 Azure VM (가상 머신) 네트워크 대기 시간을 측정 합니다. SockPerf (Linux 용) 및 latte.exe (Windows 용)와 같은 공개적으로 사용 가능한 도구는 응용 프로그램 대기 시간과 같은 다른 유형의 대기 시간을 제외 하 고 네트워크 대기 시간을 분리 하 고 측정할 수 있습니다. 이러한 도구는 응용 프로그램 성능에 영향을 주는 네트워크 트래픽 종류 (즉, TCP (전송 제어 프로토콜) 및 UDP (사용자 데이터 그램 프로토콜) 트래픽)에 중점을 둡니다. 

Ping과 같은 다른 일반적인 연결 도구는 대기 시간을 측정할 수 있지만 실제 작업에서 사용 되는 네트워크 트래픽을 표시 하지 않을 수 있습니다. 이러한 도구의 대부분은 ICMP (Internet Control Message Protocol)를 사용 하기 때문입니다. ICMP는 응용 프로그램 트래픽과 다르게 처리할 수 있으며 그 결과는 TCP 및 UDP를 사용 하는 작업에 적용 되지 않을 수 있습니다. 

대부분의 응용 프로그램에서 사용 하는 프로토콜의 정확한 네트워크 대기 시간 테스트를 위해 SockPerf (Linux) 및 latte.exe (Windows 용)에서 가장 관련성이 높은 결과를 생성 합니다. 이 문서에서는 이러한 도구를 모두 다룹니다.

## <a name="overview"></a>개요

두 Vm을 발신자로, 하나는 수신자로 사용 하 여 양방향 통신 채널을 만듭니다. 이 접근 방식을 사용 하면 양방향으로 패킷을 보내고 받을 수 있으며 RTT (왕복 시간)를 측정할 수 있습니다.

이 방법을 사용 하 여 두 Vm 간의 네트워크 대기 시간을 측정 하거나 두 개의 물리적 컴퓨터 간에 네트워크 대기 시간을 측정할 수 있습니다. 대기 시간 측정은 다음과 같은 경우에 유용할 수 있습니다.

- 배포 된 Vm 간의 네트워크 대기 시간에 대 한 벤치 마크를 설정 합니다.
- 관련 변경 내용이 적용 된 후 네트워크 대기 시간 변경의 영향을 비교 합니다.
  - 운영 체제 (OS) 또는 네트워크 스택 소프트웨어 (구성 변경 포함)
  - VM 배포 방법 (예: 가용성 영역에 배포 또는 PPG (근접 배치 그룹)).
  - 가속 네트워킹 또는 크기 변경과 같은 VM 속성
  - 라우팅 또는 필터링 변경 등의 가상 네트워크

### <a name="tools-for-testing"></a>테스트용 도구
대기 시간을 측정 하기 위한 두 가지 도구 옵션은 다음과 같습니다.

* Windows 기반 시스템: [latte.exe (windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Linux 기반 시스템: [SockPerf (linux)](https://github.com/mellanox/sockperf)

이러한 도구를 사용 하 여 TCP 또는 UDP 페이로드 배달 시간만 측정 되 고 ICMP (Ping) 또는 응용 프로그램에서 사용 되지 않으며 성능에 영향을 주지 않는 다른 패킷 유형이 아닌 것을 확인할 수 있습니다.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>최적의 VM 구성 만들기에 대 한 팁

VM 구성을 만들 때 다음 권장 사항을 염두에 두어야 합니다.
- 최신 버전의 Windows 또는 Linux를 사용 합니다.
- 최상의 결과를 위해 가속 네트워킹을 사용 하도록 설정 합니다.
- [Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)을 사용 하 여 vm을 배포 합니다.
- 일반적으로 더 큰 Vm은 더 작은 Vm 보다 성능이 우수 합니다.

### <a name="tips-for-analysis"></a>분석 팁

테스트 결과를 분석 하는 동안 다음과 같은 권장 사항을 염두에 두어야 합니다.

- 배포, 구성 및 최적화가 완료 되는 즉시 초기 계획을 수립 합니다.
- 항상 새 결과를 기준선과 비교 하거나, 제어 된 변경 내용이 있는 한 테스트에서 다른 테스트로 비교 합니다.
- 변경 내용이 관찰 되거나 계획 될 때마다 테스트를 반복 합니다.


## <a name="test-vms-that-are-running-windows"></a>Windows를 실행 하는 Vm 테스트

### <a name="get-latteexe-onto-the-vms"></a>Vm에 대 한 latte.exe 가져오기

[최신 버전의 latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)을 다운로드 합니다.

*C:\tools*와 같이 별도의 폴더에 latte.exe을 배치 하는 것이 좋습니다.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Windows Defender 방화벽을 통한 latte.exe 허용

*수신기*에서 Windows Defender 방화벽에 대 한 허용 규칙을 만들어 latte.exe 트래픽이 도착할 수 있도록 허용 합니다. 특정 TCP 포트 인바운드를 허용 하는 대신 이름으로 전체 latte.exe 프로그램을 허용 하는 것이 가장 쉽습니다.

다음 명령을 실행 하 여 Windows Defender 방화벽을 통해 latte.exe 수 있습니다.

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

예를 들어 latte.exe을 *c:\tools* 폴더에 복사한 경우 다음 명령이 수행 됩니다.

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>대기 시간 테스트 실행

* *받는 사람*에서 latte.exe를 시작 합니다 (PowerShell이 아닌 CMD 창에서 실행).

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    65000 회 반복은 대표적인 결과를 반환할 만큼 길어야 합니다.

    사용 가능한 포트 번호는 모두 충분 합니다.

    VM의 IP 주소가 10.0.0.4 인 경우 명령은 다음과 같습니다.

    `latte -a 10.0.0.4:5005 -i 65100`

* *보낸 사람*에서 latte.exe를 시작 합니다 (PowerShell이 아닌 CMD 창에서 실행).

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    결과 명령은 &nbsp; *-c* *client*를 추가 하는 것을 제외 하 고는 *받는 사람*에서와 동일 합니다.

    `latte -c -a 10.0.0.4:5005 -i 65100`

결과를 기다립니다. Vm이 얼마나 떨어져 있는지에 따라 테스트를 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 더 긴 테스트를 실행 하기 전에 성공 여부를 테스트 하려면 더 짧은 반복으로 시작 하는 것이 좋습니다.

## <a name="test-vms-that-are-running-linux"></a>Linux를 실행 하는 Vm 테스트

Linux를 실행 하는 Vm을 테스트 하려면 [SockPerf](https://github.com/mellanox/sockperf)을 사용 합니다.

### <a name="install-sockperf-on-the-vms"></a>Vm에 SockPerf 설치

*발신자* 와 *수신자*모두 Linux vm에서 다음 명령을 실행 하 여 Vm에 대 한 SockPerf를 준비 합니다. 주요 배포판에 대해 명령이 제공 됩니다.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>For Red Hat Enterprise Linux (RHEL)/CentOS

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

#### <a name="for-ubuntu"></a>Ubuntu 용

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

다음 단계에 따라 SockPerf를 복사, 컴파일 및 설치 합니다.

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

### <a name="run-sockperf-on-the-vms"></a>Vm에서 SockPerf 실행

SockPerf 설치가 완료 되 면 Vm은 대기 시간 테스트를 실행할 준비가 된 것입니다. 

먼저 *수신자*에 대해 SockPerf를 시작 합니다.

사용 가능한 포트 번호는 모두 충분 합니다. 이 예제에서는 포트 12345을 사용 합니다.

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

이제 서버가 수신 대기 중 이므로 클라이언트는 수신 대기 중인 포트 (이 경우 12345)에서 서버로 패킷을 전송 하기 시작할 수 있습니다.

다음 예에 표시 된 것 처럼 약 100 초는 대표적인 결과를 반환할 만큼 짧습니다.

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

결과를 기다립니다. Vm이 얼마나 떨어져 있는지에 따라 반복 횟수는 달라 집니다. 더 긴 테스트를 실행 하기 전에 성공 여부를 테스트 하려면 약 5 초 동안 짧은 테스트로 시작 하는 것이 좋습니다.

이 SockPerf 예제에서는 평균 패킷에 일반적으로 사용 되는 350 바이트 메시지 크기를 사용 합니다. 크기를 더 높거나 낮게 조정 하 여 Vm에서 실행 중인 작업을 보다 정확 하 게 나타내는 결과를 달성할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)을 사용 하 여 대기 시간을 개선 합니다.
* 시나리오를 위해 [vm에 대 한 네트워킹을 최적화](../virtual-network/virtual-network-optimize-network-bandwidth.md) 하는 방법을 알아봅니다.
* [가상 컴퓨터에 대역폭을 할당 하는 방법을](../virtual-network/virtual-machine-network-throughput.md)참조 하세요.
* 자세한 내용은 [Azure VIRTUAL NETWORK FAQ](../virtual-network/virtual-networks-faq.md)를 참조 하세요.
