---
title: 포함 파일
description: 포함 파일
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335063"
---
| 리소스 | 제한 |
| --- | :--- |
| 구독당 최대 클러스터 | 100 |
| 가상 시스템 가용성 집합 및 기본 로드 밸런서 SKU를 갖춘 클러스터당 최대 노드  | 100 |
| 가상 머신 스케일 세트 및 [표준 부하 분산 기 SKU를][standard-load-balancer] 갖춘 클러스터당 최대 노드 | 1000(노드 [풀당][node-pool]노드 100개) |
| 노드당 최대 포드: Kubenet을 위한 [기본 네트워킹][basic-networking] | 110 |
| 노드당 최대 포드: Azure 컨테이너 네트워킹 인터페이스를 사용하여 [고급 네트워킹][advanced-networking] | Azure CLI 배포: 30<sup>1</sup><br />Azure 리소스 관리자 템플릿: 30<sup>1</sup><br />포털 배포: 30 |

<sup>1개</sup> Azure CLI 또는 리소스 관리자 템플릿을 사용하여 AKS(Azure Kubernetes Service) 클러스터를 배포할 때 이 값은 노드당 최대 250개의 포드를 구성할 수 있습니다. AKS 클러스터를 이미 배포한 후 또는 Azure 포털을 사용하여 클러스터를 배포한 후에는 노드당 최대 포드를 구성할 수 없습니다.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
