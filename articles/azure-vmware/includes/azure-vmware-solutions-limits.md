---
title: Azure VMware Solution 제한
description: Azure VMware Solution 제한 사항.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582738"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

다음 표에서는 Azure VMware Solution의 최대 제한에 대해 설명합니다.

| **리소스** | **제한** |
| :-- | :-- |
| 프라이빗 클라우드당 클러스터 수 | 12 |
| 클러스터당 최소 노드 수 | 3 |
| 클러스터당 최대 노드 수 | 16 |
| 프라이빗 클라우드당 노드 수 | 96 |
| 프라이빗 클라우드당 vCenter | 1  |
| HCX 사이트 페어링 | 3개(고급 버전 포함), 10개(엔터프라이즈 버전 포함) |
| AVS ExpressRoute 최대 연결 SDDC | 4 |
| AVS ExpressRoute 포트 속도 | 10Gbps<br />사용되는 가상 네트워크 게이트웨이는 실제 대역폭을 결정합니다. 자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이 정보](../../expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요. | 
| vWAN을 통해 노출되는 공용 IP | 100 |
| vSAN 용량 제한 | 총 가용의 75%(SLA에 25% 사용 가능)  |

다른 VMware 특정 제한에 대해서는 [VMware 구성 최대 도구](https://configmax.vmware.com/)를 사용하세요.
