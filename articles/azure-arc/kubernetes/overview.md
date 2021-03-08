---
title: Azure Arc 지원 Kubernetes 개요
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: 이 문서에서는 Azure Arc 지원 Kubernetes 개요를 제공합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.custom: references_regions
ms.openlocfilehash: b3296aa1c1d5d463cc7f4cb932b44ad76632e19e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121595"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes란?

Azure Arc 지원 Kubernetes를 사용하면 Azure 내부 또는 외부에 있는 Kubernetes 클러스터를 연결하고 구성할 수 있습니다. Kubernetes 클러스터를 Azure Arc에 연결하면 다음과 같이 됩니다.
* Azure Resource Manager ID 및 관리 ID와 함께 Azure Portal에 표시됩니다. 
* Azure 구독 및 리소스 그룹에 배치됩니다.
* 다른 Azure 리소스와 마찬가지로 태그를 수신합니다. 

Kubernetes 클러스터를 Azure에 연결하려면 클러스터 관리자가 에이전트를 배포해야 합니다. 이러한 에이전트는 다음과 같습니다.
* 표준 Kubernetes 배포로 `azure-arc` Kubernetes 네임스페이스에서 실행합니다.
* Azure에 대한 연결 처리
* Azure Arc 로그 및 메트릭을 수집합니다.
* 구성 요청을 감시합니다. 

Azure Arc 지원 Kubernetes는 전송 중인 데이터를 보호하기 위해 업계 표준 SSL을 지원합니다. 또한, 데이터는 기밀 유지를 위해 Azure Cosmos DB 데이터베이스에 암호화된 상태로 유지됩니다.

## <a name="supported-kubernetes-distributions"></a>지원되는 Kubernetes 배포

Azure Arc 지원 Kubernetes는 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터에서 작동합니다. Azure Arc 팀은 Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 배포의 [적합성을 검증하기 위해 주요 업계 파트너](./validation-program.md)와 협력했습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오 

Azure Arc 지원 Kubernetes는 다음과 같은 시나리오를 지원합니다. 

* 인벤토리, 그룹화 및 태그 지정을 위해 Azure 외부에서 실행되는 Kubernetes를 연결합니다.

* GitOps 기반 구성 관리를 사용하여 애플리케이션을 배포하고 구성을 적용합니다. 

* 컨테이너에 대한 Azure Monitor를 사용하여 클러스터를 확인 및 모니터링합니다. 

* Kubernetes에 대한 Azure Policy를 사용하여 정책을 적용합니다. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>지원되는 지역 

현재 Azure Arc 지원 Kubernetes는 다음 지역에서 지원됩니다. 

* 미국 동부
* 서유럽
* 미국 중서부
* 미국 중남부
* 동남아시아
* 영국 남부
* 미국 서부 2
* 오스트레일리아 동부
* 미국 동부 2
* 북유럽

## <a name="next-steps"></a>다음 단계

클러스터를 Azure Arc에 연결하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)
