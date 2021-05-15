---
title: Azure Policy를 이용해 AKS 모니터링 추가 기능을 사용으로 설정
description: Azure Custom Policy를 사용하여 AKS 모니터링 추가 기능을 사용으로 설정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713901"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Azure Policy를 이용해 AKS 모니터링 추가 기능 사용
이 문서에서는 Azure Custom Policy를 사용하여 AKS 모니터링 추가 기능을 사용으로 설정하는 방법을 설명합니다. 모니터링 추가 기능 사용자 지정 정책은 구독 또는 리소스 그룹 범위에서 할당할 수 있습니다. Azure Log Analytics 작업 영역 및 AKS 클러스터가 다른 구독에 있는 경우, 정책 할당에서 사용하는 관리 ID에는 구독에 대한 필수 역할 권한이나 Log Analytics 작업 영역의 리소스에 대한 최소한의 권한이 있어야 합니다. 마찬가지로 정책이 리소스 그룹으로 범위가 지정된 경우, 선택한 리소스 그룹 범위에 없는 Log Analytics 작업 영역에 대한 필수 역할 권한이 관리 ID에 있어야 합니다.

모니터링 추가 기능을 사용하려면 Azure Policy에서 사용하는 관리 ID에 대해 다음 역할이 필요합니다.

 - [azure-kubernetes-service-contributor-role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [log-analytics-contributor](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Azure Portal을 사용하여 정책 정의 만들기 및 할당

### <a name="create-policy-definition"></a>정책 정의 만들기

1. Azure Custom Policy 정의를 다운로드하여 AKS 모니터링 추가 기능을 사용하도록 설정합니다.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions 로 이동하여 정책 정의 만들기 대화 상자에서 다음 세부 정보로 정책 정의를 만듭니다.
 
    - **정의 위치**: 정책 정의를 저장해야 하는 Azure 구독을 선택합니다.
    - **이름**: *(미리 보기)AKS-Monitoring-Addon*
    - **설명**: *지정된 범위에서 Azure Kubernetes Cluster(s)에 대한 모니터링 추가 기능을 사용하도록 설정하는 Azure Custom Policy*
    - **범주**: *기존 항목 사용* 을 선택하고, 드롭다운에서 *Kubernetes* 를 선택합니다.
    - **정책 규칙**: 기존 샘플 규칙을 제거하고, 위의 1단계에서 다운로드한 *azurepolicy.json* 의 내용을 복사합니다.

### <a name="assign-policy-definition-to-specified-scope"></a>지정된 범위에 정책 정의 할당

> [!NOTE]
>  관리 ID가 자동으로 생성되고 정책 정의에 지정된 역할이 할당됩니다.

1. 방금 만든 정책 정의 *(미리 보기) AKS 모니터링 추가 기능* 을 선택합니다.
4. *할당** *을 클릭하고 정책을 할당할 **범위** 를 지정합니다. 
5. **다음** 을 클릭하고 Azure Log Analytics 작업 영역의 리소스 ID를 제공합니다. 리소스 ID는 이 양식 `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>`에 있어야 합니다.
6. 선택한 범위에서 기존 AKS 클러스터에 정책을 적용하려는 경우 수정 작업을 만듭니다.
7. **검토 + 만들기** 옵션을 클릭하여 정책 할당을 만듭니다.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Azure Portal을 사용하여 정책 정의 만들기 및 할당

### <a name="create-policy-definition"></a>정책 정의 만들기

1. 다음 명령을 사용하여 Azure 사용자 지정 정책 정의 규칙 및 매개 변수 파일을 다운로드합니다.

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. 다음 명령을 사용하여 정책 정의를 만듭니다.

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>지정된 범위에 정책 정의 할당

- 다음 명령을 사용하여 정책 할당을 만듭니다.

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/overview.md)에 대해 자세히 알아봅니다.
- [수정 보안의 작동 방식](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)을 알아봅니다.
- [컨테이너 인사이트](./container-insights-overview.md)에 대해 자세히 알아봅니다.
- [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.