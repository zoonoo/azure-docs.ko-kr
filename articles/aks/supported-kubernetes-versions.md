---
title: Azure Kubernetes Service에서 지원되는 Kubernetes 버전
description: AKS(Azure Kubernetes Service)의 Kubernetes 버전 지원 정책 및 클러스터 수명 주기에 대해 알아봅니다.
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: b7910ee6f58c582b824cec834d92a24c0e184bfb
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205284"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 지원되는 Kubernetes 버전

Kubernetes 커뮤니티에서는 대략 3개월마다 부 버전을 릴리스합니다. 이러한 릴리스에는 새로운 기능과 향상된 기능이 포함됩니다. 패치는 더 자주 릴리스되며(매주 릴리스되기도 함), 부 버전의 중요한 버그 수정용으로만 제공됩니다. 이러한 패치 릴리스에는 Kubernetes 기반 프로덕션 환경에서 실행되는 제품 및 다수의 고객에게 영향을 주는 중요한 버그나 보안 취약성용 수정이 포함됩니다.

AKS를 인증 하 고 새 Kubernetes 버전 릴리스의 안정성에 따라 업스트림 릴리스, 30 일 이내에 해제 하려고 합니다.

## <a name="kubernetes-versions"></a>Kubernetes 버전

Kubernetes는 표준을 [유의 적 버전](https://semver.org/) 버전 관리 체계입니다. 이 Kubernetes의 각 버전에이 번호 매기기 체계 따름을 의미 합니다.

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

버전의 각 숫자는 이전 버전을 사용 하 여 일반적인 호환성을 나타냅니다.

* 주 버전 변경 호환 되지 않는 경우 API가 변경 하거나 이전 버전과 호환성 손상 될 수도 있습니다.
* 부 버전 기능 변경 내용이 다른 부 릴리스는 이전 버전과 호환 되는 사항이 있을 경우 변경 합니다.
* 이전 버전과 호환성 버그를 수정 하는 경우 패치 버전 변경이 됩니다.

예를 들어 프로덕션 클러스터에 있으면 실행 중인 부 버전의 최신 패치 릴리스에서 실행 사용자 하려고 노력 해야 일반적으로 *1.13.6* 하 고 *1.13.7* 사용 가능한 최신 패치를는 사용 가능한 버전을 *1.13* 로 업그레이드 해야 시리즈 *1.13.7* 클러스터 완전히 패치를 적용 하 고 지원 되는 수는 즉시 합니다.

## <a name="kubernetes-version-support-policy"></a>Kubernetes 버전 지원 정책

AKS에서 지원하는 Kubernetes의 4개 부 버전은 다음과 같습니다.

* AKS (N)에서 제공 되는 현재 부 버전
* 이전 부 버전 3개. 지원되는 각 부 버전은 안정적인 패치 2개도 지원합니다.

이 "N-3-" (N (최신 릴리스)-3 (부 버전)) 이라고 합니다.

예를 들어 AKS 소개 *1.13.x* 다음 버전에 대 한 지원이 제공 됩니다는 현재:

새로운 부 버전    |    지원 되는 버전 목록
-----------------    |    ----------------------
1.13.x               |    1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

여기서 "x" 및 ".a" 및 ".b"은 대표 패치 버전입니다.

버전 변경 내용 및 요구 사항에 대 한 통신에 대 한 내용은 아래 "통신"을 참조 하세요.

부 버전이 새로 도입 되었으며, 지원 되는 가장 오래 된 부 버전 및 패치 릴리스 않으며 제거 됩니다. 예를 들어 현재 지원 되는 버전 목록 경우:

<a name="supported-version-list"></a>지원 되는 버전 목록
----------------------
1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b, 1.9.a, 1.9.b

AKS 1.13.x 해제, 즉, 1.9.x 버전 (모든 버전 1.9 버전)를 제거할 및 지원 되지 않음.

> [!NOTE]
> 하세요 note에 고객은 지원 되지 않는 Kubernetes 버전을 실행 하는 경우 이러한 하 라는 메시지가 나타납니다 클러스터에 대 한 지원을 요청 하는 경우 업그레이드 합니다. 지원 되지 않는 Kubernetes 릴리스를 실행 하는 클러스터에서 다루지 않는 합니다 [AKS 정책을 지원](https://docs.microsoft.com/azure/aks/support-policies)합니다.


AKS 위의 부 버전 외에도 두 가지 최신 버전 지원 *패치** 버전의 부 버전을 지정된 합니다. 예를 들어 다음과 같습니다. 지원 되는 다음 버전

<a name="supported-version-list"></a>지원 되는 버전 목록
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

업스트림 Kubernetes 1.12.3 및 1.11.6 출시 및 AKS를 해제 하는 경우 해당 버전을 패치할 가장 오래 된 패치 버전은 사용 되지 않으며 제거 및 지원 되는 버전 목록 됩니다.

<a name="supported-version-list"></a>지원 되는 버전 목록
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

> [!NOTE]
> 고객은 클러스터 생성, CI 또는 다른 자동화 된 작업 특정 패치 버전을 고정 하지 해야 합니다. 

### <a name="communications"></a>통신

* 새로운 **사소한** Kubernetes 버전
  * 새 버전 및 버전을 제거할 수는 모든 사용자에 게 공개적으로 알림이 표시 됩니다.
  * 새 패치 버전이 릴리스되면 가장 오래 된 패치 릴리스는 동시에 제거 됩니다.
  * 고객 **60 일 동안** 지원 되는 부 버전 릴리스로 업그레이드 하려면 공용 알림 날짜에서입니다.
* 새로운 **패치** Kubernetes 버전
  * 최신 패치 릴리스에서를 업그레이드 하 고 해제 하 고 새 패치 버전의 모든 사용자에 게 알림이 표시 됩니다.
  * 명의 **30 일 동안** 최신의 지원 되는 패치 릴리스를로 업그레이드 합니다. 명의 **30 일 동안** 가장 오래 된 제거 되기 전에 지원 되는 패치 릴리스를 업그레이드 합니다.

AKS를 "해제" 일반 공급으로 모든 SLO에서 사용 하도록 설정 정의 / 품질 서비스 측정 및 모든 지역에서 사용할 수 있습니다.

> [!NOTE]
> 버전 릴리스 및 결함, 부 버전은 사용자가 사용 되지 않음/제거 하는 경우 지원 되는 버전으로 업그레이드 하는 60 일 된 Kubernetes의 고객에 게 알림이 표시 됩니다. 패치 릴리스의 경우 고객 지원 되는 버전으로 업그레이드 하려면 30 일 동안 지정 됩니다.

알림은 통해 전송 됩니다.

* [AKS 릴리스 정보](https://aka.ms/aks/releasenotes)
* Azure portal 알림
* [Azure 업데이트 채널][azure-update-channel]

### <a name="policy-exceptions"></a>정책 예외

AKS 권리를 추가 하거나 버그 또는 사전 고 지 없이 보안 문제에 영향을 주는 하나 이상의 중요 한 프로덕션 하도록 식별 된 새/기존 버전을 제거 합니다.

특정 패치 릴리스를 건너뛸 수 있습니다 또는 버그나 보안 문제의 심각도 따라 출시를 가속화 합니다.

### <a name="azure-portal-and-cli-default-versions"></a>Azure portal 및 CLI 기본 버전

포털 또는 Azure CLI를 사용 하 여 AKS 클러스터를 배포할 때 클러스터 N-1 부 버전 및 최신 패치를 항상 설정 됩니다. 예를 들어, AKS에서 지 원하는 경우 *1.13.x*를 *1.12.a* + *1.12.b*하십시오 *1.11.a*  +   *1.11.b*하십시오 *1.10.a* + *1.10b*, 새 클러스터에 대 한 기본 버전은 *1.12.b*합니다.

AKS (예: 1.12.b minor.latestPatch) 고객에 게 제공 알된 안정적 N-1 기본값으로 및 기본적으로 패치 버전입니다.

## <a name="list-currently-supported-versions"></a>현재 지원되는 버전 목록

사용자의 구독과 지역에 현재 제공되는 버전을 알아보려면 [az aks get-versions][az-aks-get-versions] 명령을 사용합니다. 다음 예제에는 *EastUS* 지역에 제공되는 Kubernetes 버전이 나열되어 있습니다.

```azurecli-interactive
az aks get-versions --location eastus --output table
```

출력은 Kubernetes 버전을 보여 주는 다음 예제와 유사 *1.13.5* 를 최신 버전을 사용할 수 있습니다.

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>FAQ

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 업그레이드하면 어떻게 되나요?**

있는 경우는 *n-4* 버전 있습니다 외부 지원 및 업그레이드 하 라는 메시지가 나타납니다. N-3로는 n-4 버전에서에서의 업그레이드에 성공 하면 이제 당사의 지원 정책 내에서 있는 수 있습니다. 예를 들면 다음과 같습니다.

- 지원 되는 AKS 버전이 *1.13.x*를 *1.12.a* + *1.12.b*하십시오 *1.11.c*  +  *1.11 d*, 및 *1.10.e* + *1.10f* 에서 되며 *1.9.g* 또는 *1.9.h*, 외부 지원 됩니다.
- 경우로 업그레이드 *1.9.g* 하거나 *1.9.h* 에 *1.10.e* 또는 *1.10.f* 성공 하면 돌아왔습니다는 당사의 지원 정책 내에서.

*n-4*보다 오래된 버전으로의 업그레이드는 지원되지 않습니다. 이러한 경우 고객은 새 AKS 클러스터를 만들고 워크로드를 다시 배포하는 것이 좋습니다.

**' 개 지원 ' 무슨 의미 인가요**

'Support' 외부 있음을 나타내고 중인 버전이 지원 되는 버전 목록 외부 지원을 요청할 때 지원 되는 버전으로 클러스터를 업그레이드 하 라는 메시지가 나타납니다. 또한 모든 런타임 또는 지원 되는 버전 목록 외부 클러스터에 대 한 다른 보장 AKS는 만들지 않습니다.

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 확장하면 어떻게 되나요?**

AKS에서 지원되지 않는 부 버전에서도 규모 감축이나 확장은 문제 없이 계속 작동합니다.

**고객은 특정 Kubernetes 버전을 계속 사용할 수 있나요?**

예. 그러나 클러스터 AKS에서 지원 되는 버전 중 하나에 없는 경우 클러스터를 벗어난 AKS 지원 정책을 확인. Azure는 클러스터를 자동으로 업그레이드하거나 삭제하지 않습니다.

**에이전트 클러스터가 지원되는 AKS 버전 중 하나가 아닌 경우 마스터는 어떤 버전을 지원하나요?**

마스터는 지원되는 최신 버전으로 자동 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

클러스터를 업그레이드하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
