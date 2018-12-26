---
title: Azure Kubernetes Service에서 지원되는 Kubernetes 버전
description: AKS(Azure Kubernetes Service)의 Kubernetes 버전 지원 정책 및 클러스터 수명 주기에 대해 알아봅니다.
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: a17e2fa4bef6890d59f7e66c6ede349f8dee3b8a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51280456"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 지원되는 Kubernetes 버전

Kubernetes 커뮤니티에서는 대략 3개월마다 부 버전을 릴리스합니다. 이러한 릴리스에는 새로운 기능과 향상된 기능이 포함됩니다. 패치는 더 자주 릴리스되며(매주 릴리스되기도 함), 부 버전의 중요한 버그 수정용으로만 제공됩니다. 이러한 패치 릴리스에는 Kubernetes 기반 프로덕션 환경에서 실행되는 제품 및 다수의 고객에게 영향을 주는 중요한 버그나 보안 취약성용 수정이 포함됩니다.

새 Kubernetes 부 버전은 릴리스 당일에 [acs-engine][acs-engine]에서 제공됩니다. AKS SLO(서비스 수준 목표)에 따른 AKS 클러스터 부 버전 릴리스 목표 기간은 30일 이내입니다(릴리스 안정성에 따라 달라질 수 있음).

## <a name="kubernetes-version-support-policy"></a>Kubernetes 버전 지원 정책

AKS에서 지원하는 Kubernetes의 4개 부 버전은 다음과 같습니다.

- 업스트림 릴리스된 현재 부 버전(n)
- 이전 부 버전 3개. 지원되는 각 부 버전은 안정적인 패치 2개도 지원합니다.

예를 들어 AKS *1.11.x* 버전이 오늘 릴리스되는 경우 *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8f*도 지원됩니다. 여기서 문자가 붙은 패치 릴리스가 안정적인 최신 빌드 2개입니다.

새로운 부 버전이 릴리스되면 지원되는 가장 오래된 부 버전 및 패치 릴리스는 사용 중지됩니다. 새로운 부 버전이 릴리스되어 이전 버전이 사용 중지되기 15일 전에 [Azure 업데이트 채널][azure-update-channel]을 통해 공지가 제공됩니다. 위의 예제에서는 *1.11.x*가 릴리스되면 *1.7.g* + *1.7.h* 버전이 사용 중지됩니다.

포털에서 또는 Azure CLI를 사용하여 배포하는 AKS 클러스터는 항상 n-1 부 버전 및 최신 패치로 설정됩니다. 예를 들어 AKS에서 *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8f*를 지원하는 경우 새 클러스터의 기본 버전은 *1.10.b*가 됩니다.

## <a name="list-currently-supported-versions"></a>현재 지원되는 버전 목록

사용자의 구독과 지역에 현재 제공되는 버전을 알아보려면 [az aks get-versions][az-aks-get-versions] 명령을 사용합니다. 다음 예제에는 *EastUS* 지역에 제공되는 Kubernetes 버전이 나열되어 있습니다.

```azurecli-interactive
az aks get-versions --location eastus --output table
```

출력은 다음 예제와 비슷합니다. 예제의 경우 Kubernetes 버전 *1.11.3*이 사용할 수 있는 최신 버전입니다.

```
KubernetesVersion    Upgrades
-------------------  ----------------------
1.11.3               None available
1.11.2               1.11.3
1.10.8               1.11.2, 1.11.3
1.10.7               1.10.8, 1.11.2, 1.11.3
1.9.10               1.10.7, 1.10.8
1.9.9                1.9.10, 1.10.7, 1.10.8
1.8.15               1.9.9, 1.9.10
1.8.14               1.8.15, 1.9.9, 1.9.10
```

## <a name="faq"></a>FAQ

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 업그레이드하면 어떻게 되나요?**

해당 버전이 *n-4* 버전이면 SLO 범위를 벗어나게 됩니다. n-4에서 n-3 버전으로의 업그레이드가 정상적으로 완료되면 다시 SLO 범위에 포함됩니다. 예: 

- 지원되는 AKS 버전이 *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8f*인데 현재 사용 중인 버전이 *1.7.g* 또는 *1.7.h*이면 SLO 범위를 벗어나게 됩니다.
- *1.7.g* 또는 *1.7.h*에서 *1.8.e* 또는 *1.8.f*로의 업그레이드가 정상적으로 완료되면 다시 SLO 범위에 포함됩니다.

*n-4*보다 오래된 버전으로의 업그레이드는 지원되지 않습니다. 이러한 경우 고객은 새 AKS 클러스터를 만들고 워크로드를 다시 배포하는 것이 좋습니다.

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 확장하면 어떻게 되나요?**

AKS에서 지원되지 않는 부 버전에서도 규모 감축이나 확장은 문제 없이 계속 작동합니다.

**고객은 특정 Kubernetes 버전을 계속 사용할 수 있나요?**

예. 하지만 클러스터가 AKS에서 지원하는 버전 중 하나가 아니면 AKS SLO의 범위를 벗어나게 됩니다. Azure는 클러스터를 자동으로 업그레이드하거나 삭제하지 않습니다.

**에이전트 클러스터가 지원되는 AKS 버전 중 하나가 아닌 경우 마스터는 어떤 버전을 지원하나요?**

마스터는 지원되는 최신 버전으로 자동 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

클러스터를 업그레이드하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
