---
title: "Azure DDoS 보호 표준 개요 | Microsoft Docs"
description: "Azure DDoS 보호 서비스에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b0fa58c8bf0e50314c61272eb221878eca2c3def
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS 보호 표준 개요

>[!IMPORTANT]
>Azure DDoS 보호 표준은 현재 미리 보기로 제공됩니다. 제한된 수의 Azure 리소스가 선택한 수의 지역에서 DDoS 보호 표준을 지원합니다. 제한된 미리 보기 동안 구독에 대해 DDoS 보호 표준을 사용하도록 설정하려면 [서비스에 등록](http://aka.ms/ddosprotection)해야 합니다. 등록 시 Azure DDoS 팀에서 사용 설정 프로세스를 안내하기 위해 연락합니다. DDoS 보호 표준은 미국 동부, 미국 서부 및 미국 중부 지역에서 사용할 수 있습니다. 미리 보기 중에는 이 서비스 사용 요금이 청구되지 않습니다.

DDoS(분산 서비스 거부) 공격은 고객이 응용 프로그램을 클라우드로 전환하게 만드는 가장 큰 가용성 및 보안 문제 중 하나입니다. DDoS 공격은 응용 프로그램의 리소스를 소진시켜서 정상적인 사용자가 응용 프로그램을 사용할 수 없게 만듭니다. 인터넷을 통해 공개적으로 도달 가능한 모든 끝점은 DDoS 공격의 대상이 될 수 있습니다.

응용 프로그램 설계 모범 사례와 결합된 Azure DDoS 보호는 이러한 공격에 대한 방어 기능을 제공합니다. 이러한 두 가지 서비스 계층이 제공됩니다. 

- **Azure DDoS 보호 기본** - 추가 비용 없이 Azure 플랫폼의 일부로 제공되며, 이미 자동으로 사용하도록 설정되어 있습니다. 일반적인 네트워크 수준 공격에 대한 항시 트래픽 모니터링과 실시간 완화는 Microsoft의 온라인 서비스에서 활용하는 것과 동일한 방어를 제공합니다.  Azure 글로벌 네트워크의 전체 규모를 활용하여 전체 지역에서 공격 트래픽을 분산하고 완화할 수 있습니다. 
- **Azure DDoS 보호 표준** - 가상 네트워크 리소스에 맞게 특별히 조정된 추가적인 완화 기능을 제공합니다. 간단히 활성화할 수 있고 응용 프로그램을 변경할 필요가 없습니다. 보호 정책은 전용 트래픽 모니터링 및 기계 학습 알고리즘을 통해 조정되며, Load Balancer, Application Gateway 및 Service Fabric 인스턴스와 같은 Virtual Network 리소스와 연결된 공용 IP에 적용됩니다.  공격을 받고 있을 때 기록을 위해 Azure Monitor 뷰를 통해 실시간 원격 분석을 사용할 수 있습니다. [Application Gateway 웹 응용 프로그램 방화벽](https://azure.microsoft.com/services/application-gateway/)으로 응용 프로그램 계층 보호를 추가할 수 있습니다. 

![Azure DDoS 보호 표준](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

개발 및 테스트 시나리오의 경우 DDoS 보호 표준을 사용해 보고 다음과 같은 리소스를 사용하여 자신의 경험에 대한 의견을 제공해 주시면 감사하겠습니다.
- [Microsoft Azure 포럼의 Azure DDoS 보호](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [MSDN 포럼의 Azure DDoS 보호](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection).
- [Stack Overflow의 Azure DDos 보호](https://stackoverflow.com/tags/azure-ddos/info)

지원 문제의 경우 [Azure 지원 티켓](../azure-supportability/how-to-create-azure-support-request.md)을 열면 됩니다.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS 보호 표준으로 완화되는 DDoS 공격의 유형

DDoS 보호 표준은 이러한 유형의 공격을 완화할 수 있습니다.

- **대규모 공격** - 이 공격의 목표는 정상적으로 보이는 대량의 트래픽으로 네트워크 계층을 마비시키는 것입니다. 여기에는 UDP 서비스 장애, 증폭 서비스 장애 및 기타 스푸핑된 패킷 서비스 장애가 포함됩니다. DDoS 보호 표준은 자동으로 Azure의 글로벌 네트워크 규모를 활용하여 이러한 잠재적인 멀티 기가바이트 공격을 흡수하고 스크럽하여 완화시킵니다. 
- **프로토콜 공격** - 이러한 공격은 계층 3 및 계층 4 프로토콜 스택의 취약점을 악용하여 대상을 액세스 불능 상태로 만듭니다. SYN 서비스 장애 공격, 리플렉션 공격 및 기타 프로토콜 공격이 여기에 포함됩니다. DDoS 보호 표준은 클라이언트와의 상호 작용을 통해 악성 트래픽과 정상 트래픽을 구분하고 악성 트래픽을 차단하여 이러한 공격을 완화합니다. 
- **응용 프로그램 계층 공격** - 이러한 공격은 대상 웹 응용 프로그램 패킷을 공격하여 호스트 간 데이터 전송을 방해합니다. HTTP 프로토콜 위반, SQL 주입, 사이트 간 스크립팅 및 기타 계층 7 공격이 여기에 포함됩니다. DDoS 보호 표준과 Application Gateway WAF를 함께 사용하여 이러한 공격을 방어하세요. 

DDoS 보호 표준은 VM과 연결된 공용 IP, 내부 부하 분산 장치 및 응용 프로그램 게이트웨이를 비롯한 가상 네트워크의 리소스를 보호합니다. Application Gateway WAF SKU와 함께 사용할 경우 DDoS 보호 표준은 L3~L7의 완화 기능을 온전히 제공할 수 있습니다.

## <a name="ddos-protection-standard-features"></a>DDoS 보호 표준 기능

![DDoS 기능](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS 보호 표준 기능에는 다음이 포함됩니다. 

- **원시 플랫폼 통합:** DDoS 보호 표준은 기본적으로 Azure에 통합되어 있고, Azure Portal 및 PowerShell을 통해 구성을 포함합니다. DDoS 보호 표준은 사용자의 리소스 및 리소스 구성을 인식합니다.
- **트래픽 항시 모니터링:** DDoS 공격의 징후를 찾기 위해 응용 프로그램 트래픽 패턴이 항시 모니터링됩니다. 보호 정책이 초과되면 완화가 수행됩니다.
- **턴키 보호:** 간소화된 구성으로 DDoS 보호 표준이 사용되는 즉시 가상 네트워크의 모든 리소스를 바로 보호할 수 있습니다. 개입이나 사용자 정의가 필요 없습니다. DDoS 보호 표준은 공격이 감지되는 즉시 자동으로 공격을 완화시킵니다.
- **적응형 튜닝:** 지능형 트래픽 프로파일링으로 시간별 응용 프로그램 트래픽을 학습하고, 사용자의 서비스에 가장 적합한 프로필을 업데이트합니다. 트래픽이 시간이 지남에 따라 변경되면서 프로필이 조정됩니다.
- **응용 프로그램 게이트웨이를 사용한 L3~L7 보호:** Application Gateway WAF는 전체 스택 DDoS 보호 기능을 제공합니다.
- **광범위한 완화 규모:** 가장 큰 규모로 알려진 DDoS 공격으로부터 시스템을 보호할 수 있는 글로벌 역량으로 60가지 공격을 완화할 수 있습니다. 
- **공격 메트릭:** Azure Monitor를 통해 각 공격으로부터 요약된 메트릭에 액세스할 수 있습니다.
- **공격 경고:** 기본 제공되는 공격 메트릭을 사용하여 공격 시작 및 중지 시, 그리고 공격이 진행되는 동안 경고를 구성할 수 있습니다. 경고는 OMS, Splunk, Azure Storage, 전자 메일 및 Azure Portal과 같은 운영 소프트웨어에 통합됩니다.
- **비용 보장:** 문서화된 DDoS 공격에 대한 데이터 전송 및 응용 프로그램 확장 서비스 크레딧이 제공됩니다.

## <a name="ddos-protection-standard-mitigation"></a>DDoS 보호 표준 완화

Microsoft의 DDoS 보호 서비스는 실제 트래픽 사용률을 모니터링하고 이를 DDoS 정책에 정의된 임계값과 지속적으로 비교합니다. 트래픽 임계값이 초과되면 DDoS 완화가 자동으로 시작됩니다. 트래픽이 임계값 아래로 돌아오면 완화가 제거됩니다.

완화 중에 보호되는 리소스로 들어오는 트래픽은 DDoS 보호 서비스를 통해 리디렉션되며 여러 검사가 수행됩니다. 이러한 검사는 일반적으로 다음 기능을 수행합니다.

- 패킷이 인터넷 사양을 준수하고 잘못된 형식이 아닌지 확인합니다.
- 클라이언트와 상호 작용하여 스푸핑된 패킷일 가능성이 있는지 확인합니다(예: SYN Auth 또는 SYN 쿠키 또는 원본에서 다시 전송하도록 패킷 삭제).
- 다른 적용 방법을 수행할 수 없는 경우 패킷 속도를 제한합니다.

DDoS 보호는 공격 트래픽을 차단하고 나머지 트래픽을 원하는 대상으로 전달합니다. 공격이 감지되면 몇 분 안에 Azure Monitor 메트릭을 통해 알림이 전송됩니다. DDoS 보호 표준 원격 분석에 대한 로깅을 구성하면 추후 분석에 사용할 수 있는 옵션에 로그를 작성할 수 있습니다. DDoS 보호 표준을 위한 Azure Monitor의 메트릭 데이터는 현재 30일 동안 보존됩니다.

자신이 받은 DDoS 공격을 시뮬레이션하는 것은 권장되지 않습니다. 대신에 지원 채널을 사용하여 Azure 네트워킹으로 수행된 DDoS 공격 시뮬레이션을 요청할 수 있습니다. 엔지니어가 연락해서 DDoS 고격의 세부 정보(포트, 프로토콜, 대상 IP)를 물어보고 테스트를 수행할 시간을 정할 것입니다.

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell](ddos-protection-manage-ps.md) 또는 [Azure Portal](ddos-protection-manage-portal.md)을 사용하여 DDoS 보호 표준을 관리하는 방법을 자세히 알아봅니다.
