---
title: Azure Load Balancer의 새로운 기능
description: 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능 및 예정된 변경 내용과 같은 Azure Load Balancer의 새로운 기능을 알아봅니다.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147481"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure Load Balancer의 새로운 기능

Azure Load Balancer는 정기적으로 업데이트됩니다. 최신 공지 사항을 최신 상태로 유지합니다. 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능(해당하는 경우)

[여기](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)에서 최신 Azure Load Balancer 업데이트를 찾아 RSS 피드를 구독할 수도 있습니다.

## <a name="new-features"></a>새로운 기능

|기능  |Description  |추가된 날짜  |
|---------|---------|---------|
| IP 기반 백 엔드 풀 관리 지원(미리 보기) | Azure Load Balancer는 IPv4 또는 IPv6 주소를 통해 백 엔드 풀에서 리소스 추가 및 제거를 지원합니다. 이렇게 하면 Load Balancer와 연결된 컨테이너, 가상 머신 및 가상 머신 확장 집합을 쉽게 관리할 수 있습니다. 또한 관련 리소스를 만들기 전에 IP 주소를 백 엔드 풀의 일부로 예약할 수 있습니다. | 2020년 7월 |
| Azure Monitor를 사용하는 Azure Load Balancer Insights | 네트워크용 Azure Monitor의 일부로 빌드된 고객은 이제 Azure Portal에서 메트릭으로 미리 구성된 표준 Load Balancers용 모든 Load Balancer 구성과 상태 대시보드에 대한 토폴로지 맵을 보유하고 있습니다. [시작 및 자세한 정보](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020년 6월 |
| Azure Load Balancer에 대한 IPv6 지원(일반적으로 사용 가능) | Azure Load Balancers에 대한 프런트 엔드로 IPv6 주소를 사용할 수 있습니다. [여기에서 이중 스택 애플리케이션](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)을 만드는 방법에 대해 알아봅니다. |2020년 4월|
| 유휴 시간 제한 시 TCP 다시 설정(일반적으로 사용 가능)| TCP 다시 설정을 사용하여 보다 예측 가능한 애플리케이션 동작을 만듭니다. [자세히 알아보기](load-balancer-tcp-reset.md)| 2020년 2월 |

## <a name="next-steps"></a>다음 단계

Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer란?](load-balancer-overview.md) 및 [질문과 대답](load-balancer-faqs.md)을 참조하세요.