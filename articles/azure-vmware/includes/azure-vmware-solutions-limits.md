---
title: Azure VMware 솔루션 제한
description: Azure VMware 솔루션 제한 사항.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193810"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

다음 표에서는 Azure VMware 솔루션에 대 한 최대 제한 사항을 설명 합니다.

| **리소스** | **제한** |
| :-- | :-- |
| 사설 클라우드 당 클러스터 | 4 |
| 클러스터 당 최소 노드 수 | 3 |
| 클러스터당 최대 노드 수 | 16 |
| 사설 클라우드 당 노드 수 | 64 |
| 사설 클라우드 당 vCenter | 1  |
| HCX 사이트 쌍 | 3 with Advanced edition, 10 Enterprise edition |
| AVS Express 경로 최대 연결 된 SDDCs | 4 |
| AVS Express 경로 portspeed | 10Gbps | 
| VWAN을 통해 노출 되는 공용 Ip | 100 |
| vSAN 용량 제한 | 사용 가능한 총 75% (SLA에 25% 사용 가능)  |

다른 VMware 특정 제한에 대해서는 [vmware 구성 최대 도구](https://configmax.vmware.com/)를 사용 하세요.
