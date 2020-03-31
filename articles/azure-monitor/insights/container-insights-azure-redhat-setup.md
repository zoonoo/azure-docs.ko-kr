---
title: 컨테이너에 대한 Azure 모니터를 통해 Azure Red Hat OpenShift 클러스터 구성 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Red Hat OpenShift에서 호스팅되는 Azure 모니터를 사용하여 Kubernetes 클러스터의 모니터링을 구성하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275517"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>컨테이너에 대한 Azure 모니터를 통해 Azure Red Hat OpenShift 클러스터 구성

컨테이너용 Azure 모니터는 AKS(Azure Kubernetes Service) 및 AKS 엔진 클러스터에 대한 풍부한 모니터링 환경을 제공합니다. 이 문서에서는 Azure [Red Hat OpenShift에서](../../openshift/intro-openshift.md) 호스팅되는 Kubernetes 클러스터를 모니터링하여 유사한 모니터링 환경을 구현하는 방법을 설명합니다.

>[!NOTE]
>Azure Red Hat OpenShift에 대한 지원은 현재 공개 미리 보기의 기능입니다.
>

컨테이너에 대한 Azure 모니터는 다음 지원되는 방법을 사용하여 새 서비스 또는 Azure Red Hat OpenShift의 하나 이상의 기존 배포에 대해 활성화할 수 있습니다.

- Azure 포털의 기존 클러스터 또는 Azure 리소스 관리자 템플릿을 사용하는 경우
- Azure 리소스 관리자 템플릿을 사용하거나 [Azure CLI를](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)사용하여 새 클러스터를 만드는 동안새 클러스터의 경우 .

## <a name="supported-and-unsupported-features"></a>지원 및 지원되지 않는 기능

컨테이너용 Azure Monitor는 개요 문서에 설명된 대로 [다음](container-insights-overview.md) 기능을 제외한 Azure Red Hat OpenShift 모니터링을 지원합니다.

- 라이브 데이터(미리 보기)
- 클러스터 노드 및 포드에서 [메트릭을 수집하고](container-insights-update-metrics.md) Azure Monitor 메트릭 데이터베이스에 저장합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 컨테이너에 대한 Azure Monitor의 기능을 활성화하고 액세스하려면 최소한 Azure 구독에서 Azure *기여자* 역할의 구성원이어야 하며 컨테이너용 Azure Monitor로 구성된 로그 분석 작업 영역의 [*로그 분석 기여자*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할의 구성원이어야 합니다.

- 모니터링 데이터를 보려면 컨테이너에 대한 Azure Monitor로 구성된 Log Analytics 작업 영역이 있는 [*Log Analytics 판독기*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할 권한의 구성원입니다.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 새 클러스터를 사용하도록 설정합니다.

모니터링을 사용하도록 설정한 Azure Red Hat OpenShift 클러스터를 배포하려면 다음 단계를 수행합니다. 계속하기 전에 자습서를 검토 [Azure Red Hat OpenShift 클러스터 만들기](../../openshift/tutorial-create-cluster.md#prerequisites) 환경을 올바르게 설정하도록 구성해야 하는 종속성을 이해합니다.

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 템플릿은 모니터링을 사용하도록 설정된 클러스터를 배포하도록 구성을 지정하고 다른 템플릿에는 다음을 지정하도록 구성한 매개 변수 값이 포함됩니다.

- Azure Red Hat 오픈시프트 클러스터 리소스 ID입니다.

- 클러스터가 배포되는 리소스 그룹입니다.

- [Azure Active Directory 테넌트 ID는](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) 하나 또는 이미 만든 테넌트를 만드는 단계를 수행한 후 기록되었습니다.

- [Azure Active Directory 클라이언트 응용 프로그램 ID는](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) 하나 또는 이미 만든 클라이언트 응용 프로그램 단계를 수행한 후 기록되었습니다.

- [Azure Active Directory 클라이언트 보안은](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) 하나 또는 이미 만든 단계를 만드는 단계를 수행한 후 기록되었습니다.

- [Azure AD 보안 그룹은](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) 하나 또는 이미 만든 단계를 수행한 후 기록되었습니다.

- 기존 로그 분석 작업 영역의 리소스 ID입니다.

- 클러스터에서 만들 마스터 노드 의 수입니다.

- 에이전트 풀 프로필의 계산 노드 수입니다.

- 에이전트 풀 프로필의 인프라 노드 수입니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.65 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

Azure PowerShell 또는 CLI를 사용하여 모니터링을 활성화하기 전에 로그 분석 작업 영역을 만들어야 합니다. 작업 영역을 만들려면 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)에서 설정할 수 있습니다.

1. 로컬 폴더인 Azure Resource Manager 템플릿 및 매개 변수 파일을 다운로드하여 저장하여 다음 명령을 사용하여 모니터링 추가 기능을 사용하여 클러스터를 만듭니다.

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Azure에 로그인

    ```azurecli
    az login    
    ```

    여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

3. 아직 클러스터가 없는 경우 클러스터에 대한 리소스 그룹을 만듭니다. Azure에서 OpenShift를 지원하는 Azure 지역 목록은 [지원되는 지역](../../openshift/supported-resources.md#azure-regions)을 참조하십시오.

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. JSON 매개 변수 파일 **newClusterWithMonitoringParam.json을** 편집하고 다음 값을 업데이트합니다.

    - *위치*
    - *클러스터 이름*
    - *aadTenantId*
    - *aad클라이언트Id*
    - *aad클라이언트시크릿*
    - *aad고객관리그룹Id*
    - *workspaceResourceId*
    - *마스터노드카운트*
    - *계산노드카운트*
    - *인프라노드카운트*

5. 다음 단계에서는 Azure CLI를 사용하여 모니터링을 사용하도록 설정한 클러스터를 배포합니다.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    출력은 다음과 유사합니다.

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>기존 클러스터에 대한 사용

다음 단계를 수행하여 Azure에 배포된 Azure Red Hat OpenShift 클러스터를 모니터링할 수 있습니다. Azure 포털에서 또는 제공된 템플릿을 사용하여 이 작업을 수행할 수 있습니다.

### <a name="from-the-azure-portal"></a>Azure Portal에서

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. Azure 포털 메뉴 또는 홈 페이지에서 **Azure 모니터를 선택합니다.** **인사이트** 섹션에서 **컨테이너**를 선택합니다.

3. **모니터 - 컨테이너** 페이지에서 **모니터링되지 않는 클러스터**를 선택합니다.

4. 모니터링되지 않는 클러스터 목록에서 목록에서 클러스터를 찾아 **사용 을**클릭합니다. **클러스터 유형**열 아래에서 **ARO** 값을 검색하여 목록에서 결과를 식별할 수 있습니다.

5. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너용 Azure Monitor에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    이 목록은 클러스터가 구독에 배포되는 기본 작업 영역 및 위치를 미리 선택합니다.

    ![모니터링되지 않는 클러스터에 대한 모니터링 지원](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요. RedHat OpenShift 클러스터가 배포된 것과 동일한 구독에서 작업 영역을 만들어야 합니다.

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="enable-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿사용

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- Azure RedHat 오픈시프트 클러스터 리소스 ID입니다.

- 클러스터가 배포되는 리소스 그룹입니다.

- Log Analytics 작업 영역.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.65 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

Azure PowerShell 또는 CLI를 사용하여 모니터링을 활성화하기 전에 로그 분석 작업 영역을 만들어야 합니다. 작업 영역을 만들려면 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)에서 설정할 수 있습니다.

1. 템플릿 및 매개 변수 파일을 다운로드하여 다음 명령을 사용하여 모니터링 추가 기능을 사용하여 클러스터를 업데이트합니다.

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Azure에 로그인

    ```azurecli
    az login    
    ```

    여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

3. Azure RedHat 오픈시프트 클러스터의 구독을 지정합니다.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. 다음 명령을 실행하여 클러스터 위치 및 리소스 ID를 식별합니다.

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. JSON 매개 변수 파일 기존 을 **편집ClusterParam.json** 및 값을 업데이트 *아라리소스 Id* 및 *araResoruceLocation*. **workspaceResourceId** 값은 Log Analytics 작업 영역의 전체 리소스 ID 이며, 작업 영역 이름을 포함합니다.

6. Azure CLI를 사용하여 배포하려면 다음 명령을 실행합니다.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    출력은 다음과 유사합니다.

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>다음 단계

- 모니터링을 사용하여 RedHat OpenShift 클러스터 및 해당 클러스터에서 실행 중인 워크로드의 상태 및 리소스 활용도를 수집할 수 있으므로 컨테이너에 Azure [Monitor를 사용하는 방법을](container-insights-analyze.md) 알아봅니다.

- 컨테이너에 대한 Azure 모니터를 사용하여 클러스터 모니터링을 중지하는 방법을 알아보려면 [Azure Red Hat OpenShift 클러스터 모니터링을 중지하는 방법을 참조하세요.](container-insights-optout-openshift.md)
