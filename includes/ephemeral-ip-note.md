---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 03080e4518fdc1997740dd156135202dfc24f778
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179879"
---
> [!NOTE]
> Azure는 공용 IP 주소가 할당되지 않았거나 내부 기본 Azure Load Balancer의 백 엔드 풀에 있는 Azure Virtual Machines에 대한 기본 아웃바운드 액세스 IP를 제공합니다. 기본 아웃바운드 액세스 IP 메커니즘은 구성할 수 없는 아웃바운드 IP 주소를 제공합니다. 
>
>공용 IP 주소가 가상 머신에 할당되거나 아웃바운드 규칙이 있거나 없는 표준 Load Balancer의 백엔드 풀에 가상 머신이 배치되면 기본 아웃바운드 IP가 비활성화됩니다. [Azure Virtual Network NAT](../articles/virtual-network/nat-gateway/nat-overview.md) 게이트웨이 리소스가 가상 머신의 서브넷에 할당되면 기본 아웃바운드 액세스 IP가 비활성화됩니다.
>
> Azure의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결에 SNAT(원본 네트워크 주소 변환) 사용](../articles/load-balancer/load-balancer-outbound-connections.md)을 참조하세요.