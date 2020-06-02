---
title: Azure Arc가 지원되는 Kubernetes 개요
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665278"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Azure Arc가 지원되는 Kubernetes란 무엇인가요?(미리 보기)

Azure Arc가 지원되는 Kubernetes를 사용하여 Azure 내부 또는 외부에서 Kubernetes 클러스터를 연결하고 구성할 수 있습니다(미리 보기). Kubernetes 클러스터가 Azure Arc에 연결되면 Azure Portal에 표시되고 Azure Resource Manager ID와 관리 ID를 갖습니다. 클러스터는 표준 Azure 구독에 연결되고, 리소스 그룹에서 라이브되며, 다른 Azure 리소스와 마찬가지로 태그를 받을 수 있습니다. 


Kubernetes 클러스터를 Azure에 연결하려면 클러스터 관리자가 에이전트를 배포해야 합니다. 이러한 에이전트는 `azure-arc`라는 Kubernetes 네임스페이스에서 실행되며 표준 Kubernetes 배포입니다. 에이전트는 Azure에 대한 연결을 담당하고, Azure Arc 로그 및 메트릭을 수집하고, 구성 요청을 감시합니다.  
 
 > [!NOTE]
> Azure Arc가 지원되는 Kubernetes는 미리 보기 상태이며 프로덕션 워크로드에는 권장되지 않습니다. 


## <a name="supported-scenarios"></a>지원되는 시나리오 

Azure Arc가 지원되는 Kubernetes는 다음과 같은 시나리오를 지원합니다. 

* 인벤토리, 그룹화 및 태그 지정을 위해 Azure 외부에서 실행되는 Kubernetes 연결 

* GitOps 기반 구성 관리를 사용하여 애플리케이션 배포 및 구성 적용 

* 컨테이너에 대한 Azure Monitor를 사용하여 클러스터를 확인하고 모니터링합니다. 

* Kubernetes에 대한 Azure Policy를 사용하여 정책 적용 

 
## <a name="supported-regions"></a>지원되는 지역 

현재 Azure Arc가 지원되는 Kubernetes는 다음 지역에서 지원됩니다. 

* 미국 동부 
* 서유럽 


## <a name="next-steps"></a>다음 단계

* [클러스터 연결](./connect-cluster.md)
