---
title: 하이브리드 Kubernetes 클러스터 모니터링을 중지 하는 방법 | Microsoft Docs
description: 이 문서는 컨테이너에 대 한 Azure Monitor를 사용 하 여 hybrid Kubernetes 클러스터의 모니터링을 중지 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 86a774737d5269d77c4053ad61ab870b13288aa7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84885863"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>하이브리드 클러스터 모니터링을 중지 하는 방법

Kubernetes 클러스터의 모니터링을 사용 하도록 설정한 후에는 더 이상 모니터링 하지 않기로 결정 한 경우 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터의 모니터링을 중지할 수 있습니다. 이 문서에서는 다음 환경에 대해이 작업을 수행 하는 방법을 보여 줍니다.

- Azure의 AKS 엔진 및 Azure Stack
- OpenShift 버전 4 이상
- Azure Arc 사용 Kubernetes(미리 보기)

## <a name="how-to-stop-monitoring-using-helm"></a>투구를 사용 하 여 모니터링을 중지 하는 방법

다음 단계는 다음 환경에 적용 됩니다.

- Azure의 AKS 엔진 및 Azure Stack
- OpenShift 버전 4 이상

1. 먼저 클러스터에 설치 된 컨테이너에 대 한 Azure Monitor를 식별 하려면 다음 투구 명령을 실행 합니다.

    ```
    helm list
    ```

    출력은 다음과 유사합니다.

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-1* 은 컨테이너의 Azure Monitor에 대 한 투구 차트 릴리스를 나타냅니다.

2. 차트 릴리스를 삭제 하려면 다음 투구 명령을 실행 합니다.

    `helm delete <releaseName>`

    예:

    `helm delete azmon-containers-release-1`

    그러면 클러스터에서 릴리스가 제거 됩니다. 다음 명령을 실행 하 여 확인할 수 있습니다 `helm list` .

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 투구는 삭제 한 후에도 릴리스를 추적 하므로, 클러스터의 기록을 감사 하 고를 사용 하 여 릴리스를 삭제 취소할 수 있습니다 `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Arc 활성화 된 Kubernetes에 대 한 모니터링을 중지 하는 방법

### <a name="using-powershell"></a>PowerShell 사용

1. 다음 명령을 사용 하 여 모니터링 추가 기능을 사용 하 여 클러스터를 구성 하는 로컬 폴더에 스크립트를 다운로드 하 고 저장 합니다.

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. `$azureArcClusterResourceId`에 해당 하는 값을 설정 하 `subscriptionId` `resourceGroupName` 고 `clusterName` Azure Arc 사용 Kubernetes 클러스터 리소스의 리소스 ID를 나타내는 변수를 구성 합니다.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `$kubeContext`명령을 실행 하 여 클러스터의 **kube 컨텍스트로** 변수를 구성 합니다 `kubectl config get-contexts` . 현재 컨텍스트를 사용 하려면 값을로 설정 `""` 합니다.

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 다음 명령을 실행 하 여 클러스터의 모니터링을 중지 합니다.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

### <a name="using-bash"></a>Bash 사용

1. 다음 명령을 사용 하 여 모니터링 추가 기능을 사용 하 여 클러스터를 구성 하는 로컬 폴더에 스크립트를 다운로드 하 고 저장 합니다.

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. `azureArcClusterResourceId`에 해당 하는 값을 설정 하 `subscriptionId` `resourceGroupName` 고 `clusterName` Azure Arc 사용 Kubernetes 클러스터 리소스의 리소스 ID를 나타내는 변수를 구성 합니다.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `kubeContext`명령을 실행 하 여 클러스터의 **kube 컨텍스트로** 변수를 구성 합니다 `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 클러스터 모니터링을 중지 하기 위해 배포 시나리오에 따라 다양 한 명령이 제공 됩니다.

    다음 명령을 실행 하 여 현재 컨텍스트를 사용 하는 클러스터의 모니터링을 중지 합니다.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    다음 명령을 실행 하 여 컨텍스트를 지정 하 여 클러스터 모니터링을 중지 합니다.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

## <a name="next-steps"></a>다음 단계

클러스터 모니터링을 지원 하기 위해 Log Analytics 작업 영역을 만들었지만 더 이상 필요 하지 않은 경우 수동으로 삭제 해야 합니다. 작업 영역을 삭제 하는 방법에 익숙하지 않은 경우 [Azure Log Analytics 작업 영역 삭제](../../log-analytics/log-analytics-manage-del-workspace.md)를 참조 하세요.
