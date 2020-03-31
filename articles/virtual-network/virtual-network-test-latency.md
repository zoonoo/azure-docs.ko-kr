---
title: Azure 가상 네트워크에서 Azure 가상 시스템 네트워크 대기 시간 테스트 | 마이크로 소프트 문서
description: 가상 네트워크에서 Azure 가상 컴퓨터 간의 네트워크 대기 시간을 테스트하는 방법 알아보기
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
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896370"
---
# <a name="test-vm-network-latency"></a>VM 네트워크 대기 시간 테스트

가장 정확한 결과를 얻으려면 작업에 맞게 설계된 도구를 사용하여 Azure 가상 시스템(VM) 네트워크 대기 시간을 측정합니다. SockPerf(Linux용) 및 latte.exe(Windows용)와 같은 공개적으로 사용 가능한 도구는 응용 프로그램 대기 시간과 같은 다른 유형의 대기 시간을 제외하면서 네트워크 대기 시간을 격리하고 측정할 수 있습니다. 이러한 도구는 응용 프로그램 성능에 영향을 주는 네트워크 트래픽의 종류(즉, 전송 제어 프로토콜 [TCP] 및 사용자 데이터그램 프로토콜 [UDP] 트래픽)에 중점을 둡니다. 

Ping과 같은 다른 일반적인 연결 도구는 대기 시간을 측정할 수 있지만 결과는 실제 워크로드에 사용되는 네트워크 트래픽을 나타내지 않을 수 있습니다. 이러한 도구의 대부분은 응용 프로그램 트래픽과 다르게 처리될 수 있으며 TCP 및 UDP를 사용하는 워크로드에는 결과가 적용되지 않을 수 있는 ICMP(인터넷 제어 메시지 프로토콜)를 사용하기 때문입니다. 

대부분의 응용 프로그램에서 사용하는 프로토콜의 정확한 네트워크 대기 시간 테스트를 위해 SockPerf(Linux용) 및 latte.exe(Windows용)는 가장 관련성이 좋은 결과를 생성합니다. 이 문서에서는 이러한 두 가지 도구를 다룹니다.

## <a name="overview"></a>개요

두 개의 VM(하나는 보낸 사람으로, 다른 하나는 수신자로)을 사용하여 양방향 통신 채널을 만들 수 있습니다. 이 방법을 사용하면 양방향으로 패킷을 보내고 받을 수 있으며 왕복 시간(RTT)을 측정할 수 있습니다.

이 방법을 사용하여 두 VM 간의 네트워크 대기 시간을 측정하거나 두 물리적 컴퓨터 간에 도모할 수 있습니다. 대기 시간 측정은 다음 시나리오에 유용할 수 있습니다.

- 배포된 VM 간의 네트워크 대기 시간에 대한 벤치마크를 설정합니다.
- 관련 변경 이후에 네트워크 대기 시간 변경의 영향을 다음과 비교하여 비교합니다.
  - 구성 변경을 포함한 운영 체제(OS) 또는 네트워크 스택 소프트웨어.
  - 가용성 영역 또는 근접 배치 그룹(PPG)에 배포하는 것과 같은 VM 배포 방법입니다.
  - 가속 네트워킹 또는 크기 변경과 같은 VM 속성입니다.
  - 라우팅 또는 필터링 변경과 같은 가상 네트워크입니다.

### <a name="tools-for-testing"></a>테스트를 위한 도구
대기 시간을 측정하려면 두 가지 도구 옵션이 있습니다.

* Windows 기반 시스템의 경우: [latte.exe(Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* 리눅스 기반 시스템의 경우: [삭퍼프 (리눅스)](https://github.com/mellanox/sockperf)

이러한 도구를 사용하면 TCP 또는 UDP 페이로드 배달 시간만 측정되고 ICMP(Ping) 또는 응용 프로그램에서 사용되지 않고 성능에 영향을 주지 않는 다른 패킷 유형이 아닌지 확인할 수 있습니다.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>최적의 VM 구성을 만들기 위한 팁

VM 구성을 만들 때 다음 권장 사항을 염두에 두어야 합니다.
- 최신 버전의 Windows 또는 Linux를 사용합니다.
- 최상의 결과를 위해 가속 네트워킹을 활성화합니다.
- [Azure 근접 배치 그룹으로](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)VM을 배포합니다.
- 일반적으로 VM이 클수록 더 작은 VM보다 성능이 우수합니다.

### <a name="tips-for-analysis"></a>분석을 위한 팁

테스트 결과를 분석할 때 다음 권장 사항을 염두에 두어야 합니다.

- 배포, 구성 및 최적화가 완료되는 즉시 기준을 조기에 설정합니다.
- 항상 새 결과를 기준선에 비교하거나, 그렇지 않으면 한 테스트에서 다른 테스트로 제어된 변경 내용을 사용하여 비교합니다.
- 변경 사항이 관찰되거나 계획될 때마다 테스트를 반복합니다.


## <a name="test-vms-that-are-running-windows"></a>Windows를 실행 중인 테스트 VM

### <a name="get-latteexe-onto-the-vms"></a>VM에 라떼.exe 받기

[latte.exe의 최신 버전을](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)다운로드합니다.

*c:\도구와*같은 별도의 폴더에 latte.exe를 넣는 것이 좋습니다.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>윈도우 디펜더 방화벽을 통해 latte.exe 허용

*수신기에서*Windows Defender 방화벽에서 latte.exe 트래픽이 도착할 수 있도록 허용 규칙을 만듭니다. 특정 TCP 포트인바운드를 허용하는 대신 전체 latte.exe 프로그램을 이름으로 허용하는 것이 가장 쉽습니다.

다음 명령을 실행하여 Windows Defender 방화벽을 통해 latte.exe 허용:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

예를 들어 *c:\도구* 폴더에 latte.exe를 복사한 경우 이 명령이 됩니다.

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>대기 시간 테스트 실행

* *수신기에서*latte.exe를 시작합니다 (PowerShell이 아닌 CMD 창에서 실행하십시오).

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    약 65,000번의 반복은 대표 결과를 반환하기에 충분합니다.

    사용 가능한 포트 번호는 괜찮습니다.

    VM에 IP 주소가 10.0.0.4인 경우 명령은 다음과 같습니다.

    `latte -a 10.0.0.4:5005 -i 65100`

* 보낸 *사람은*latte.exe를 시작합니다 (PowerShell이 아닌 CMD 창에서 실행하십시오).

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    결과 명령은 클라이언트 *또는* *보낸 사람이라는*것을 나타내기 위해&nbsp;*-c를* 추가하지 않는 경우를 제외하고 수신기에서와 동일합니다.

    `latte -c -a 10.0.0.4:5005 -i 65100`

결과를 기다립니다. VM이 얼마나 멀리 떨어져 있는지에 따라 테스트를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 더 긴 테스트를 실행하기 전에 성공을 테스트하기 위해 더 적은 반복으로 시작하는 것이 좋습니다.

## <a name="test-vms-that-are-running-linux"></a>Linux를 실행하는 테스트 VM

Linux를 실행 중인 VM을 테스트하려면 [SockPerf](https://github.com/mellanox/sockperf)를 사용합니다.

### <a name="install-sockperf-on-the-vms"></a>VM에 양말 퍼프 설치

Linux VM에서 *보낸 사람* 및 *수신자*모두 다음 명령을 실행하여 VM에서 SockPerf를 준비합니다. 명령은 주요 배포체에 대해 제공됩니다.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>레드 햇 엔터프라이즈 리눅스 (RHEL)/CentOS에 대 한

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

#### <a name="for-ubuntu"></a>우분투용

다음 명령을 실행합니다.

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>모든 배포체에 대 한

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

### <a name="run-sockperf-on-the-vms"></a>VM에서 양말 퍼프 실행

SockPerf 설치가 완료되면 VM은 대기 시간 테스트를 실행할 준비가 됩니다. 

첫째, *수신기에*SockPerf를 시작합니다.

사용 가능한 포트 번호는 괜찮습니다. 이 예제에서는 포트 12345를 사용합니다.

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

이제 서버가 수신 대기 중이되었으므로 클라이언트는 수신 대기 중인 포트의 서버로 패킷을 보내기 시작할 수 있습니다(이 경우 12345).

다음 예제와 같이 약 100초가 길어 대표 결과를 반환할 수 있습니다.

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

결과를 기다립니다. VM의 간격에 따라 반복 횟수가 달라집니다. 더 긴 테스트를 실행하기 전에 성공을 테스트하려면 약 5초의 짧은 테스트로 시작하는 것이 좋습니다.

이 SockPerf 예제에서는 평균 패킷에 대 한 일반적인 350 바이트 메시지 크기를 사용 합니다. 크기를 더 높거나 낮게 조정하여 VM에서 실행 중인 워크로드를 보다 정확하게 나타내는 결과를 얻을 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [Azure 근접 배치 그룹으로](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)대기 시간을 개선합니다.
* 시나리오에 [맞게 VM에 대한 네트워킹을 최적화하는](../virtual-network/virtual-network-optimize-network-bandwidth.md) 방법을 알아봅니다.
* [대역폭이 가상 시스템에 할당되는 방법에](../virtual-network/virtual-machine-network-throughput.md)대해 읽어보십시오.
* 자세한 내용은 [Azure 가상 네트워크 FAQ를](../virtual-network/virtual-networks-faq.md)참조하십시오.
