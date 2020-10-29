---
title: Azure DDoS Protection 기본적인 모범 사례
description: DDoS 보호를 사용 하는 최상의 보안 방법에 대해 알아봅니다.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 594a7e2a6977cc2a7052a15e1a007c68c08da259
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905311"
---
# <a name="fundamental-best-practices"></a>기본 모범 사례

다음 섹션에서는 Azure에서 DDoS 복원 서비스를 빌드하기 위한 규범적 지침을 제공합니다.

## <a name="design-for-security"></a>보안을 위해 설계

디자인 및 구현에서 배포 및 작업까지 애플리케이션의 전체 수명 주기에서 보안을 최우선으로 고려해야 합니다. 애플리케이션에는 과도하게 많은 리소스를 사용하여 결국 서비스 중단으로 이어지게 만드는 비교적 적은 볼륨의 요청을 허용하는 버그가 있을 수 있습니다. 

Microsoft Azure에서 실행되는 서비스를 보호하기 위해 애플리케이션 아키텍처를 정확하게 이해하고 [소프트웨어 품질의 5가지 핵심 요소](/azure/architecture/guide/pillars)에 집중해야 합니다.
일반적인 트래픽 볼륨, 애플리케이션과 다른 애플리케이션 간의 연결 모델, 공용 인터넷에 노출되는 서비스 엔드포인트를 알아야 합니다.

애플리케이션 자체를 대상으로 하는 서비스 거부 공격을 처리할 수 있도록 애플리케이션의 복원력을 높이는 것이 가장 중요합니다. [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx)부터 보안 및 개인 정보 보호 기능이 Azure 플랫폼에 기본 제공됩니다. SDL은 모든 개발 단계에서 보안 문제를 해결하고 Azure를 지속적으로 업데이트하여 더욱 안전하게 보호합니다.

## <a name="design-for-scalability"></a>확장성을 위한 디자인

확장성은 시스템이 증가된 로드를 처리할 수 있는 정도입니다. 증폭되는 부하, 특히 DDoS 공격 시 증폭되는 부하 수요를 충족할 수 있도록 애플리케이션이 [수평으로 확장 가능](/azure/architecture/guide/design-principles/scale-out)하도록 설계합니다. 애플리케이션이 서비스의 단일 인스턴스에 종속된 경우 단일 실패 지점이 생깁니다. 여러 인스턴스를 프로비전하면 시스템에 복원성 및 확장성이 증가하게 됩니다.

[Azure App Service](/azure/app-service/app-service-value-prop-what-is)의 경우 여러 인스턴스를 제공하는 [App Service 계획](/azure/app-service/overview-hosting-plans)을 선택합니다. Azure Cloud Services의 경우 각각의 역할을 [여러 인스턴스](/azure/cloud-services/cloud-services-choose-me)를 사용하도록 구성합니다. [Azure Virtual Machines](../virtual-machines/index.yml)의 경우, VM(가상 머신) 아키텍처가 둘 이상의 VM을 포함하는지 그리고 각각의 VM이 [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md)에 포함되는지 확인합니다. 자동 크기 조정 기능을 사용 하려면 [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md) 을 사용 하는 것이 좋습니다.

## <a name="defense-in-depth"></a>심층 방어

심층 방어의 개념은 다양한 방어 전략을 사용하여 위험을 관리하는 것입니다. 애플리케이션에서 보안 방어를 계층화하면 공격이 성공할 가능성이 줄어듭니다. Azure 플랫폼의 기본 제공 기능을 사용하여 안전한 애플리케이션 디자인을 구현하는 것이 좋습니다.

예를 들어 공격 위험이 증가하면 애플리케이션의 크기( *노출 영역* )도 커집니다. 승인 목록을 사용 하 여 표시 되는 IP 주소 공간과 부하 분산 장치 ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) 및 [Azure 애플리케이션 Gateway](/azure/application-gateway/application-gateway-create-probe-portal))에서 필요 하지 않은 수신 포트를 닫아 노출 영역을 줄일 수 있습니다. [NSG(네트워크 보안 그룹)](/azure/virtual-network/security-overview)는 공격 노출을 줄이기 위한 또 다른 방법입니다.
[서비스 태그](/azure/virtual-network/security-overview#service-tags) 및 [애플리케이션 보안 그룹](/azure/virtual-network/security-overview#application-security-groups)을 사용하여 보안 규칙을 만드는 복잡성을 최소화하고 애플리케이션 구조의 기본 확장으로 네트워크 보안을 구성할 수 있습니다.

가능하면 [가상 네트워크](/azure/virtual-network/virtual-networks-overview)에 Azure 서비스를 배포해야 합니다. 이렇게 하면 서비스 리소스가 개인 IP 주소를 통해 통신할 수 있습니다. 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다. [서비스 엔드포인트](/azure/virtual-network/virtual-network-service-endpoints-overview)를 사용하던 기존 방식에서 서비스 트래픽이 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 프라이빗 주소를 원본 IP 주소로 사용하도록 전환됩니다.

종종 고객의 온-프레미스 리소스가 Azure의 해댱 리소스와 함께 공격당하는 모습을 볼 수 있습니다. 온-프레미스 환경을 Azure에 연결하는 경우 공용 인터넷에 노출되는 온-프레미스 리소스를 최소화하는 것이 좋습니다. Azure에서 잘 알려진 공용 엔터티를 배포하여 Azure의 크기 조정 및 고급 DDoS Protection 기능을 사용할 수 있습니다. 이러한 공개적으로 액세스 가능한 엔터티는 종종 DDoS 공격의 대상이 되기 때문에 Azure에 배치하면 온-프레미스 리소스에 미치는 영향을 줄일 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 계획을 만드는](manage-ddos-protection.md)방법에 대해 알아봅니다.