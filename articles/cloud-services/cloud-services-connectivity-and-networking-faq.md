---
title: "Microsoft Azure Cloud Services FAQ에 대한 연결 및 네트워킹 문제 | Microsoft Docs"
description: "이 문서는 Microsoft Azure Cloud Services의 연결 및 네트워킹에 대한 질문과 대답을 나열합니다."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 3fc0d34fdb617ebb1af9c9f33e018d5fe6ec9a7d
ms.contentlocale: ko-kr
ms.lasthandoff: 06/15/2017

---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services의 연결 및 네트워킹 문제: FAQ(질문과 대답)

이 문서는 [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)의 연결 및 네트워킹에 대한 질문과 대답을 포함합니다. 크기 정보는 [클라우드 서비스 VM 크기 페이지](cloud-services-sizes-specs.md) 를 참조할 수도 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>여러 VIP 클라우드 서비스에서 IP를 예약할 수 없습니다.
우선, IP를 예약하려고 하는 가상 컴퓨터 인스턴스가 켜져 있는지 확인합니다. 다음으로 스테이징 및 프로덕션 배포 모두에 대해 예약된 IP를 사용해야 합니다. **않습니다** .

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>NSG가 있을 때 원격 데스크톱을 어떻게 수행하나요?
포트 **3389** 및 **20000**에서 트래픽을 허용하는 규칙을 NSG에 추가합니다.  원격 데스크톱은 포트 **3389**를 사용합니다.  클라우드 서비스 인스턴스에서 부하가 분산되므로 연결할 인스턴스를 직접 제어할 수 없습니다.  *RemoteForwarder* 및 *RemoteAccess* 에이전트가 RDP 트래픽을 관리하고 클라이언트에서 RDP 쿠키를 전송하고 연결할 개별 인스턴스를 지정할 수 있도록 합니다.  *RemoteForwarder* 및 *RemoteAccess* 에이전트를 사용하려면 포트 **20000**이 열려 있어야 합니다. 이 포트는 NSG가 있으면 차단될 수 있습니다.

## <a name="can-i-ping-a-cloud-service"></a>클라우드 서비스를 ping할 수 있나요?

아니요. 일반적인 "ping"/ICMP 프로토콜을 사용하면 안됩니다. Azure Load Balancer를 통해 ICMP 프로토콜을 사용할 수 없습니다.

연결을 테스트하려면 포트 ping을 수행하는 것이 좋습니다. Ping.exe가 ICMP를 사용하는 반면 PSPing, Nmap, 텔넷 등 기타 도구를 사용하면 특정 TCP 포트에 대한 연결을 테스트할 수 있습니다.

자세한 내용은 [ICMP 대신 포트 ping을 사용하여 Azure VM 연결 테스트](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/)를 참조하세요.

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>알 수 없는 IP 주소로부터 클라우드 서비스에 대한 악의적 공격을 의미하는 수 천 번의 적중을 수신하지 않도록 방지하려면 어떻게 할까요?
Azure는 DDoS(distributed denial-of-service) 공격에 대해 플랫폼 서비스를 보호하기 위해 다중 계층 네트워크 보안을 구현합니다. Azure DDoS 방어 시스템은 Azure의 연속 모니터링 프로세스의 일부이며 침투 테스트를 통해 지속적으로 개선됩니다. 이 DDoS 방어 시스템은 외부에서의 공격뿐만 아니라 다른 Azure 테넌트에서의 공격에 대응하도록 설계되었습니다. 자세한 내용은 [Microsoft Azure 네트워크 보안](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)을 참조하세요.

또한 일부 특정 IP 주소를 차단하는 시작 작업을 만들 수 있습니다. 자세한 내용은 [특정 IP 주소 차단](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)을 참조하세요.

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>클라우드 서비스 인스턴스에 RDP하려는 경우 "사용자 계정이 만료되었습니다."라는 메시지가 표시됩니다.
RDP 설정에 구성된 만료 날짜를 바이패스하면 "이 사용자 계정이 만료되었습니다."라는 오류 메시지가 표시될 수 있습니다. 다음과 같은 단계를 수행하여 포털에서 만료 날짜를 변경할 수 있습니다.
1. Azure 관리 콘솔(https://manage.windowsazure.com)에 로그인하여 클라우드 서비스를 찾아 **구성** 탭을 선택합니다.
2. **원격**을 선택합니다.
3. "만료" 날짜를 변경하고 구성을 저장합니다.

이제 컴퓨터에 RDP할 수 있습니다.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>LoadBalancer가 트래픽을 동일하게 분산하지 않는 이유는 무엇인가요?
내부 부하 분산 장치 작동 방법에 대한 정보는 [Azure Load Balancer 새 배포 모드](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)를 참조하세요.

사용되는 배포 알고리즘은 트래픽을 사용 가능한 서버에 매핑하는 5개 튜플(소스 IP, 소스 포트, 대상 IP, 대상 포트, 프로토콜 종류) 해시입니다. 전송 세션 내에서만 연결이 유지됩니다. 동일한 TCP 또는 UDP 세션의 패킷은 부하 분산 끝점 뒤의 동일한 DIP(데이터 센터 IP) 인스턴스로 전송됩니다. 클라이언트가 연결을 닫았다가 다시 열거나 동일한 원본 IP에서 새 세션을 시작하는 경우 원본 포트가 변경되어 트래픽이 다른 DIP 끝점으로 이동합니다.

