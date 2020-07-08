---
title: Azure Kubernetes Service에서 지원되는 Kubernetes 버전
description: AKS(Azure Kubernetes Service)의 Kubernetes 버전 지원 정책 및 클러스터 수명 주기에 대해 알아봅니다.
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 5f396ba6ec1ecc6bd111e048ce34e3546c7364dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84345056"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 지원되는 Kubernetes 버전

Kubernetes 커뮤니티에서는 대략 3개월마다 부 버전을 릴리스합니다. 이러한 릴리스에는 새로운 기능과 향상된 기능이 포함됩니다. 패치는 더 자주 릴리스되며(매주 릴리스되기도 함), 부 버전의 중요한 버그 수정용으로만 제공됩니다. 이러한 패치 릴리스에는 Kubernetes 기반 프로덕션 환경에서 실행되는 제품 및 다수의 고객에게 영향을 주는 중요한 버그나 보안 취약성용 수정이 포함됩니다.

AKS는 버전의 안정성에 따라 업스트림 릴리스의 30 일 이내에 새로운 Kubernetes 버전을 인증 하 고 릴리스 하는 것을 목표로 합니다.

## <a name="kubernetes-versions"></a>Kubernetes 버전

Kubernetes는 표준 [의미 체계 버전](https://semver.org/) 관리 체계를 사용 합니다. 즉, 각 버전의 Kubernetes는 다음 번호 매기기 체계를 따릅니다.

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

버전의 각 번호는 이전 버전과의 호환성을 나타냅니다.

* 호환 되지 않는 API 변경 또는 이전 버전과의 호환성이 손상 될 때 주 버전은 변경 됩니다.
* 사소한 버전은 다른 부 릴리스와 이전 버전과 호환 되는 기능이 변경 될 때 변경 됩니다.
* 이전 버전과 호환 되는 버그 수정이 수행 될 때 패치 버전이 변경 됩니다.

사용자는 실행 중인 부 버전의 최신 패치 릴리스를 실행 해야 합니다. 예를 들어 프로덕션 클러스터가 *1.12.14* 에 있고 *1.12.15* 가 *1.12* 시리즈에 사용할 수 있는 최신 패치 버전 인 경우 클러스터가 완전히 패치 되 고 지원 되는지 확인 하는 즉시 *1.12.15* 로 업그레이드 해야 합니다.

## <a name="kubernetes-version-support-policy"></a>Kubernetes 버전 지원 정책

AKS는 Kubernetes의 세 가지 부 버전을 지원 합니다.

* AKS (N)에서 릴리스된 현재 부 버전
* 이전 두 부 버전 지원되는 각 부 버전은 안정적인 패치 2개도 지원합니다.

이를 "N-2" 라고 합니다. (N (최신 릴리스)-2 (부 버전)).

예를 들어 AKS에서 *1.15* 를 도입 하는 경우 다음 버전에 대 한 지원이 제공 됩니다.

새 부 버전    |    지원 되는 버전 목록
-----------------    |    ----------------------
1.15. a               |    1.15. a, 1.15. b, 1.14, 1.14, 1.13. e, 1.13. f

여기서 ". letter"는 패치 버전을 나타냅니다.

버전 변경 및 기대치와 관련 된 통신에 대 한 자세한 내용은 아래의 "통신"을 참조 하십시오.

새 부 버전이 도입 되 면 지원 되는 가장 오래 된 부 버전 및 패치 릴리스가 더 이상 사용 되지 않고 제거 됩니다. 예를 들어 현재 지원 되는 버전 목록은 다음과 같습니다.

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

및 AKS 릴리스 1.16. 즉 *, 1.13입니다.* 버전 (모든 1.13 버전)은 제거 되며 지원 되지 않습니다.

> [!NOTE]
> 고객이 지원 되지 않는 Kubernetes 버전을 실행 하는 경우 클러스터에 대 한 지원을 요청할 때 업그레이드 하 라는 메시지가 표시 됩니다. 지원 되지 않는 Kubernetes 릴리스를 실행 하는 클러스터는 [AKS 지원 정책](https://docs.microsoft.com/azure/aks/support-policies)에 포함 되지 않습니다.

위의 사소한 버전에서 AKS는 지정 된 부 버전의 두 가지 최신 **패치** 릴리스를 지원 합니다. 예를 들어 다음과 같은 지원 되는 버전이 있다고 가정 합니다.

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

업스트림 Kubernetes 릴리스된 1.15.3 및 1.14.6 및 AKS가 해당 패치 버전을 해제 하면 가장 오래 된 패치 버전이 더 이상 사용 되지 않고 제거 되며 지원 되는 버전 목록은 다음과 같이 됩니다.

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>통신

* Kubernetes의 새 **부** 버전
  * 모든 사용자에 게는 새 버전을 공개적으로 알리고 제거 되는 버전이 표시 됩니다.
  * 새 패치 버전이 릴리스되면 가장 오래 된 패치 릴리스가 동시에 제거 됩니다.
  * Azure 지원은 공개 알림 날짜 로부터 **30 일 이내** 에 지원 되는 부 버전 릴리스로 업그레이드 하는 고객을 제공 합니다. 30 일이 지나면 지원을 계속 받으려면 부 버전을 업데이트 해야 합니다.
* Kubernetes의 새로운 **패치** 버전
  * 모든 사용자에 게 릴리스되는 새 패치 버전에 대 한 알림이 표시 되 고 최신 패치 릴리스로 업그레이드 됩니다.
  * 이전 패치 버전을 제거한 후에는 Azure 지원에서 **30 일 동안** 지원 되는 패치 릴리스로 업그레이드할 수 있습니다. 30 일이 지나면 지원 받기를 계속 하려면 patch 버전을 업데이트 해야 합니다.

AKS는 일반적으로 사용 가능한 버전으로 "릴리스 버전"을 정의 하 고 모든 SLO/서비스 측정 품질에서 사용 하도록 설정 하 고 모든 지역에서 사용할 수 있습니다. AKS는 명시적으로 레이블이 지정 되 고 미리 보기 약관에 따라 미리 보기 버전을 지원할 수도 있습니다.

#### <a name="notification-channels-for-aks-changes"></a>AKS 변경에 대 한 알림 채널

AKS는 [GitHub](https://github.com/Azure/AKS/releases)에서 서비스에 릴리스된 새 Kubernetes 버전, 서비스 변경 및 구성 요소 업데이트를 요약 하는 일반 서비스 업데이트를 게시 합니다.

이러한 변경 내용은 관리 서비스의 일부로 제공 되는 정기 유지 관리의 일환으로 모든 고객에 게 제공 되며, 일부는 명시적 업그레이드가 필요 하 고 다른 작업은 필요 하지 않습니다.

알림은 다음을 통해 전송 됩니다.

* [AKS 릴리스 정보](https://aka.ms/aks/releasenotes)
* Azure Portal 알림
* [Azure 업데이트 채널][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>지원 되는 버전 정책 예외

AKS는 하나 이상의 중요 한 프로덕션에 영향을 주는 버그 또는 보안 문제를 사전에 확인 하지 않고 식별 된 새/기존 버전을 추가 하거나 제거할 권리를 보유 합니다.

특정 패치 릴리스를 건너뛰거나 버그 또는 보안 문제의 심각도에 따라 롤아웃 가속화를 사용할 수 있습니다.

### <a name="azure-portal-and-cli-default-versions"></a>Azure Portal 및 CLI 기본 버전

포털 또는 Azure CLI를 사용 하 여 AKS 클러스터를 배포 하는 경우 클러스터는 기본적으로 N-1 부 버전 및 최신 패치로 설정 됩니다. 예를 들어 AKS가 *1.15. a*, *1.15. b*, *1.14*, *1.14*, *1.13.* *f*를 지 원하는 경우 선택 된 기본 버전은 *1.14. c*입니다.

AKS는 기본적으로 N-1의 기본값을 선택 하 여 고객에 게 알려지고 안정적 이며 패치가 적용 된 버전을 제공 합니다.

## <a name="list-currently-supported-versions"></a>현재 지원되는 버전 목록

사용자의 구독과 지역에 현재 제공되는 버전을 알아보려면 [az aks get-versions][az-aks-get-versions] 명령을 사용합니다. 다음 예제에는 *EastUS* 지역에 제공되는 Kubernetes 버전이 나열되어 있습니다.

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>FAQ

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 업그레이드하면 어떻게 되나요?**

*N-3* 버전을 사용할 경우 지원 센터에 있지 않으며 업그레이드 하 라는 메시지가 표시 됩니다. 버전 n-3에서 n-2로 업그레이드 하는 경우 이제 지원 정책에 포함 됩니다. 예를 들어:

- 가장 오래 된 지원 되는 AKS 버전은 *1.13입니다. a* 와 사용자는 *1.12. b* 이상에서는 지원 되지 않습니다.
- *1.12. b* 에서 *1.13.* 이상으로 업그레이드 하는 경우 지원 정책 내에서 다시 시작 됩니다.

*N-2* 의 지원 되는 기간 보다 오래 된 버전으로 업그레이드 하는 것은 지원 되지 않습니다. 이러한 경우 고객이 새 AKS 클러스터를 만들고 지원 되는 창에서 해당 워크 로드를 버전으로 다시 배포 하는 것이 좋습니다.

**' 지원 외부 '의 의미**

' 지원 외부 '는 실행 중인 버전이 지원 되는 버전 목록 외부에 있음을 의미 하며 지원을 요청할 때 클러스터를 지원 되는 버전으로 업그레이드 하 라는 메시지가 표시 됩니다. 또한 AKS은 지원 되는 버전 목록 외부의 클러스터에 대 한 런타임 또는 다른 보증을 수행 하지 않습니다.

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 확장하면 어떻게 되나요?**

AKS에서 지원 하지 않는 부 버전의 경우 확장 또는 축소는 계속 작동 하지만 클러스터를 다시 지원 하도록 업그레이드 하는 것이 좋습니다.

**고객은 특정 Kubernetes 버전을 계속 사용할 수 있나요?**

클러스터가 3 개 이상의 부 버전에 대 한 지원을 제공 하지 않고 보안 위험을 발생 시킬 수 있는 경우 Azure는 클러스터를 사전에 업그레이드 하도록 사용자에 게 연락 합니다. 추가 작업을 수행 하지 않는 경우 Azure는 사용자를 대신 하 여 클러스터를 강제 업그레이드할 권리를 보유 합니다.

**노드 풀이 지원 되는 AKS 버전 중 하나가 아닌 경우 제어 평면에서 지 원하는 버전은 무엇 인가요?**

컨트롤 평면은 모든 노드 풀의 버전 창 내에 있어야 합니다. 제어 평면 또는 노드 풀을 업그레이드 하는 방법에 대 한 자세한 내용은 [노드 풀 업그레이드](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)에 대 한 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

클러스터를 업그레이드하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
