---
title: Azure Policy를 사용 하 여 AKS 모니터링 추가 기능 사용
description: Azure 사용자 지정 정책을 사용 하 여 AKS 모니터링 추가 기능을 사용 하도록 설정 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 302fdbbbcadf211339952f4b1bd97dcbb4ab1a85
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808302"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Azure Policy를 사용 하 여 AKS 모니터링 추가 기능 사용
이 문서에서는 Azure 사용자 지정 정책을 사용 하 여 AKS 모니터링 추가 기능을 사용 하는 방법을 설명 합니다. 모니터링 Addon 사용자 지정 정책은 구독 또는 리소스 그룹 범위에서 할당할 수 있습니다. Azure Log Analytics 작업 영역 및 AKS 클러스터가 다른 구독에 있는 경우 정책 할당에 사용 되는 관리 id에는 구독에 대 한 필수 역할 권한이 나 Log Analytics 작업 영역의 리소스에 대 한 최소한의 권한이 있어야 합니다. 마찬가지로 정책이 리소스 그룹으로 범위가 지정 된 경우 선택한 리소스 그룹 범위에 없는 작업 영역에 대 한 Log Analytics 작업 영역에 대 한 필수 역할 권한이 관리 되는 id에 있어야 합니다.

모니터링 추가 기능을 사용 하려면 Azure Policy에서 사용 하는 관리 id에 대해 다음 역할이 필요 합니다.

 - [azure-kubernetes](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-contributor-role)
 - [로그 분석-참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Azure Portal를 사용 하 여 정책 정의 만들기 및 할당

### <a name="create-policy-definition"></a>정책 정의 만들기

1. Azure 사용자 지정 정책 정의를 다운로드 하 여 AKS 모니터링 추가 기능을 사용 하도록 설정 합니다.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions정책 정의 만들기 대화 상자에서 다음 세부 정보로 이동 하 여 정책 정의를 만듭니다.
 
    - **정의 위치**: 정책 정의를 저장 해야 하는 Azure 구독을 선택 합니다.
    - **이름**: *(Preview) AKS-Addon*
    - **설명**: *지정 된 범위에서 azure Kubernetes 클러스터에 대 한 모니터링 기능을 사용 하도록 설정 하는 Azure 사용자 지정 정책*
    - **범주**: *기존 항목 사용* 을 선택 하 고 *Kubernetes* 에서 선택 합니다.
    - **정책 규칙**: 기존 샘플 규칙을 제거 하 고 위의 #1 단계에서 다운로드 한 *azurepolicy.js* 의 내용을 복사 합니다.

### <a name="assign-policy-definition-to-specified-scope"></a>지정 된 범위에 정책 정의 할당

> [!NOTE]
>  관리 id가 자동으로 생성 되 고 정책 정의에 지정 된 역할이 할당 됩니다.

1. 방금 만든 정책 정의 *(미리 보기) AKS 모니터링 추가* 기능을 선택 합니다.
4. *할당** *을 클릭 하 고 정책을 할당할 **범위** 를 지정 합니다. 
5. **다음** 을 클릭 하 고 Azure Log Analytics 작업 영역의 리소스 ID를 제공 합니다. 리소스 ID는이 형식 이어야 합니다 `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. 선택한 범위에서 기존 AKS 클러스터에 정책을 적용 하려는 경우 수정 작업을 만듭니다.
7. **검토 + 만들기** 옵션을 클릭 하 여 정책 할당을 만듭니다.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Azure CLI를 사용 하 여 정책 정의 만들기 및 할당

### <a name="create-policy-definition"></a>정책 정의 만들기

1. 다음 명령을 사용 하 여 Azure 사용자 지정 정책 정의 규칙 및 매개 변수 파일을 다운로드 합니다.

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. 다음 명령을 사용 하 여 정책 정의를 만듭니다.

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>지정 된 범위에 정책 정의 할당

- 다음 명령을 사용 하 여 정책 할당을 만듭니다.

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/overview.md)에 대해 자세히 알아보세요.
- [수정 보안의 작동](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)방식에 대해 알아봅니다.
- [컨테이너에 대 한 Azure Monitor](../insights/container-insights-overview.md)에 대해 자세히 알아보세요.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 설치합니다.

