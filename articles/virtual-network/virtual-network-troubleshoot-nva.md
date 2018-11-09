---
title: Azure의 네트워크 가상 어플라이언스 문제 해결 | Microsoft Docs
description: Azure의 네트워크 가상 어플라이언스 문제를 해결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 2a0f6b75c540f319848805e8a9bda7b166d5d709
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138662"
---
#  <a name="network-virtual-appliance-issues-in-azure"></a>Azure의 네트워크 가상 어플라이언스 문제

Microsoft Azure에서 타사 NVA(네트워크 가상 어플라이언스)를 사용하면 VM 또는 VPN 연결 문제와 오류가 발생할 수 있습니다. 이 문서에서는 NVA 구성에 대한 기본 Azure 플랫폼 요구 사항의 유효성을 검사하는 기본 단계를 제공합니다.

타사 NVA 및 Azure 플랫폼과의 통합에 대한 기술 지원은 NVA 공급 업체에서 제공합니다. NVA와 관련된 연결 또는 라우팅 문제가 있는 경우 직접 [NVA 공급 업체에 문의](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)해야 합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>NVA 공급업체와 문제를 해결하기 위한 검사 목록

- NVA VM 소프트웨어의 소프트웨어 업데이트
- 서비스 계정 설정 및 기능
- 트래픽을 NVA로 보내는 가상 네트워크 서브넷의 UDR(사용자 정의 경로)
- NVA의 트래픽을 보내는 가상 네트워크 서브넷의 UDR
- NVA 내의 라우팅 테이블 및 NVA(예를 들어 NIC1에서 NIC2로)
- NVA NIC를 추적하여 네트워크 트래픽 수신 및 전송 확인

## <a name="basic-troubleshooting-steps"></a>기본 문제 해결 단계

- 기본 구성 확인
- NVA 성능 확인
- 고급 네트워크 문제 해결

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Azure에서 NVA의 최소 구성 요구 사항 확인

NVA마다 Azure에서 올바르게 작동하기 위한 기본 구성 요구 사항이 있습니다. 다음 섹션에서는 이러한 기본 구성을 확인하는 단계를 제공합니다. 자세한 내용은 [NVA 공급업체에 문의](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)하세요.

**NVA에서 IP 전달이 사용되는지 확인**

Azure Portal 사용

1.  [Azure Portal](https://portal.azure.com)에서 NVA 리소스를 찾고, 네트워킹을 선택하고, 네트워크 인터페이스를 선택합니다.
2.  [네트워크 인터페이스] 페이지에서 IP 구성을 선택합니다.
3.  IP 전달을 사용하도록 설정합니다.

PowerShell 사용

1. PowerShell을 열고 Azure 계정에 로그인합니다.
2. 다음 명령을 실행합니다(대괄호 값을 자신의 정보로 대체).

        Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>  

3. **EnableIPForwarding** 속성을 확인합니다.
 
4. IP 전달을 사용하도록 설정되지 않은 경우 다음 명령을 실행하여 사용하도록 설정합니다.

          $nic2 = Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
          $nic2.EnableIPForwarding = 1
          Set-AzureRmNetworkInterface -NetworkInterface $nic2
          Execute: $nic2 #and check for an expected output:
          EnableIPForwarding   : True
          NetworkSecurityGroup : null

**트래픽을 NVA로 라우팅할 수 있는지 확인**

1. [Azure Portal](https://portal.azure.com)에서 **Network Watcher**를 열고 **다음 홉**을 선택합니다.
2. NVA로 트래픽을 리디렉션하도록 구성된 VM 및 다음 홉을 볼 대상 IP 주소를 지정합니다. 
3. NVA가 **다음 홉**으로 나열되지 않는 경우 Azure 경로 테이블을 확인하고 업데이트합니다.

**트래픽이 NVA에 도달할 수 있는지 확인**

1.  [Azure Portal](https://portal.azure.com)에서 **Network Watcher**를 열고 **IP 흐름 확인**을 선택합니다. 
2.  NVA의 VM 및 IP 주소를 지정한 다음, 트래픽이 NSG(네트워크 보안 그룹)에 의해 차단되는지 확인합니다.
3.  트래픽을 차단하는 NSG 규칙이 있는 경우 **효과적인 보안** 규칙에서 NSG를 찾은 다음, 트래픽이 통과할 수 있도록 NSG를 업데이트합니다. 그런 다음, **IP 흐름 확인**을 다시 실행하고 **연결 문제 해결**을 사용하여 VM에서 내부 또는 외부 IP 주소로의 TCP 통신을 테스트합니다.

**NVA와 VM이 예상 트래픽을 수신 대기하는지 확인**

1.  RDP 또는 SSH를 사용하여 NVA에 연결하고 다음 명령을 실행합니다.

    Windows의 경우:

        netstat -an

    Linux의 경우:

        netstat -an | grep -i listen
2.  NVA 소프트웨어에서 사용하는 TCP 포트가 결과에 나타나지 않으면 이 포트에 도달하는 트래픽에 응답하도록 NVA 및 VM에서 응용 프로그램을 구성해야 합니다. [필요에 따라 NVA 공급업체에 문의하세요](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>NVA 성능 확인

### <a name="validate-vm-cpu"></a>VM CPU 유효성 검사

CPU 사용량이 100%에 근접하면 네트워크 패킷 드롭에 영향을 주는 문제가 발생할 수 있습니다. VM은 Azure Portal에서 특정 시간대의 평균 CPU 사용량을 보고합니다. CPU가 급증하는 시간에 게스트 VM의 어떤 프로세스 때문에 CPU 사용량이 높아지는지 확인하고, 가능하다면 사용량을 줄입니다. 또한 VM 크기를 좀 더 큰 SKU로 조정하거나, 가상 머신 확장 집합의 경우 인스턴스 수를 늘리거나 CPU 사용량에 따라 자동으로 크기를 조정하도록 설정해야 합니다. 필요에 따라 이러한 문제에 대해 [NVA 공급업체에 지원을 요청](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)하세요.

### <a name="validate-vm-network-statistics"></a>VM 네트워크 통계 유효성 검사 

VM 네트워크 사용량이 급증하거나 특정 기간에 사용량이 많은 것으로 나타나면 VM의 SKU 크기를 늘려서 처리량을 늘릴 필요가 있습니다. 가속 네트워킹을 사용하여 VM을 배포할 수도 있습니다. 필요에 따라 NVA에서 가속 네트워킹 기능을 지원하는지 확인하려면 [NVA 공급 업체에 지원을 요청](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)하세요.

## <a name="advanced-network-administrator-troubleshooting"></a>고급 네트워크 관리자 문제 해결

### <a name="capture-network-trace"></a>네트워크 추적 캡처
**[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** 또는 **Nmap**을 실행하는 동안 원본 VM, NVA 및 대상 VM에서 동시 네트워크 추적을 캡처한 다음, 추적을 중지합니다.

1. 동시 네트워크 추적을 캡처하려면 다음 명령을 실행합니다.

    Windows의 경우:

        netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

    Linux의 경우:

        sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. 원본 VM에서 대상 VM으로 **PsPing** 또는 **Nmap**을 사용합니다(예: `PsPing 10.0.0.4:80` 또는 `Nmap -p 80 10.0.0.4`).

3. [네트워크 모니터](https://www.microsoft.com/download/details.aspx?id=4865) 또는 tcpdump를 사용하여 대상 VM에서 네트워크 추적을 엽니다. `IPv4.address==10.0.0.4 (Windows netmon)` 또는 `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4`처럼(Linux인 경우) **PsPing** 또는 **Nmap**을 실행한 원본 VM의 IP에 대한 디스플레이 필터를 적용합니다.

### <a name="analyze-traces"></a>추적 분석

백 엔드 VM 추적으로 들어오는 패킷이 보이지 않으면 NSG 또는 UDR 방해가 있거나 NVA 라우팅 테이블이 잘못된 것일 수 있습니다.

들어오는 패킷이 표시되지만 응답하지 않으면 VM 응용 프로그램 또는 방화벽 문제를 해결해야 합니다. 필요에 따라 이러한 문제에 대해 [NVA 공급업체에 지원을 요청](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)하세요.

