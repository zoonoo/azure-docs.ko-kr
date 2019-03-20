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
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554636"
---
| 리소스 | 기본 제한 |
| --- | :--- |
| 구독 당 최대 클러스터 | 100 |
| 클러스터당 최대 노드 | 100 |
| 노드당 최대 포드: Kubenet을 사용하는 [기본 네트워킹][basic-networking] | 110 |
| 노드당 최대 포드: [고급 네트워킹이] [ advanced-networking] Azure 컨테이너 네트워킹 인터페이스를 사용 하 여 | Azure CLI 배포: 30<sup>1</sup><br />Azure Resource Manager 템플릿: 30<sup>1</sup><br />포털 배포: 30 |

<sup>1</sup>Azure CLI 또는 Resource Manager 템플릿을 사용 하 여 Azure Kubernetes Service (AKS) 클러스터를 배포 하는 경우이 값은 노드당 110 pod까지 구성할 수 있습니다. AKS 클러스터를 이미 배포한 후 또는 Azure portal을 사용 하 여 클러스터를 배포 하는 경우에 노드당 최대 포드를 구성할 수 없습니다.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
