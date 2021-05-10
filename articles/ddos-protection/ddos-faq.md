---
title: Azure DDoS Protection 표준 질문과 대답
description: DDoS 공격에 대한 방어를 제공하는 데 유용한 Azure DDoS Protection 표준에 대한 질문과 대답입니다.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 89685d1b8c3a57fa142bbbfd1114f9aa0ff0c400
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98611120"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection 표준 질문과 대답

이 문서에서는 Azure DDoS Protection 표준에 대한 일반적인 질문과 답변을 제공합니다. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>DDoS(분산 서비스 거부) 공격이란?
분산 서비스 거부 또는 DDoS는 공격자가 애플리케이션이 처리할 수 있는 것보다 많은 요청을 애플리케이션에 전송하는 공격 유형입니다. 결과적으로 리소스가 고갈되어 애플리케이션의 가용성 및 고객 서비스 기능에 영향을 줍니다. 지난 몇 년 동안 업계에서는 공격이 점점 더 정교해지고 규모도 커지고 있습니다. 인터넷을 통해 공개적으로 도달 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Azure DDoS Protection 표준 서비스란?
애플리케이션 설계 모범 사례와 결합된 Azure DDoS Protection 표준은 DDoS 공격으로부터 방어하기 위해 향상된 DDoS 완화 기능을 제공합니다. 가상 네트워크에서 특정 Azure 리소스를 보호하도록 자동으로 조정됩니다. 보호는 새 가상 네트워크 또는 기존 가상 네트워크에서 간단하게 설정할 수 있으며 애플리케이션 또는 리소스를 변경할 필요가 없습니다. 로깅, 경고 및 원격 분석을 포함하여 기본 서비스에 비해 여러 가지 장점이 있습니다. 자세한 내용은 [Azure DDoS Protection 표준 개요](ddos-protection-overview.md)를 참조하세요. 

## <a name="how-does-pricing-work"></a>가격은 어떻게 책정됩니까?
DDoS Protection 플랜에는 최대 100개의 공용 IP 주소를 포함하는 월 $2,944의 고정 요금이 부과됩니다. 추가 리소스에 대한 보호에는 리소스당 월 $30가 추가로 부과됩니다. 

테넌트에서 단일 DDoS Protection 플랜을 여러 구독에서 사용할 수 있으므로 둘 이상의 DDoS Protection 플랜을 만들 필요가 없습니다.

자세한 내용은 [Azure DDoS Protection 표준 가격 책정](https://azure.microsoft.com/pricing/details/ddos-protection/)을 참조하세요.

## <a name="is-the-service-zone-resilient"></a>서비스 영역 복원력이 있습니까?
예. Azure DDoS Protection은 기본적으로 영역 복원력이 있습니다.

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>영역 복원력을 갖도록 서비스를 구성하려면 어떻게 해야 하나요?
영역 복원력을 사용하도록 설정하기 위한 고객 구성은 필요 없습니다. Azure DDoS Protection 리소스의 영역 복원력은 기본적으로 제공되며 서비스 자체에서 관리합니다.

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>서비스 계층(계층 7)의 보호는 어떤가요?
고객은 네트워크 계층(Azure DDoS Protection 표준에서 제공하는 레이어 3 및 4) 및 애플리케이션 계층(WAF에서 제공하는 계층 7)에서 보호를 위해 WAF(웹 애플리케이션 방화벽)와 함께 Azure DDoS Protection 서비스를 사용할 수 있습니다. WAF 제품에는 Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)와 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)에서 사용할 수 있는 타사 웹 애플리케이션 방화벽 제품이 포함됩니다.

## <a name="are-services-unsafe-in-azure-without-the-service"></a>이 서비스가 없으면 Azure에서 서비스가 안전하지 않나요?
Azure에서 실행되는 서비스는 기본적으로 Azure의 인프라를 보호하기 위해 배치된 Azure DDoS Protection 기본을 통해 보호됩니다. 그러나 인프라를 보호하는 보호 기능은 대부분의 애플리케이션에서 처리할 수 있는 것보다 훨씬 더 높은 임계값을 가지며, 원격 분석 또는 경고를 제공하지 않으므로, 트래픽 볼륨이 플랫폼에서는 무해한 것으로 인식될 수 있지만 수신하는 애플리케이션에 심각한 영향을 미칠 수 있습니다. 

애플리케이션은 Azure DDoS Protection 표준 서비스에 온보딩하여 공격 및 애플리케이션별 임계값을 감지하는 전용 모니터링을 얻습니다. 서비스는 예상되는 트래픽 볼륨에 맞게 조정되는 프로필을 사용하여 보호되며 DDoS 공격에 대해 훨씬 더 견고한 방어를 제공합니다.

## <a name="what-are-the-supported-protected-resource-types"></a>보호되는 지원 리소스 종류란?
ARM 기반 VNET의 공용 IP는 현재 보호되는 유일한 종류의 리소스입니다. PaaS 서비스(다중 테넌트)는 현재 지원되지 않습니다. 자세한 내용은 [Azure DDoS Protection 표준 참조 아키텍처](ddos-protection-reference-architectures.md)를 참조하세요.

## <a name="are-classicrdfe-protected-resources-supported"></a>클래식/RDFE 보호된 리소스가 지원되나요?
ARM 기반 보호된 리소스만 미리 보기에서 지원됩니다. 클래식/RDFE 배포의 VM은 지원되지 않습니다. 현재 클래식/RDFE 리소스에 대한 지원은 계획되지 않습니다. 자세한 내용은 [Azure DDoS Protection 표준 참조 아키텍처](ddos-protection-reference-architectures.md)를 참조하세요.

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>DDoS Protection을 사용하여 PaaS 리소스를 보호할 수 있나요?
다중 테넌트, 단일 VIP PaaS 서비스에 연결된 공용 IP는 현재 지원되지 않습니다. 지원되지 않는 리소스의 예로는 스토리지 VIP, 이벤트 허브 VIP 및 App/Cloud Services 애플리케이션이 있습니다. 자세한 내용은 [Azure DDoS Protection 표준 참조 아키텍처](ddos-protection-reference-architectures.md)를 참조하세요.

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>DDoS Protection을 사용하여 온-프레미스 리소스를 보호할 수 있나요?
Azure의 VNet에 연결된 서비스의 퍼블릭 엔드포인트에서 DDoS Protection을 사용하도록 설정해야 합니다. 예제 디자인은 다음과 같습니다.
- Azure의 웹 사이트(IaaS) 및 온-프레미스 데이터 센터의 백 엔드 데이터베이스 
- Azure의 Application Gateway(App Gateway/WAF에서 DDoS Protection이 사용하도록 설정됨) 및 온-프레미스 데이터 센터의 웹 사이트

자세한 내용은 [Azure DDoS Protection 표준 참조 아키텍처](ddos-protection-reference-architectures.md)를 참조하세요.

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Azure 외부에 도메인을 등록하고 VM 또는 ELB와 같은 보호된 리소스에 연결할 수 있나요?
공용 IP 시나리오의 경우 연결된 공용 IP가 Azure에서 호스트되는 한, DDoS Protection 서비스는 연결된 도메인이 등록되거나 호스트되는 위치에 관계 없이 모든 애플리케이션을 지원합니다. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Vnet/공용 IP에 적용된 DDoS 정책을 수동으로 구성할 수 있나요?
아니요, 아쉽게도 지금은 정책 사용자 지정을 사용할 수 없습니다.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>특정 IP 주소를 허용 목록/차단 목록에 포함할 수 있나요?
아니요, 아쉽게도 수동 구성은 현재 사용할 수 없습니다.

## <a name="how-can-i-test-ddos-protection"></a>DDoS Protection을 테스트하려면 어떻게 해야 하나요?
[시뮬레이션을 통해 테스트](test-through-simulations.md)를 참조하세요.

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Portal에서 메트릭이 로드되는 데 시간이 얼마나 걸리나요?
메트릭은 5분 이내에 Portal에 표시되어야 합니다. 리소스가 공격을 받고 있는 경우 다른 메트릭은 5-7분 내에 Portal에 표시되기 시작합니다. 

## <a name="does-the-service-store-customer-data"></a>서비스에서 고객 데이터를 저장하나요?
아니요, Azure DDoS Protection은 고객 데이터를 저장하지 않습니다.
    
