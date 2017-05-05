---
title: "Azure Kubernetes 클러스터 모니터링 - 운영 관리 | Microsoft Docs"
description: "Microsoft Operations Management Suite를 사용하여 Azure Container Service에서 Kubernetes 클러스터 모니터링"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 4e4a4f4e299dc2747eb48bbd2e064cd80783211c
ms.openlocfilehash: 46240f3dc99a8c8a103a1e7919ad4f5e7a8ea62a
ms.lasthandoff: 04/04/2017


---

# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Microsoft OMS(Operations Management Suite)를 사용하여 Azure Container Service 클러스터 모니터링

## <a name="prerequisites"></a>필수 조건
이 연습에서는 [Azure Container Service를 사용하여 Kubernetes 클러스터를 만들었다고](container-service-kubernetes-walkthrough.md) 가정합니다.

또한 `az` Azure CLI 및 `kubectl` 도구가 설치되어 있다고 가정합니다.

다음을 실행하여 `az` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ az --version
```

`az` 도구가 설치되어 있지 않으면 [여기](https://github.com/azure/azure-cli#installation)의 지침을 따르세요.

다음을 실행하여 `kubectl` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ kubectl version
```

`kubectl`이 설치되어 있지 않으면 다음을 실행할 수 있습니다.

```console
$ az acs kubernetes install-cli
```

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>OMS(Operations Management Suite)를 사용하여 컨테이너 모니터링

OMS(Microsoft Operations Management)는 온-프레미스 및 클라우드 인프라를 관리 및 보호하는 데 유용한 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. Container Solution은 컨테이너 인벤토리, 성능 및 로그를 한 곳에서 볼 수 있는 OMS Log Analytics에 포함된 솔루션입니다. 중앙 위치에서 로그를 확인하여 컨테이너를 감사하고 문제를 해결하며 호스트에서 매우 과도하게 사용되는 컨테이너를 찾을 수 있습니다.

![](media/container-service-monitoring-oms/image1.png)

Container Solution에 대한 자세한 내용은 [Container Solution Log Analytics](../log-analytics/log-analytics-containers.md)를 참조하세요.

## <a name="installing-oms-on-kubernetes"></a>Kubernetes에 OMS 설치

### <a name="obtain-your-workspace-id-and-key"></a>작업 영역 ID 및 키 가져오기
OMS 에이전트가 서비스와 통신하려면 작업 영역 ID 및 작업 영역 키로 구성되어야 합니다. 작업 영역 ID 및 키를 모두 가져오려면 <https://mms.microsoft.com>에서 OMS 계정을 만들어야 합니다.
다음 단계에 따라 계정을 만듭니다. 계정을 만들었으면 아래와 같이 **설정**, **연결된 원본**, **Linux 서버**를 차례로 클릭하여 ID 및 키를 획득해야 합니다.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>DaemonSet을 사용하여 OMS 에이전트 설치
DaemonSet은 Kubernetes가 클러스터의 각 호스트에서 컨테이너의 단일 인스턴스를 실행하기 위해 사용합니다.
모니터링 에이전트를 실행하는 데 완벽합니다.

다음은 [DaemonSet YAML 파일](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)입니다. `oms-daemonset.yaml`라는 파일로 저장하고 `WSID` 및 `KEY`에 대한 자리 표시자 값을 파일의 작업 영역 ID 및 키로 바꿉니다.

작업 영역 ID와 키를 DaemonSet 구성에 추가한 후 `kubectl` 명령줄 도구를 사용하여 클러스터에 OMS 에이전트를 설치할 수 있습니다.

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="conclusion"></a>결론
이것으로 끝입니다. 몇 분 후 OMS 대시보드로 이동하는 데이터를 볼 수 있습니다.

