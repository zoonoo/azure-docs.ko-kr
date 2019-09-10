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
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "67182566"
---
| 리소스 | 기본 제한 |
| --- | :--- |
| 구독 당 최대 클러스터 수 | 100 |
| 클러스터당 최대 노드 수 | 100 |
| 노드당 최대 pod: Kubenet를 사용 하는 [기본 네트워킹][basic-networking] | 110 |
| 노드당 최대 pod: Azure 컨테이너 네트워킹 인터페이스를 사용 하 여 [고급 네트워킹][advanced-networking] | Azure CLI 배포: 30<sup>1</sup><br />Azure Resource Manager 템플릿: 30<sup>1</sup><br />포털 배포: 30 |

<sup>1</sup> Azure CLI 또는 리소스 관리자 템플릿을 사용 하 여 AKS (Azure Kubernetes Service) 클러스터를 배포 하는 경우이 값은 노드당 최대 250 pod로 구성할 수 있습니다. AKS 클러스터를 이미 배포한 후 또는 Azure Portal를 사용 하 여 클러스터를 배포 하는 경우 노드당 최대 pod을 구성할 수 없습니다.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
