---
title: Microsoft Azure 가상 네트워크에 대한 VPN 처리량 검증
description: 이 문서의 목적은 사용자가 온-프레미스 리소스에서 Azure 가상 머신으로의 네트워크 처리량을 유효성 검사하도록 돕는 것입니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: dcf86deda32069bf9711dbeb733dc9361e22a771
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631780"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>가상 네트워크에 대한 VPN 처리량의 유효성을 검사하는 방법

VPN Gateway 연결을 통해 Azure 내 Virtual Network와 온-프레미스 IT 인프라 사이에 안전한 프레미스 간 연결을 설정할 수 있습니다.

이 문서에서는 온-프레미스 리소스에서 Azure VM(가상 컴퓨터)으로의 네트워크 처리량을 유효성 검사하는 방법을 보여줍니다.

> [!NOTE]
> 이 문서는 일반적인 문제를 진단하고 해결하는 데 사용됩니다. 다음 정보를 사용하여 문제를 해결할 수 없는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

## <a name="overview"></a>개요

VPN Gateway 연결에는 다음 구성 요소가 포함됩니다.

* 온-프레미스 VPN [장치(검증된 VPN 장치](vpn-gateway-about-vpn-devices.md#devicetable)목록 보기)
* 공용 인터넷
* Azure VPN Gateway
* Azure VM

다음 다이어그램에서는 VPN을 통한 온-프레미스 네트워크와 Azure Virtual Network의 논리적 연결을 보여 줍니다.

![VPN을 사용한 고객 네트워크와 MSFT 네트워크의 논리적 연결](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>최대 예상 수신/송신 계산

1. 애플리케이션의 기준선 처리량 요구 사항을 결정합니다.
1. Azure VPN Gateway 처리량 제한을 결정합니다. 도움말은 [VPN 게이트웨이 정보의](vpn-gateway-about-vpngateways.md#gwsku)"게이트웨이 SCO" 섹션을 참조하십시오.
1. VM 크기에 대한 [Azure VM 처리량 지침](../virtual-machines/virtual-machines-windows-sizes.md)을 결정합니다.
1. ISP(인터넷 서비스 공급자) 대역폭을 결정합니다.
1. VM, VPN 게이트웨이 또는 ISP중 가장 적은 대역폭을 사용하여 예상 처리량을 계산합니다. 메가비트(/)로 8(8)로 나눈 값입니다.

계산된 처리량이 응용 프로그램의 기준 처리량 요구 사항을 충족하지 않는 경우 병목 현상으로 식별한 리소스의 대역폭을 늘려야 합니다. Azure VPN Gateway의 크기를 조정하려면 [Changing a gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)(게이트웨이 SKU 변경)를 참조하세요. 가상 머신의 크기를 조정하려면 [VM 크기 조정](../virtual-machines/virtual-machines-windows-resize-vm.md)을 참조하세요. 예상되는 인터넷 대역폭이 발생하지 않는 경우 ISP에 문의할 수도 있습니다.

> [!NOTE]
> VPN 게이트웨이 처리량은 모든 사이트 간\VNET-VNET 또는 지점 간 연결의 집계입니다.

## <a name="validate-network-throughput-by-using-performance-tools"></a>성능 도구를 사용하여 네트워크 처리량의 유효성 검사

테스트 중에 VPN 터널 처리량 포화가 발생하면 정확한 결과가 제공되지 않으므로 이 유효성 검사는 사용량이 많지 않은 시간에 수행해야 합니다.

이 테스트에 사용하는 도구는 Windows 및 Linux에서 둘 다 작동하고 클라이언트 및 서버 모드가 둘 다 있는 iPerf입니다. Windows VM의 경우 3Gbps로 제한됩니다.

이 도구는 디스크에 대한 읽기/쓰기 작업을 수행하지 않습니다. 종단 간에 자체 생성된 TCP 트래픽을 생성할 뿐입니다. 클라이언트와 서버 노드 간에 사용 가능한 대역폭을 측정하는 실험을 기반으로 통계를 생성합니다. 두 노드 간에 테스트할 때 한 노드는 서버 역할을 하고 다른 노드는 클라이언트 역할을 합니다. 이 테스트가 완료되면 노드의 역할을 반대로 하여 두 노드에서 업로드 및 다운로드 처리량을 모두 테스트하는 것이 좋습니다.

### <a name="download-iperf"></a>iPerf 다운로드

[iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip)를 다운로드하십시오. 자세한 내용은 [iPerf 설명서](https://iperf.fr/iperf-doc.php)를 참조하세요.

 > [!NOTE]
 > 이 문서에서 설명하는 타사 제품은 Microsoft와 무관한 회사에서 제조한 것입니다. Microsoft는 이러한 제품의 성능에 대한 어떠한 묵시적 또는 다른 형태의 보증도 하지 않습니다.

### <a name="run-iperf-iperf3exe"></a>iPerf(iperf3.exe) 실행

1. 트래픽을 허용하는 NSG/ACL 규칙을 사용하도록 설정합니다(Azure VM에서 공용 IP 주소 테스트의 경우).

1. 두 노드에서 모두 포트 5001에 대한 방화벽 예외를 사용하도록 설정합니다.

   **Windows:** 관리자 권한으로 다음 명령을 실행합니다.

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   테스트가 완료될 때 규칙을 제거하려면 이 명령을 실행합니다.

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure 리눅스:** Azure Linux 이미지에는 허용 방화벽이 있습니다. 포트를 수신 중인 애플리케이션이 있으면 트래픽이 통과할 수 있습니다. 보안 설정된 사용자 지정 이미지를 사용하려면 명시적으로 열린 포트가 필요할 수 있습니다. 일반적인 Linux OS 계층 방화벽에는 `iptables`, `ufw` 또는 `firewalld`가 포함됩니다.

1. 서버 노드에서 iperf3.exe가 추출된 디렉터리로 변경합니다. 그런 다음 서버 모드에서 iPerf를 실행하고 포트 5001에서 다음 명령으로 수신하도록 설정합니다.

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Port 5001은 사용자 환경의 특정 방화벽 제한을 고려하여 사용자 지정할 수 있습니다.

1. 클라이언트 노드에서 iperf 도구가 추출된 디렉터리로 변경하고 다음 명령을 실행합니다.

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   클라이언트는 포트 5001에서 30초 동안의 트래픽을 서버로 안내합니다. 플래그 '-P'는 서버 노드에 32개의 동시 연결을 만들고 있음을 나타냅니다.

   다음 화면에는 이 예제의 출력이 표시됩니다.

   ![출력](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (선택 사항) 테스트 결과를 유지하려면 이 명령을 실행합니다.

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. 이전 단계를 완료한 후 역할과 동일한 단계를 실행하여 서버 노드가 이제 클라이언트 노드가 되고 그 반대의 경우도 마찬가지입니다.

> [!Note]
> Iperf만이 유일한 도구는 아닙니다. [NTTTCP는 테스트를 위한 대체 솔루션입니다.](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)

## <a name="test-vms-running-windows"></a>Windows를 실행하는 테스트 VM

### <a name="load-latteexe-onto-the-vms"></a>VM에 라떼.exe 로드

[라떼exe의](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b) 최신 버전 다운로드

Latte.exe를 별도의 폴더에 넣는 것이 좋습니다.`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Windows 방화벽을 통해 라떼.exe 허용

수신기에서 Windows 방화벽에서 Latte.exe 트래픽이 도착할 수 있도록 허용 규칙을 만듭니다. 특정 TCP 포트인바운드를 허용하는 대신 전체 Latte.exe 프로그램을 이름으로 허용하는 것이 가장 쉽습니다.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>다음과 같은 Windows 방화벽을 통해 Latte.exe 허용

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

예를 들어 latte.exe를 "c:\tools" 폴더로 복사한 경우 이 명령이 됩니다.

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>대기 시간 테스트 실행

수신기에서 latte.exe를 시작합니다 (PowerShell이 아닌 CMD에서 실행) :

`latte -a <Receiver IP address>:<port> -i <iterations>`

약 65k 반복은 대표 결과를 반환하기에 충분합니다.

사용 가능한 포트 번호는 괜찮습니다.

VM에 IP 주소가 10.0.0.4인 경우 다음과 같이 보입니다.

`latte -c -a 10.0.0.4:5005 -i 65100`

SENDER에서 latte.exe 시작(PowerShell이 아닌 CMD에서 실행)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

결과 명령은 "클라이언트" 또는 보낸 사람이라는 것을 나타내기 위해 "-c"를 추가하지 않는 경우를 제외하고 수신기와 동일합니다.

`latte -c -a 10.0.0.4:5005 -i 65100`

결과를 기다립니다. VM이 얼마나 멀리 떨어져 있는지에 따라 완료하는 데 몇 분 정도 걸릴 수 있습니다. 더 긴 테스트를 실행하기 전에 성공을 테스트하기 위해 더 적은 반복으로 시작하는 것이 좋습니다.

## <a name="test-vms-running-linux"></a>리눅스를 실행하는 테스트 VM

[SockPerf를](https://github.com/mellanox/sockperf) 사용하여 VM을 테스트합니다.

### <a name="install-sockperf-on-the-vms"></a>VM에 양말 퍼프 설치

Linux VM(보낸 사람 및 수신기 모두)에서 다음 명령을 실행하여 VM에서 SockPerf를 준비합니다.

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - GIT 및 기타 유용한 도구 설치

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>우분투 - GIT 및 기타 유용한 도구를 설치

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>배쉬 - 모두

bash 명령줄에서(git이 설치되어 있다고 가정)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

만들기가 느리고 몇 분 정도 걸릴 수 있습니다.

`make`

설치가 빠릅니다.

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>VM에서 양말 퍼프 실행

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>설치 후 명령을 샘플링합니다. 서버/수신기 - 서버의 IP가 10.0.0.4라고 가정합니다.

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>클라이언트 - 서버의 IP가 10.0.0.4라고 가정합니다.

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> VM과 게이트웨이 사이에 처리량 테스트를 수행하는 동안 중간 홉(예: 가상 어플라이언스)이 없는지 확인합니다.
> 위의 iPERF/NTTTCP 테스트에서 결과가 좋지 않으면 다음 문서를 참조하여 문제의 근본 원인의 주요 원인을 이해하십시오.https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

특히, 이러한 테스트 동안 클라이언트와 서버에서 병렬로 수집된 패킷 캡처 추적(Wireshark/Network Monitor)을 분석하면 성능 저하를 평가하는 데 도움이 됩니다. 이러한 트레이스에는 패킷 손실, 높은 대기 시간, MTU 크기가 포함될 수 있습니다. 조각화, TCP 0 창, 순서가 바래지 조각 등입니다.

## <a name="address-slow-file-copy-issues"></a>느린 파일 복사 문제 처리

이전 단계(iPERF/NTTTCP/etc.)로 평가된 전체 처리량이 양호하더라도 Windows 탐색기를 사용하거나 RDP 세션을 드래그앤드롭할 때 파일 처리 속도가 느려질 수 있습니다. 일반적으로 이 문제의 원인은 다음 요소 중 하나이거나 둘 다에 해당합니다.

* Windows 탐색기 및 RDP와 같은 파일 복사 애플리케이션은 파일을 복사할 때 여러 스레드를 사용하지 않습니다. 성능을 개선하기 위해 [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx)와 같은 다중 스레드 파일 복사 애플리케이션을 통해 16개 또는 32개의 스레드를 사용하여 파일을 복사합니다. 리치카피에서 파일 복사의 스레드 번호를 변경하려면 **작업** > **복사 옵션** > **파일 복사를**클릭합니다.

   ![느린 파일 복사 문제](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > 모든 응용 프로그램이 동일한 것은 아니며 모든 응용 프로그램/프로세스가 모든 스레드를 사용하는 것은 아닙니다. 테스트를 실행하면 일부 스레드가 비어 있고 정확한 처리량 결과를 제공하지 못할 수 있습니다.
   > 응용 프로그램 파일 전송 성능을 확인하려면 응용 프로그램 또는 파일 전송의 최적의 처리량을 찾기 위해 스레드의 #을 연속적으로 늘리거나 줄임으로써 다중 스레드를 사용합니다.

* 부족한 VM 디스크 읽기/쓰기 속도. 자세한 내용은 [Azure Storage 문제 해결](../storage/common/storage-e2e-troubleshooting.md)을 참조하세요.

## <a name="on-premises-device-external-facing-interface"></a>온-프레미스 디바이스 외부 연결 인터페이스

Azure가 로컬 네트워크 게이트웨이의 VPN을 통해 도달하기를 원하는 온-프레미스 범위의 서브넷을 언급했습니다. 동시에 Azure의 VNET 주소 공간을 온-프레미스 장치에 정의합니다.

* **경로 기반 게이트웨이**: 경로 기반 VPN에 대한 정책 또는 트래픽 선택기는 임의(또는 와일드카드)로 구성됩니다.

* **정책 기반 게이트웨이**: 정책 기반 VPN은 온-프레미스 네트워크와 Azure VNet 간의 주소 접두사 조합을 기반으로 IPsec 터널을 통해 패킷을 암호화하고 직접 암호화합니다. 정책 또는 트래픽 선택기는 일반적으로 VPN 구성에서 액세스 목록으로 정의됩니다.

* **UsePolicyBased트래픽선택기** 연결: ("UsePolicyBasedTrafficSelectors"는 연결에서 $True Azure VPN 게이트웨이를 구성하여 온프레미스의 정책 기반 VPN 방화벽에 연결합니다. PolicyBasedTrafficSelectors를 사용하도록 설정하는 경우 VPN 장치에 온-프레미스 네트워크(로컬 네트워크 게이트웨이) 접두사와 Azure 가상 네트워크 접두사와의 모든 조합으로 정의된 일치하는 트래픽 선택기(임의의 것 대신)가 있는지 확인해야 합니다.

부적절한 구성으로 인해 터널 내의 연결이 자주 끊어지고 패킷이 떨어지며 처리량이 잘못되고 대기 시간이 지연될 수 있습니다.

## <a name="check-latency"></a>대기 시간 확인

다음 도구를 사용하여 대기 시간을 확인할 수 있습니다.

* 윈엠트르
* TCP추적루트
* `ping`(이러한 `psping` 도구는 RTT의 좋은 추정치를 제공할 수 있지만 모든 경우에 사용할 수는 없습니다.)

![대기 시간 확인](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

MS Network 백본을 입력하기 전에 홉에서 대기 시간이 급증하는 경우 인터넷 서비스 공급자를 통해 추가 조사를 진행할 수 있습니다.

"msn.net"내의 홉에서 크고 비정상적인 대기 시간 스파이크가 발견되면 추가 조사를 위해 MS 지원팀에 문의하십시오.

## <a name="next-steps"></a>다음 단계

자세한 정보 나 도움말을 보려면 다음 링크를 확인하십시오.

* [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
