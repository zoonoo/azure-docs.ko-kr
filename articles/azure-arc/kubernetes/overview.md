---
title: Azure Arc 지원 Kubernetes 개요
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: 이 문서에서는 Azure Arc 지원 Kubernetes 개요를 제공합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.custom: references_regions
ms.openlocfilehash: 7e48ebf98f12e79cb154fb50d8e6dbdfaea1cd95
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371310"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Azure Arc 지원 Kubernetes 미리 보기란?

Azure Arc 지원 Kubernetes 미리 보기를 사용하여 Azure 내부 또는 외부에서 Kubernetes 클러스터를 연결하고 구성할 수 있습니다. Kubernetes 클러스터가 Azure Arc에 연결되면 Azure Portal에 표시됩니다. Azure Resource Manager ID와 관리 ID가 있습니다. 클러스터는 표준 Azure 구독에 연결되고, 리소스 그룹에 위치하며, 다른 Azure 리소스와 마찬가지로 태그를 받을 수 있습니다. 

Kubernetes 클러스터를 Azure에 연결하려면 클러스터 관리자가 에이전트를 배포해야 합니다. 이러한 에이전트는 `azure-arc`라는 Kubernetes 네임스페이스에서 실행되며 표준 Kubernetes 배포입니다. 에이전트는 Azure에 대한 연결을 담당하고, Azure Arc 로그 및 메트릭을 수집하고, 구성 요청을 감시합니다. 

Azure Arc 지원 Kubernetes는 전송 중인 데이터를 보호하기 위해 업계 표준 SSL을 지원합니다. 또한, 데이터는 기밀 유지를 위해 Azure Cosmos DB 데이터베이스에 암호화된 상태로 유지됩니다.
 
> [!NOTE]
> Azure Arc 지원 Kubernetes는 미리 보기로 제공됩니다. 프로덕션 워크로드에는 권장하지 않습니다.

## <a name="supported-kubernetes-distributions"></a>지원되는 Kubernetes 배포

Azure Arc 지원 Kubernetes는 Azure의 AKS 엔진, Azure Stack Hub의 AKS 엔진, GKE, EKS 및 VMware vSphere 클러스터와 같은 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터와 함께 작동합니다.

Azure Arc 지원 Kubernetes 기능은 다음 배포에서 Arc 팀에 의해 테스트되었습니다.
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS 엔진
* Azure Stack Hub의 AKS 엔진
* Azure Stack HCI의 AKS
* 클러스터 API 공급자 Azure

## <a name="supported-scenarios"></a>지원되는 시나리오 

Azure Arc 지원 Kubernetes는 다음 시나리오를 지원합니다. 

* 인벤토리, 그룹화 및 태그 지정을 위해 Azure 외부에서 실행되는 Kubernetes 연결.

* GitOps 기반 구성 관리를 사용하여 애플리케이션 배포 및 구성 적용. 

* 컨테이너에 대한 Azure Monitor를 사용하여 클러스터를 확인 및 모니터링. 

* Kubernetes에 대한 Azure Policy를 사용하여 정책 적용. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>지원되는 지역 

현재 Azure Arc 지원 Kubernetes는 다음 지역에서 지원됩니다. 

* 미국 동부 
* 서유럽

## <a name="frequently-asked-questions"></a>질문과 대답

* Azure Arc를 지원하는 Kubernetes 및 AKS(Azure Kubernetes Service)의 차이점은 무엇인가요?

    AKS(Azure Kubernetes Service)는 Azure에서 제공하는 관리되는 Kubernetes 제품입니다. AKS를 사용하면 Azure에서 관리되는 Kubernetes 클러스터를 간단하게 배포할 수 있습니다. AKS는 대부분의 부담을 Azure에 오프로딩하여 Kubernetes를 관리하는 복잡성 및 운영 과부하를 감소시킵니다. Kubernetes 마스터는 Azure에서 관리됩니다. 에이전트 노드만 관리하고 유지하면 됩니다.

    Azure Arc를 지원하는 Kubernetes를 사용하면 Kubernetes 클러스터를 Azure에 연결하여 Azure Monitor 및 Azure Policy와 같은 Azure의 관리 기능을 확장할 수 있습니다. 기본 Kubernetes 클러스터 자체의 유지 관리는 사용자에 의해 수행됩니다.

* Azure에서 실행되는 Azure Kubernetes Service 클러스터를 Azure Arc에 연결해야 하나요?

    아니요. Azure Monitor, Azure Policy(Gatekeeper)와 같은 Azure Arc를 지원하는 Kubernetes의 모든 기능은 Azure에서 이미 리소스를 표시하는 AKS에서 기본적으로 사용할 수 있습니다.
    
* Azure Stack HCI의 AKS 클러스터를 Azure Arc에 연결해야 하나요? Azure Stack Hub 또는 Azure Stack Edge에서 실행되는 Kubernetes 클러스터는 어떻게 되나요?

    예, 이러한 클러스터를 Azure Arc에 연결하면 이점이 있습니다. Azure Resource Manager에서 이러한 Kubernetes 클러스터에 대한 리소스 표현을 제공합니다. 이 리소스 표현을 사용하여 클러스터 구성, Azure Monitor, Azure Policy(Gatekeeper) 등의 기능을 이러한 Kubernetes 클러스터로 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [클러스터 연결](./connect-cluster.md)
