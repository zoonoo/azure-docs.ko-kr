---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 Azure Red Hat OpenShift v4 .x 구성 | Microsoft Docs
description: 이 문서에서는 Azure Red Hat OpenShift 버전 4 이상에서 호스트 되 Azure Monitor를 사용 하 여 Kubernetes 클러스터의 모니터링을 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196297"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 Azure Red Hat OpenShift v4 .x 구성

컨테이너에 대 한 Azure Monitor는 AKS (Azure Kubernetes Service) 및 AKS 엔진 클러스터에 대 한 풍부한 모니터링 환경을 제공 합니다. 이 문서에서는 [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 버전 4.x에서 호스트 되는 Kubernetes 클러스터의 모니터링을 사용 하도록 설정 하 여 비슷한 모니터링 환경을 구현 하는 방법을 설명 합니다.

>[!NOTE]
>Azure Red Hat OpenShift에 대 한 지원은 현재 공개 미리 보기의 기능입니다.
>

컨테이너에 대 한 Azure Monitor는 다음과 같은 지원 되는 메서드를 사용 하 여 Azure Red Hat OpenShift .x의 기존 배포 하나 이상에 대해 사용 하도록 설정할 수 있습니다.

- 제공 된 bash 스크립트를 사용 하 고 [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)에서 실행 되는 기존 클러스터의 경우

## <a name="supported-and-unsupported-features"></a>지원 되거나 지원 되지 않는 기능

컨테이너에 대 한 Azure Monitor는 다음 기능을 제외 하 고 [개요](container-insights-overview.md) 문서에 설명 된 대로 Azure Red Hat openshift v4. x 모니터링을 지원 합니다.

- 라이브 데이터 (미리 보기)
- 클러스터 노드 및 pod에서 [메트릭을 수집](container-insights-update-metrics.md) 하 고 Azure Monitor 메트릭 데이터베이스에 저장 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure CLI 버전 2.0.72 이상

- [투구 3](https://helm.sh/docs/intro/install/) CLI 도구

- [Bash 버전 4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 명령줄 도구

- 컨테이너에 대 한 Azure Monitor의 기능을 사용 하도록 설정 하 고 액세스 하려면 최소한 Azure 구독에서 Azure *참가자* 역할의 멤버 여야 하 고 컨테이너에 대 한 Azure Monitor로 구성 된 Log Analytics 작업 영역의 구성원 인 [*Log Analytics 참가자*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할의 구성원 이어야 합니다.

- 모니터링 데이터를 보려면 컨테이너에 대해 Azure Monitor 구성 된 Log Analytics 작업 영역에 대 한 [*Log Analytics 읽기 권한자*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할 권한의 멤버입니다.

## <a name="enable-for-an-existing-cluster"></a>기존 클러스터에 대해 사용

제공 된 bash 스크립트를 사용 하 여 Azure에 배포 된 Azure Red Hat OpenShift 버전 4 이상 클러스터의 모니터링을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure에 로그인

    ```azurecli
    az login
    ```

2. 다음 명령을 사용 하 여 모니터링 추가 기능을 사용 하 여 클러스터를 구성 하는 로컬 폴더에 스크립트를 다운로드 하 고 저장 합니다.

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. 클러스터에 대해 성공한 `oc login` 후 클러스터의 **kube-컨텍스트** 를 식별 하려면 명령을 `kubectl config current-context` 실행 하 고 값을 복사 합니다.

### <a name="integrate-with-an-existing-workspace"></a>기존 작업 영역과 통합

다음 단계에서는 이전에 다운로드 한 bash 스크립트를 사용 하 여 클러스터를 모니터링할 수 있습니다. 기존 Log Analytics 작업 영역에 통합 하려면 다음 단계를 수행 하 여 `workspaceResourceId` 매개 변수에 필요한 Log Analytics 작업 영역의 전체 리소스 ID를 먼저 확인 한 다음 명령을 실행 하 여 지정 된 작업 영역에 대해 모니터링 추가 기능을 사용 하도록 설정 합니다. 지정할 작업 영역이 없는 경우 5 단계로 건너뛰고 스크립트에서 새 작업 영역을 만들도록 할 수 있습니다.

1. 다음 명령을 사용 하 여 액세스 권한이 있는 모든 구독을 나열 합니다.

    ```azurecli
    az account list --all -o table
    ```

    출력은 다음과 유사합니다.

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    **SubscriptionId**의 값을 복사 합니다.

2. 다음 명령을 사용 하 여 Log Analytics 작업 영역을 호스팅하는 구독으로 전환 합니다.

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 다음 예에서는 구독의 작업 영역 목록을 기본 JSON 형식으로 표시 합니다.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    출력에서 작업 영역 이름을 찾은 다음, 해당 Log Analytics 작업 영역의 전체 리소스 ID를 필드 **ID**로 복사 합니다.

4. 다음 명령을 실행 하 여 모니터링을 사용 하도록 설정 하 고 `workspaceResourceId` 매개 변수의 값을 바꿉니다. 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    예제:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="integrate-with-default-workspace"></a>기본 작업 영역과 통합

다음 단계에서는 다운로드 한 bash 스크립트를 사용 하 여 Azure Red Hat OpenShift v4. x 클러스터의 모니터링을 사용 하도록 설정 합니다. 이 예제에서는 기존 작업 영역을 미리 만들거나 지정할 필요가 없습니다. 이 명령은 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역 (이미 존재 하지 않는 경우)을 만들어 해당 프로세스를 간소화 합니다. 만든 기본 작업 영역은 *DefaultWorkspace-\<GUID>-\<Region>* 형식과 비슷합니다.  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="from-the-azure-portal"></a>Azure Portal에서

컨테이너에 대 한 Azure Monitor의 다중 클러스터 뷰는 모니터링 **되지 않는 클러스터** 탭에서 모니터링을 사용 하도록 설정 되지 않은 Azure Red Hat openshift 클러스터를 강조 표시 합니다. 클러스터 옆의 **사용** 옵션은 포털에서 모니터링 온 보 딩을 시작 하지 않습니다. 이 문서 앞의 단계에 따라 수동으로 모니터링을 사용 하도록 설정 하려면이 문서로 리디렉션됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal 메뉴 또는 홈 페이지에서 **Azure Monitor**를 선택 합니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다.

3. **모니터 - 컨테이너** 페이지에서 **모니터링되지 않는 클러스터**를 선택합니다.

4. 모니터링 되지 않는 클러스터 목록에서 목록에서 클러스터를 찾고 **사용**을 클릭 합니다. 열 **클러스터 유형**아래의 값 **ARO** 를 검색 하 여 목록에서 결과를 식별할 수 있습니다. **사용**을 클릭 하면이 문서로 리디렉션됩니다.

## <a name="next-steps"></a>다음 단계

- 모니터링을 사용 하 여 RedHat OpenShift 버전 4.x 클러스터 및 작업에서 실행 되는 작업의 상태 및 리소스 사용률을 수집할 수 있습니다. 컨테이너에 Azure Monitor [를 사용 하는 방법을](container-insights-analyze.md) 알아봅니다.

- 기본적으로 컨테이너 화 된 에이전트는 kube를 제외한 모든 네임 스페이스에서 실행 중인 모든 컨테이너의 stdout/stderr 컨테이너 로그를 수집 합니다. 특정 네임 스페이스 또는 네임 스페이스에 특정 한 컨테이너 로그 컬렉션을 구성 하려면 [컨테이너 Insights 에이전트 구성](container-insights-agent-config.md) 을 검토 하 여 원하는 데이터 수집 설정을 configmap 구성 파일에 구성 합니다.

- 클러스터에서 프로메테우스 메트릭을 스크랩 하 고 분석 하려면 [프로메테우스 메트릭 구성](container-insights-prometheus-integration.md) 을 검토 하세요.

- 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터 모니터링을 중지 하는 방법을 알아보려면 [Azure Red Hat OpenShift 클러스터 모니터링을 중지 하는 방법](container-insights-optout-openshift.md)을 참조 하세요.
