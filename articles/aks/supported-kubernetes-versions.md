---
title: Azure Kubernetes Service에서 지원되는 Kubernetes 버전
description: AKS(Azure Kubernetes Service)의 Kubernetes 버전 지원 정책 및 클러스터 수명 주기에 대해 알아봅니다.
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593447"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 지원되는 Kubernetes 버전

Kubernetes 커뮤니티에서는 대략 3개월마다 부 버전을 릴리스합니다. 이러한 릴리스에는 새로운 기능과 향상된 기능이 포함됩니다. 패치는 더 자주 릴리스되며(매주 릴리스되기도 함), 부 버전의 중요한 버그 수정용으로만 제공됩니다. 이러한 패치 릴리스에는 Kubernetes 기반 프로덕션 환경에서 실행되는 제품 및 다수의 고객에게 영향을 주는 중요한 버그나 보안 취약성용 수정이 포함됩니다.

AKS는 출시의 안정성에 따라 업스트림 릴리스 후 30일 이내에 새로운 Kubernetes 버전을 인증하고 출시하는 것을 목표로 합니다.

## <a name="kubernetes-versions"></a>쿠베네츠 버전

Kubernetes는 표준 [시맨틱 버전 버전 전환](https://semver.org/) 체계를 사용합니다. 즉, 각 Kubernetes 버전은 이 번호 매기기 체계를 따릅니다.

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

버전의 각 번호는 이전 버전과의 일반적인 호환성을 나타냅니다.

* 호환되지 않는 API가 변경되거나 이전 버전과의 호환성이 손상될 때 주 버전이 변경됩니다.
* 다른 부 릴리스와 호환되는 기능이 변경되면 부 버전이 변경됩니다.
* 이전 버전과 호환되는 버그 수정이 이루어지면 패치 버전이 변경됩니다.

사용자는 실행 중인 마이너 버전의 최신 패치 릴리스를 실행하는 것을 목표로 해야 합니다(예: 프로덕션 클러스터가 *1.12.14* 및 *1.12.15에* 있는 경우 *1.12* 시리즈에 사용할 수 있는 최신 패치 버전인 경우 클러스터가 완전히 패치되고 지원되도록 하는 즉시 *1.12.15로* 업그레이드해야 합니다.)

## <a name="kubernetes-version-support-policy"></a>Kubernetes 버전 지원 정책

AKS는 Kubernetes의 세 가지 마이너 버전을 지원합니다:

* AKS (N)에서 릴리스되는 현재 의 부 버전
* 두 개의 이전 마이너 버전. 지원되는 각 부 버전은 안정적인 패치 2개도 지원합니다.

이를 "N-2"로 알려져 있습니다: (N(최신 릴리스) - 2(부버전))).

예를 들어 AKS가 오늘 *1.15.a를* 도입하는 경우 다음 버전에 대한 지원이 제공됩니다.

새 마이너 버전    |    지원되는 버전 목록
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

여기서 ".letter"는 패치 버전을 대표합니다.

버전 변경 및 기대에 대한 통신에 대한 자세한 내용은 아래의 "통신"을 참조하십시오.

새 부 버전이 도입되면 지원되는 가장 오래된 부 버전 및 패치 릴리스가 더 이상 사용되지 않고 제거됩니다. 예를 들어 현재 지원되는 버전 목록이 다음과 같은 경우:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

그리고 AKS는 1.16을 출시합니다. *즉, 1.13입니다.* 버전(모든 1.13 버전)이 제거되고 지원이 부족합니다.

> [!NOTE]
> 고객이 지원되지 않는 Kubernetes 버전을 실행하는 경우 클러스터에 대한 지원을 요청할 때 업그레이드하라는 메시지가 표시됩니다. 지원되지 않는 Kubernetes 릴리스를 실행하는 클러스터는 [AKS 지원 정책의](https://docs.microsoft.com/azure/aks/support-policies)적용을 받지 않습니다.

AKS는 부 버전에서 위의 것 외에도 주어진 마이너 버전의 두 가지 최신 **패치** 릴리스를 지원합니다. 예를 들어 다음과 같은 지원되는 버전이 제공됩니다.

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

업스트림 Kubernetes가 1.15.3 및 1.14.6을 릴리스하고 AKS가 해당 패치 버전을 릴리스하면 가장 오래된 패치 버전이 더 이상 사용되지 않고 제거되고 지원되는 버전 목록이 됩니다.

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>통신

* Kubernetes의 새로운 **마이너** 버전용
  * 모든 사용자에게 새 버전과 제거될 버전에 대한 알림을 공개적으로 알 수 있습니다.
  * 새 패치 버전이 릴리스되면 가장 오래된 패치 릴리스가 동시에 제거됩니다.
  * 고객은 공개 알림 날짜로부터 **30일 이내에** 지원되는 부 버전 릴리스로 업그레이드할 수 있습니다.
* Kubernetes의 새로운 **패치** 버전용
  * 모든 사용자에게 새 패치 버전이 릴리스되고 최신 패치 릴리스로 업그레이드하라는 알림을 받습니다.
  * 사용자는 **30일 이내에** 지원되는 최신 패치 릴리스로 업그레이드한 후 가장 오래된 패치를 제거해야 합니다.

AKS는 "릴리스된 버전"을 모든 SLO/서비스 품질 측정에서 활성화되고 모든 지역에서 사용할 수 있는 일반적으로 사용 가능한 버전으로 정의합니다. AKS는 명시적으로 레이블이 지정되고 미리 보기 이용 약관이 적용되는 미리 보기 버전을 지원할 수도 있습니다.

#### <a name="notification-channels-for-aks-changes"></a>AKS 변경에 대한 알림 채널

AKS는 [GitHub에서](https://github.com/Azure/AKS/releases)서비스에 릴리스된 새 Kubernetes 버전, 서비스 변경 및 구성 요소 업데이트를 요약하는 정기적인 서비스 업데이트를 게시합니다.

이러한 변경 내용은 관리되는 서비스의 일부로 제공되는 정기적인 유지 관리의 일부로 모든 고객에게 롤백되며 일부는 명시적 업그레이드가 필요하지만 다른 변경 사항은 아무작업도 필요하지 않습니다.

알림은 다음을 통해 전송됩니다.

* [AKS 릴리스 노트](https://aka.ms/aks/releasenotes)
* Azure Portal 알림
* [Azure 업데이트 채널][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>지원되는 버전 정책 예외

AKS는 사전 통보 없이 버그 또는 보안 문제에 영향을 미치는 하나 이상의 중요한 프로덕션이 있는 것으로 확인된 새/기존 버전을 추가하거나 제거할 수 있는 권리를 보유합니다.

버그 또는 보안 문제의 심각도에 따라 특정 패치 릴리스를 건너뛰거나 롤아웃이 가속화될 수 있습니다.

### <a name="azure-portal-and-cli-default-versions"></a>Azure 포털 및 CLI 기본 버전

포털 또는 Azure CLI를 사용하여 AKS 클러스터를 배포하면 클러스터는 기본적으로 N-1 부 버전 및 최신 패치로 설정됩니다. 예를 들어 AKS가 *1.15.a,* *1.15.b,* *1.14.c,* *1.14.d,* *1.13.e*및 *1.13.f를*지원하는 경우 선택한 기본 버전은 *1.14.c입니다.*

AKS는 기본적으로 고객에게 알려진, 안정및 패치 버전을 제공하기 위해 N-1의 기본값을 선택합니다.

## <a name="list-currently-supported-versions"></a>현재 지원되는 버전 목록

사용자의 구독과 지역에 현재 제공되는 버전을 알아보려면 [az aks get-versions][az-aks-get-versions] 명령을 사용합니다. 다음 예제에는 *EastUS* 지역에 제공되는 Kubernetes 버전이 나열되어 있습니다.

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>FAQ

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 업그레이드하면 어떻게 되나요?**

*n-3* 버전에 있는 경우 지원이 없으며 업그레이드하라는 메시지가 표시됩니다. 버전 n-3에서 n-2로 업그레이드가 성공하면 이제 지원 정책에 따라 다수 있습니다. 예를 들어:

- 가장 오래 지원되는 AKS 버전이 *1.13.a이고* *1.12.b* 이상이면 지원이 되지 않는 것입니다.
- *1.12.b에서* *1.13.a* 이상으로 업그레이드가 성공하면 지원 정책으로 돌아갑니다.

*N-2의* 지원되는 창보다 오래된 버전으로의 업그레이드는 지원되지 않습니다. 이러한 경우 고객은 새 AKS 클러스터를 만들고 지원되는 창에 버전으로 워크로드를 다시 배포하는 것이 좋습니다.

**'지원 외'의 의미**

'지원 외'는 실행 중인 버전이 지원되는 버전 목록 외부에 있음을 의미하며 지원을 요청할 때 클러스터를 지원되는 버전으로 업그레이드하라는 메시지가 표시됩니다. 또한 AKS는 지원되는 버전 목록 외부의 클러스터에 대해 런타임이나 기타 보증을 하지 않습니다.

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 확장하면 어떻게 되나요?**

AKS에서 지원되지 않는 부 버전의 경우 확장/축소가 계속 작동해야 하지만 클러스터를 다시 지원하도록 업그레이드하는 것이 좋습니다.

**고객은 특정 Kubernetes 버전을 계속 사용할 수 있나요?**

예. 그러나 클러스터가 AKS에서 지원하는 버전 중 하나에 없는 경우 클러스터는 AKS 지원 정책에서 제외됩니다. Azure는 클러스터를 자동으로 업그레이드하거나 삭제하지 않습니다.

**노드 풀이 지원되는 AKS 버전 중 하나에 없는 경우 제어 평면은 어떤 버전을 지원합니까?**

제어 평면은 모든 노드 풀의 버전 창 내에 있어야 합니다. 제어 평면 또는 노드 풀 업그레이드에 대한 자세한 내용은 [노드 풀 업그레이드에](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)대한 설명서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

클러스터를 업그레이드하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
