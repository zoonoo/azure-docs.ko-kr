---
title: Azure Arc 지원 Kubernetes 에이전트 업그레이드
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 지원 Kubernetes의 에이전트 업그레이드 제어
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너, 에이전트, 업그레이드
ms.openlocfilehash: 29df9973ead167e7774924cc3a0c685606092d28
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442527"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Azure Arc 지원 Kubernetes 에이전트 업그레이드

Azure Arc 지원 Kubernetes는 해당 에이전트의 자동 업그레이드 및 수동 업그레이드 기능을 제공합니다. 자동 업그레이드 사용 안 함을 사용하고 대신 수동 업그레이드를 사용하는 경우 Arc 에이전트 및 기본 Kubernetes 클러스터에 대한 버전 지원 정책이 적용됩니다.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>클러스터를 Azure Arc에 연결할 때 자동 업그레이드 켜기 또는 끄기

Azure Arc 지원 Kubernetes는 기본 제공 자동 업그레이드 기능을 에이전트에 제공합니다.

다음 명령은 자동 업그레이드를 **사용하도록 설정** 하여 Azure Arc에 클러스터를 연결합니다.

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

자동 업그레이드를 사용하도록 설정하면 에이전트는 최신 버전의 에이전트를 사용할 수 있도록 Azure를 매시간 폴링합니다. 에이전트는 사용 가능한 최신 버전을 찾은 경우 Azure Arc 에이전트에 대한 Helm 차트 업그레이드를 트리거합니다.

자동 업그레이드를 옵트아웃하려면 클러스터를 Azure Arc에 연결하는 동안 `--disable-auto-upgrade` 매개 변수를 지정합니다. 다음 명령은 자동 업그레이드를 **사용하지 않도록 설정** 하여 Azure Arc에 클러스터를 연결합니다.

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> 자동 업그레이드를 사용하지 않도록 설정하려면 Azure Arc 지원 Kubernetes에 대한 [버전 지원 정책](#version-support-policy)을 참조하세요.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>클러스터를 Azure Arc에 연결한 후 자동 업그레이드 켜기/끄기

클러스터를 Azure Arc 연결한 후 아래와 같이 `az connectedk8s update` 명령을 사용하여 자동 업그레이드 기능을 토글할 수 있습니다.

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>수동으로 에이전트 업그레이드

에이전트에 대해 자동 업그레이드를 사용하지 않는 경우 아래와 같이 `az connectedk8s upgrade` 명령을 사용하여 해당 에이전트의 업그레이드를 수동으로 시작할 수 있습니다.

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.1.0
```

Azure Arc 지원 Kubernetes는 에이전트 버전 관리를 위해 `MAJOR.MINOR.PATCH`의 표준 [유의적 버전 체계](https://semver.org/)를 따릅니다. 

버전의 각 번호는 이전 버전과의 일반적인 호환성을 나타냅니다.

* **주 버전** 은 호환되지 않는 API 업데이트가 있거나 이전 버전과의 호환성이 손상될 수 있을 때 변경됩니다.
* **부 버전** 은 기능 변경 내용이 이전 버전의 다른 부 릴리스와 호환될 때 변경됩니다.
* **패치 버전** 은 이전 버전과 호환되는 버그 수정이 수행될 때 변경됩니다.

## <a name="version-support-policy"></a>버전 지원 정책

지원 문제를 만들 때 Azure Arc 지원 Kubernetes는 다음 버전 지원 정책을 적용합니다.

* Azure Arc 지원 Kubernetes 에이전트에는 “N-2”의 지원 기간이 있습니다. 여기서 ‘N’은 에이전트의 최신 부 릴리스입니다. 
  * 예를 들어, Azure Arc 지원 Kubernetes가 오늘 0.28.a를 도입하면 버전 0.28.a, 0.28.b, 0.27.c, 0.27.d, 0.26.e, 0.26.f가 Azure Arc에서 지원됩니다.

* Azure Arc에 연결된 Kubernetes 클러스터에는 “N-2”의 지원 기간이 있습니다. 여기서 ‘N’은 [업스트림 Kubernetes](https://github.com/kubernetes/kubernetes/releases)의 안정적인 최신 부 릴리스입니다. 
  * 예를 들어, Kubernetes가 오늘 1.20.a를 도입하면 버전 1.20.a, 1.20.b, 1.19.c, 1.19.d, 1.18.e 및 1.18.f가 지원됩니다.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Azure Arc 지원 Kubernetes의 부 버전 릴리스는 얼마나 자주 제공되나요?

Azure Arc 지원 Kubernetes 에이전트의 부 버전 하나는 한 달에 한 번 정도 릴리스됩니다.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>공식 지원 기간에 포함되지 않는 에이전트 버전 또는 Kubernetes 버전을 사용하면 어떻게 되나요?

‘지원에 포함되지 않음’은 실행 중인 버전이 에이전트 및 업스트림 Kubernetes 클러스터의 “N-2” 지원되는 버전에 포함되지 않음을 의미합니다. 지원 문제를 계속하려면 클러스터와 에이전트를 지원되는 버전으로 업그레이드해야 합니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 통해 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* 이미 Kubernetes 클러스터와 연결된 Azure Arc가 있나요? [Arc 지원 Kubernetes 클러스터에 대한 구성을 만듭니다](./tutorial-use-gitops-connected-cluster.md).
* [Azure Policy를 사용하여 대규모로 구성을 적용](./use-azure-policy.md)하는 방법에 대해 알아봅니다.