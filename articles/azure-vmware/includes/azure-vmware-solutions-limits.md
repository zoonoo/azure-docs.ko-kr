---
title: Azure VMware 솔루션 제한
description: Azure VMware 솔루션 제한 사항.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622144"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

다음 표에서는 Azure VMware 솔루션에 대 한 최대 제한 사항을 설명 합니다.

| **리소스** | **제한** |
| :-- | :-- |
| 사설 클라우드 당 클러스터 | 12 |
| 클러스터 당 최소 노드 수 | 3 |
| 클러스터당 최대 노드 수 | 16 |
| 사설 클라우드 당 노드 수 | 64 |
| 사설 클라우드 당 vCenter | 1  |
| HCX 사이트 쌍 | 3 with Advanced edition, 10 Enterprise edition |
| AVS Express 경로 최대 연결 된 SDDCs | 4 |
| AVS Express 경로 portspeed | 10Gbps<br />사용 되는 가상 네트워크 게이트웨이는 실제 대역폭을 결정 합니다. 자세한 내용은 [express 경로 가상 네트워크 게이트웨이 정보](../../expressroute/expressroute-about-virtual-network-gateways.md) 를 참조 하세요. | 
| VWAN을 통해 노출 되는 공용 Ip | 100 |
| vSAN 용량 제한 | 사용 가능한 총 75% (SLA에 25% 사용 가능)  |

다른 VMware 특정 제한에 대해서는 [vmware 구성 최대 도구](https://configmax.vmware.com/)를 사용 하세요.
