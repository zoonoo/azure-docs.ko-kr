---
title: Azure Arc 사용 Kubernetes 에이전트 업그레이드
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 사용 Kubernetes에 대 한 컨트롤 에이전트 업그레이드
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너, 에이전트, 업그레이드
ms.openlocfilehash: 172654f655e594c295a8807b417c32d7849bd4b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121918"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Azure Arc 사용 Kubernetes 에이전트 업그레이드

Azure Arc enabled Kubernetes는 해당 에이전트에 대 한 자동 업그레이드 및 수동 업그레이드 기능을 제공 합니다. 자동 업그레이드를 사용 하지 않도록 설정 하 고 대신 수동 업그레이드를 사용 하는 경우 Arc 에이전트 및 기본 Kubernetes 클러스터에 대 한 버전 지원 정책이 적용 됩니다.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>클러스터를 Azure Arc에 연결할 때 자동 업그레이드 설정/해제

Azure Arc enabled Kubernetes는 기본 자동 업그레이드 기능을 제공 하는 에이전트를 제공 합니다.

다음 명령은 자동 업그레이드를 **사용** 하 여 클러스터를 Azure Arc에 연결 합니다.

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

자동 업그레이드를 사용 하는 경우 에이전트는 최신 버전의 에이전트에 대 한 가용성을 위해 매시간 Azure를 폴링합니다. 에이전트가 사용 가능한 최신 버전을 찾으면 Azure Arc 에이전트에 대 한 투구 차트 업그레이드를 트리거합니다.

자동 업그레이드를 옵트아웃 하려면 `--disable-auto-upgrade` 클러스터를 Azure Arc에 연결 하는 동안 매개 변수를 지정 합니다. 다음 명령은 자동 업그레이드를 **사용 하지 않도록 설정** 하 여 클러스터를 Azure Arc에 연결 합니다.

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> 자동 업그레이드를 사용 하지 않도록 설정 하려는 경우 Azure Arc 사용 Kubernetes에 대 한 [버전 지원 정책](#version-support-policy) 을 참조 하세요.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>클러스터를 Azure Arc에 연결한 후 자동 업그레이드 설정/해제

클러스터를 Azure Arc에 연결한 후 아래와 같이 명령을 사용 하 여 자동 업그레이드 기능을 설정/해제할 수 있습니다 `az connectedk8s update` .

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>수동으로 에이전트 업그레이드

에이전트의 자동 업그레이드를 사용 하지 않도록 설정한 경우 `az connectedk8s upgrade` 아래와 같이 명령을 사용 하 여 이러한 에이전트에 대 한 업그레이드를 수동으로 시작할 수 있습니다.

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Azure Arc enabled Kubernetes는 에이전트의 버전을 관리 하기 위해의 표준 [의미 체계 버전 관리 체계](https://semver.org/) 를 따릅니다 `MAJOR.MINOR.PATCH` . 

버전의 각 번호는 이전 버전과의 호환성을 나타냅니다.

* 호환 되지 않는 API 업데이트 또는 이전 버전과의 호환성이 손상 된 경우 **주 버전이** 변경 됩니다.
* 기능 변경 내용이 다른 부 릴리스와 이전 버전과 호환 되는 경우 **부 버전이** 변경 됩니다.
* 이전 버전과 호환 되는 버그 수정이 수행 될 때 **패치 버전이** 변경 됩니다.

## <a name="version-support-policy"></a>버전 지원 정책

지원 문제를 만들 때 Azure Arc enabled Kubernetes는 다음 버전 지원 정책을 구현 합니다.

* Azure Arc enabled Kubernetes 에이전트에는 "N-2"의 지원 창이 있습니다. 여기서 ' N '은 에이전트의 최신 부 버전입니다. 
  * 예를 들어 Azure Arc enabled Kubernetes에서 0.28를 도입 하는 경우 0.28, 0.28, 0.27, 0.27, 0.26, 0.26 및은 Azure Arc에서 지원 됩니다.

* Azure Arc에 연결 된 Kubernetes 클러스터에는 "N-2"의 지원 창이 있습니다. 여기서 ' N '은 [업스트림 Kubernetes](https://github.com/kubernetes/kubernetes/releases)의 안정적인 최신 보조 릴리스입니다. 
  * 예를 들어 Kubernetes이 1.20를 도입 하는 경우 1.20, 1.20, 1.19, 1.19, 1.18, 1.18 및가 지원 됩니다.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Azure Arc 사용 Kubernetes의 부 버전 릴리스는 얼마나 자주 사용 가능 한가요?

한 달에 한 번 Azure Arc enabled Kubernetes 에이전트의 한 부 버전이 릴리스됩니다.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>공식 지원 창 외부에서 에이전트 버전 또는 Kubernetes 버전을 사용 하는 경우 어떻게 되나요?

' 지원 외부 '는 실행 중인 버전이 "N-2" 지원 되는 버전의 에이전트 및 업스트림 Kubernetes 클러스터 외부에 있음을 의미 합니다. 지원 문제를 계속 하려면 클러스터와 에이전트를 지원 되는 버전으로 업그레이드 하 라는 메시지가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* [Kubernetes 클러스터를 Azure Arc에 연결 하는](./connect-cluster.md)빠른 시작을 안내 합니다.
* Kubernetes 클러스터에 연결 된 Azure Arc가 이미 있나요? [Arc Enabled Kubernetes 클러스터에 대 한 구성을 만듭니다](./use-gitops-connected-cluster.md).
* Azure Policy를 사용 하 여 [대규모 구성을 적용](./use-azure-policy.md)하는 방법을 알아봅니다.