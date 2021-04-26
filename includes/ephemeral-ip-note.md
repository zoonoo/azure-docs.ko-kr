---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529564"
---
> [!NOTE]
> Azure는 공용 IP 주소가 할당되지 않았거나 내부 기본 Azure Load Balancer의 백 엔드 풀에 있는 Azure Virtual Machines에 대한 임시 IP를 제공합니다. 임시 IP 메커니즘은 구성할 수 없는 아웃바운드 IP 주소를 제공합니다. 
>
>공용 IP 주소가 가상 머신에 할당되거나 아웃바운드 규칙이 있거나 없는 표준 Load Balancer의 백 엔드 풀에 가상 머신이 배치되면 임시 IP가 비활성화됩니다. [Azure Virtual Network NAT](../articles/virtual-network/nat-overview.md) 게이트웨이 리소스가 가상 머신의 서브넷에 할당되면 임시 IP가 비활성화됩니다.
>
> Azure의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결에 SNAT(원본 네트워크 주소 변환) 사용](../articles/load-balancer/load-balancer-outbound-connections.md)을 참조하세요.