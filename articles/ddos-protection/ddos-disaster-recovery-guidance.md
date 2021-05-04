---
title: Azure DDoS Protection Standard 비즈니스 연속성 | Microsoft Docs
description: Azure DDoS Protection Standard에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730389"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard – 비즈니스 연속성

Azure DDoS Protection Standard의 비즈니스 연속성 및 재해 복구를 사용하면 중단이 발생했을 때도 비즈니스를 계속 운영할 수 있습니다. 이 문서에서는 (지역 내) 가용성 및 재해 복구를 자세히 설명합니다.

## <a name="overview"></a>개요
Azure DDoS Protection Standard는 가상 네트워크에서 공용 IP 주소를 보호합니다. 보호는 새 가상 네트워크 또는 기존 가상 네트워크에서 간단하게 설정할 수 있으며 애플리케이션이나 리소스를 변경하지 않아도 됩니다.

Virtual Network(VNet)는 클라우드의 사용자 네트워크를 논리적으로 나타내는 표현입니다. VNet은 Azure Application Gateway, Azure Firewall. Azure Virtual Machines 같은 리소스를 호스팅하는 신뢰 경계 역할을 합니다. VNet은 지역 범위 내에서 생성됩니다. 서로 다른 두 지역(예: 미국 동부 및 미국 서부)에서 동일한 주소 공간을 사용하여 VNet을 ‘만들’ 수는 있지만, 같은 주소 공간을 공유하므로 함께 연결할 수는 없습니다. 

## <a name="business-continuity"></a>비즈니스 연속성

여러 가지 방법으로 애플리케이션이 중단될 수 있습니다. 지역은 자연 재해 또는 여러 디바이스 또는 서비스의 장애로 인한 부분 재해 때문에 완전히 차단될 수 있습니다. 보호받는 VNet에 미치는 영향은 상황에 따라 다릅니다.

**Q: 자연 재해 때문에 지역이 완전히 차단되는 경우처럼 전체 지역의 가동 중단이 발생하면 어떻게 해야 하나요? 지역에 호스트된 가상 네트워크는 어떻게 되나요?**

A: 영향을 받은 지역의 가상 네트워크와 리소스는 서비스 중단 시간 동안 액세스할 수 없습니다.

![간단한 가상 네트워크 다이어그램입니다.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**Q: 다른 지역에 동일한 가상 네트워크를 다시 만들려면 어떻게 해야 하나요?**

A: 가상 네트워크는 비교적 경량의 리소스입니다. Azure API를 호출하여 다른 지역에 동일한 주소 공간을 갖는 VNet을 만들 수 있습니다. 영향을 받은 지역에 존재하던 동일한 환경을 다시 만들려면 사용하던 가상 머신을 다시 VNet에 배포하는 API 호출을 수행해야 합니다. 하이브리드 배포와 같은 온-프레미스 연결이 있는 경우 새 VPN Gateway를 배포하고 온-프레미스 네트워크에 연결해야 합니다.

가상 네트워크를 만들려면 [가상 네트워크 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요.

**Q: 지정된 지역의 VNet 복제본을 미리 다른 지역에 다시 만들 수 있나요?**

A: 예. 사전에 두 개의 서로 다른 지역에서 동일한 개인 IP 주소 공간 및 리소스를 사용하여 두 개의 Vnet을 만들 수 있습니다. VNet에 인터넷 연결 서비스를 호스트하는 경우 활성 지역에 트래픽을 지리적으로 라우팅하도록 Traffic Manager를 설정할 수 있습니다. 그러나 라우팅 문제를 발생할 수 있으므로 동일한 주소 공간을 가진 두 Vnet을 온-프레미스 네트워크에 연결할 수 없습니다. 한 지역에서 재해 및 VNet 손실이 발생할 경우 주소 공간이 온-프레미스 네트워크의 주소 공간과 일치하는 사용 가능한 지역의 다른 VNet을 연결할 수 있습니다.

가상 네트워크를 만들려면 [가상 네트워크 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 계획을 생성](manage-ddos-protection.md)하는 방법을 알아봅니다.