---
title: Azure 응용 프로그램 보안 그룹 개요
titlesuffix: Azure Virtual Network
description: 응용 프로그램 보안 그룹을 사용 하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 775ef92a0ca486d1f8a6c44c78a4df04cd5ef467
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78274711"
---
# <a name="application-security-groups"></a>애플리케이션 보안 그룹

애플리케이션 보안 그룹을 사용하면 네트워크 보안을 애플리케이션 구조의 자연 확장으로 구성하여 가상 머신을 그룹화하고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다. 명시적 IP 주소를 수동으로 유지 관리하지 않고 대규모 보안 정책을 재사용할 수 있습니다. 플랫폼은 명시적 IP 주소 및 여러 규칙 집합의 복잡성을 처리하여 비즈니스 논리에 집중할 수 있도록 합니다. 애플리케이션 보안 그룹에 대해 보다 정확하게 이해하려면 다음 예제를 잘 살펴보세요.

![애플리케이션 보안 그룹](./media/security-groups/application-security-groups.png)

이전 그림에서 *NIC1* 및 *NIC2*는 *AsgWeb* 애플리케이션 보안 그룹의 멤버입니다. *NIC3*는 *AsgLogic* 애플리케이션 보안 그룹의 멤버입니다. *NIC4*는 *AsgDb* 애플리케이션 보안 그룹의 멤버입니다. 이 예제의 각 네트워크 인터페이스는 한 애플리케이션 보안 그룹의 멤버이긴 하지만, 네트워크 인터페이스는 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 내에서 여러 애플리케이션 보안 그룹의 멤버가 될 수 있습니다. 어떤 네트워크 인터페이스에도 네트워크 보안 그룹이 연결되지 않았습니다. *NSG1*은 두 서브넷에 연결되었으며 다음 규칙을 포함하고 있습니다.

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

이 규칙은 인터넷에서 웹 서버로 가는 트래픽을 허용하기 위해 필요합니다. 인터넷의 인바운드 트래픽을 **DenyAllInbound** 기본 보안 규칙에서 거부하기 때문에 *AsgLogic* 또는 *AsgDb* 애플리케이션 보안 그룹에 대한 규칙을 추가하지 않아도 됩니다.

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 100 | 인터넷 | * | AsgWeb | 80 | TCP | 허용 |

## <a name="deny-database-all"></a>Deny-Database-All

**AllowVNetInBound** 기본 보안 규칙은 동일한 가상 네트워크의 리소스 간 통신을 모두 허용하므로, 모든 리소스에서 들어오는 트래픽을 거부하려면 이 규칙이 필요합니다.

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | 모두 | 거부 |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

이 규칙은 *AsgLogic* 애플리케이션 보안 그룹에서 *AsgDb* 애플리케이션 보안 그룹으로 가는 트래픽을 허용합니다. 이 규칙의 우선 순위는 *Deny-Database-All* 규칙의 우선 순위보다 높습니다. 결과적으로 이 규칙이 *Deny-Database-All* 규칙보다 먼저 처리되므로 *AsgLogic* 애플리케이션 보안 그룹의 트래픽은 허용되는 반면, 그 외의 트래픽은 모두 차단됩니다.

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | 허용 |

원본 또는 대상으로 애플리케이션 보안 그룹을 지정하는 규칙은 애플리케이션 보안 그룹의 멤버인 네트워크 인터페이스에만 적용됩니다. 네트워크 인터페이스가 애플리케이션 보안 그룹의 멤버가 아닌 경우에는 네트워크 보안 그룹이 서브넷과 연결되어도 규칙이 네트워크 인터페이스에 적용되지 않습니다.

애플리케이션 보안 그룹에는 다음과 같은 제약 사항이 있습니다.

-    한 구독에 허용되는 애플리케이션 보안 그룹의 개수 제한 및 애플리케이션 보안 그룹과 관련된 기타 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.
- 하나의 애플리케이션 보안 그룹을 보안 규칙의 원본 및 대상으로 지정할 수 있습니다. 원본 또는 대상에는 여러 애플리케이션 보안 그룹을 지정할 수 없습니다.
- 애플리케이션 보안 그룹에 할당된 모든 네트워크 인터페이스는 애플리케이션 보안 그룹에 할당된 첫 번째 네트워크 인터페이스가 있는 가상 네트워크와 동일한 가상 네트워크에 있어야 합니다. 예를 들어 *AsgWeb*라는 애플리케이션 보안 그룹에 할당된 첫 번째 네트워크 인터페이스가 *VNet1*이라는 가상 네트워크에 있는 경우 *ASGWeb*에 할당되는 모든 후속 네트워크 인터페이스는 *VNet1*에 있어야 합니다. 서로 다른 가상 네트워크의 네트워크 인터페이스를 동일한 애플리케이션 보안 그룹에 추가할 수 없습니다.
- 애플리케이션 보안 그룹을 보안 규칙의 원본 및 대상으로 지정하는 경우, 두 애플리케이션 보안 그룹의 네트워크 인터페이스는 동일한 가상 네트워크에 있어야 합니다. 예를 들어 *AsgLogic*에 *VNet1*의 네트워크 인터페이스, *AsgDb*에 *VNet2*의 네트워크 인터페이스가 포함되어 있는 경우 규칙에서 *AsgLogic*을 원본으로, *AsgDb*를 대상으로 할당할 수 없습니다. 원본 및 대상 애플리케이션 보안 그룹에 대한 모든 네트워크 인터페이스는 동일한 가상 네트워크에 있어야 합니다.

> [!TIP]
> 필요한 보안 규칙 수를 최소화하고 규칙 변경을 최소화하려면 되도록이면 개별 IP 주소 또는 IP 주소 범위 대신 서비스 태그 또는 애플리케이션 보안 그룹을 사용하여 필요한 애플리케이션 보안 그룹에 대한 계획을 세우고 규칙을 만드세요.

## <a name="next-steps"></a>다음 단계

* [네트워크 보안 그룹을 만드는](tutorial-filter-network-traffic.md)방법에 대해 알아봅니다.
