---
title: 포함 파일
description: 포함 파일
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400295"
---
| 리소스 | 기본 제한 |
| --- | :--- |
| 구독당 최대 클러스터 | 100 |
| 클러스터당 최대 노드 | 100 |
| 노드당 최대 포드: Kubenet과 [기본 네트워킹][basic-networking] | 110 |
| 노드당 최대 포드: Azure CNI와 [고급 네트워킹][advanced-networking] | Azure CLI 배포: 30<sup>1</sup><br />Resource Manager 템플릿: 30<sup>1</sup><br />포털 배포: 30 |

<sup>1</sup> Azure CLI 또는 Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포할 때 이 값을 **노드당 Pod 110개**까지 구성할 수 있습니다. AKS 클러스터를 이미 배포한 경우 또는 Azure Portal을 사용하여 클러스터를 배포하는 경우에는 노드당 Pod 수를 최댓값까지 구성할 수 없습니다.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
