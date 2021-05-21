---
title: 클러스터 확장 - Azure Arc 지원 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 지원 Kubernetes의 클러스터 확장 기능에 대한 개념적 개요를 제공합니다.
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450879"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes의 클러스터 확장

[Helm 차트](https://helm.sh/)는 가장 복잡한 Kubernetes 애플리케이션을 정의, 설치 및 업그레이드하는 데 필요한 빌딩 블록도 제공하여 Kubernetes 애플리케이션을 관리하는 데 도움이 됩니다. 클러스터 확장 기능은 Helm의 패키징 구성 요소를 기반으로 빌드하려고 합니다. Azure Monitor 및 Azure Defender for Kubernetes와 같은 클러스터 확장의 설치 및 수명 주기 관리를 위한 Azure Resource Manager 기반 환경을 제공하여 수행합니다. 클러스터 확장 기능은 Helm 차트에서 기본적으로 제공되는 이상에서 다음과 같은 추가 이점을 제공합니다.

- 해당 클러스터에 설치된 모든 클러스터 및 확장의 인벤토리를 가져옵니다.
- Azure Policy를 사용하여 클러스터 확장의 대규모 배포를 자동화합니다.
- 모든 확장의 릴리스 트레인을 구독합니다.
- 확장에 대한 자동 업그레이드를 설정합니다.
- 업데이트 및 삭제의 확장 인스턴스 생성 및 수명 주기 관리 이벤트에 대한 지원 가능성

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>아키텍처

[ ![클러스터 확장 아키텍처](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

클러스터 확장 인스턴스는 Azure Resource Manager의 Azure Arc 지원 Kubernetes 리소스(`Microsoft.Kubernetes/connectedClusters`로 표시)를 기반으로 확장 Azure Resource Manager 리소스(`Microsoft.KubernetesConfiguration/extensions`)로 만들어집니다. Azure Resource Manager 표현을 사용하면 특정 클러스터 확장 유무에 관계없이 모든 Azure Arc 지원 Kubernetes 리소스를 확인하는 정책을 작성할 수 있습니다. 원하는 속성 값을 가진 클러스터 확장이 없는 클러스터를 결정한 후에는 Azure Policy를 사용하여 이러한 비규격 리소스를 수정할 수 있습니다.

클러스터에서 실행되는 `config-agent`는 Azure Arc 지원 Kubernetes 리소스에서 새롭거나 업데이트된 확장 리소스를 추적합니다. 클러스터에서 실행되는 `extensions-manager`는 Azure Container Registry 또는 Microsoft Container Registry에서 Helm 차트를 끌어와서 클러스터에 설치합니다. 

클러스터에서 실행되는 `config-agent` 및 `extensions-manager` 구성 요소 모두 버전 업데이트 및 확장 인스턴스 삭제를 처리합니다.

> [!NOTE]
> * `config-agent`는 Arc 지원 Kubernetes 리소스에서 사용할 수 있는 새로운 또는 업데이트된 확장 리소스를 모니터링합니다. 따라서 에이전트는 클러스터로 끌어올 수 있도록 원하는 상태를 연결해야 합니다. 에이전트가 Azure에 연결할 수 없는 경우 원하는 상태를 클러스터로 전파하는 작업이 지연됩니다.
> * 확장에 대한 보호된 구성 설정은 Azure Arc 지원 Kubernetes 서비스에서 최대 48시간 동안 저장됩니다. 결과적으로 Azure에서 확장 리소스를 만든 후 48시간 동안 클러스터가 분리된 상태를 유지하는 경우 확장은 `Pending` 상태에서 `Failed` 상태로 전환됩니다. 클러스터를 최대한 정기적으로 온라인 상태로 전환하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 사용하여 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* Azure Arc 사용 Kubernetes 클러스터에 [클러스터 확장을 배포](./extensions.md)합니다.