---
title: 포함 파일
description: 포함 파일
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318313"
---
### <a name="virtual-networks"></a>Virtual Network

|  |  |
|---------|---------|
| [허용되는 가상 네트워크 게이트웨이 SKU](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | 애플리케이션 게이트웨이에서 승인된 SKU를 사용해야 합니다. 승인된 SKU 배열을 지정합니다. |
| [허용되는 vNet 게이트웨이 SKU](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | 가상 네트워크 게이트웨이는 승인된 SKU를 사용해야 합니다. 승인된 SKU 배열을 지정합니다. |
| [허용되는 Load Balancer SKU](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | 가상 네트워크 부하 분산 장치는 승인된 SKU를 사용해야 합니다. 승인된 SKU 배열을 지정합니다. |
| [Express Route 네트워크에 대한 네트워크 피어링 없음](../articles/governance/policy/samples/no-peering-express-route-network.md) | 네트워크 피어링이 지정된 리소스 그룹의 한 네트워크에 연결되는 것을 금지합니다. 중앙 관리 네트워크 인프라와의 연결을 예방하기 위해 사용합니다. 연결을 예방하기 위해 리소스 그룹의 이름을 지정합니다. |
| [사용자 정의 경로 테이블 없음](../articles/governance/policy/samples/no-user-defined-route-table.md)  | 가상 네트워크가 사용자 정의 경로 테이블과 함께 배포되는 것을 금지합니다. |
| [모든 서브넷에서 NSG X](../articles/governance/policy/samples/nsg-on-subnet.md) | 특정 네트워크 보안 그룹이 모든 가상 서브넷과 함께 사용되어야 합니다. 사용할 네트워크 보안 그룹의 ID를 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 서브넷 사용](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | 네트워크 인터페이스는 승인된 서브넷을 사용해야 합니다. 승인된 서브넷의 ID를 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 VNet 사용](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | 네트워크 인터페이스는 승인된 가상 네트워크를 사용해야 합니다. 승인된 가상 네트워크 ID를 지정합니다. |