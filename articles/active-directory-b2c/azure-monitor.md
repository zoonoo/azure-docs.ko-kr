---
title: Azure 모니터를 통해 Azure AD B2C 모니터링
titleSuffix: Azure AD B2C
description: 위임된 리소스 관리를 사용하여 Azure AD B2C 이벤트를 Azure 모니터로 기록하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 99e04c95156e40eed8c2b9aa88a2bee6f39e90c9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392887"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure 모니터를 통해 Azure AD B2C 모니터링

Azure 모니터를 사용하여 Azure Active Directory B2C(Azure AD B2C) 로그인 및 [감사](view-audit-logs.md) 로그를 다른 모니터링 솔루션으로 라우팅합니다. 장기 사용을 위해 로그를 유지하거나 타사 보안 정보 및 SIEM(이벤트 관리) 도구와 통합하여 사용자 환경에 대한 통찰력을 얻을 수 있습니다.

로그 이벤트를 다음으로 라우팅할 수 있습니다.

* Azure [저장소 계정](../storage/blobs/storage-blobs-introduction.md).
* Azure [이벤트 허브(및](../event-hubs/event-hubs-about.md) 스플렁크 및 스모 로직 인스턴스와 통합).
* [로그 분석 작업 영역(데이터를](../azure-monitor/platform/resource-logs-collect-workspace.md) 분석하고, 대시보드를 만들고, 특정 이벤트에 대해 경고).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 Azure PowerShell 모듈을 사용하여 Azure 리소스 관리자 템플릿을 배포합니다.

* [Azure PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps) 버전 6.13.1 이상

Azure PowerShell 모듈의 최신 버전을 포함하는 Azure [클라우드 셸을](https://shell.azure.com)사용할 수도 있습니다.

## <a name="delegated-resource-management"></a>위임된 리소스 관리

Azure AD B2C는 [Azure Active Directory 모니터링을](../active-directory/reports-monitoring/overview-monitoring.md)활용합니다. Azure AD B2C 테넌트 내에서 Azure Active Directory에서 *진단 설정을* 사용하려면 [위임된 리소스 관리를](../lighthouse/concepts/azure-delegated-resource-management.md)사용합니다.

Azure AD B2C 디렉터리(서비스 **공급자)의**사용자 또는 그룹이 Azure **구독(고객)을**포함하는 테넌트 내에서 Azure Monitor 인스턴스를 구성할 수 있는 권한을 부여합니다. 권한 부여를 만들려면 구독을 포함하는 Azure AD 테넌트에 [Azure 리소스 관리자](../azure-resource-manager/index.yml) 템플릿을 배포합니다. 다음 섹션에서는 프로세스를 안내합니다.

## <a name="create-or-choose-resource-group"></a>리소스 그룹 생성 또는 선택

Azure Monitor에서 데이터를 수신하는 대상 Azure 저장소 계정, 이벤트 허브 또는 로그 분석 작업 영역을 포함하는 리소스 그룹입니다. Azure 리소스 관리자 템플릿을 배포할 때 리소스 그룹 이름을 지정합니다.

[리소스 그룹을 만들거나](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) Azure AD B2C 테넌트가 포함된 디렉터리가 *아닌* Azure 구독을 포함하는 Azure Active Directory(Azure AD) 테넌트에서 기존 그룹을 선택합니다.

이 예제에서는 *미국 중부* 지역의 *azure-ad-b2c-monitor라는* 리소스 그룹을 사용합니다.

## <a name="delegate-resource-management"></a>리소스 관리 위임

다음으로 다음 정보를 수집합니다.

Azure AD B2C 디렉터리(테넌트 ID라고도 함)의 **디렉터리** ID입니다.

1. *사용자 관리자* 역할(또는 그 이상)을 가진 사용자로 [Azure 포털에](https://portal.azure.com/) 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 활성 디렉터리**선택, **속성**선택.
1. **디렉터리 ID를**기록합니다.

구독을 포함하는 디렉터리에서 이전에 만든 리소스 그룹에 *기여자* 권한을 부여하려는 Azure AD B2C 그룹 또는 사용자의 **개체 ID입니다.**

관리를 쉽게 만들려면 각 역할에 Azure AD 사용자 *그룹을* 사용하여 해당 사용자에게 직접 권한을 할당하는 대신 개별 사용자를 그룹에 추가하거나 제거할 수 있도록 하는 것이 좋습니다. 이 연습에서는 사용자를 추가합니다.

1. **Azure Active Directory가** Azure 포털에서 여전히 선택된 경우 **사용자를**선택한 다음 사용자를 선택합니다.
1. 사용자의 **개체 ID를**기록합니다.

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기

Azure AD 테넌트(고객)를 온보딩하려면 다음 정보를 사용하여 오퍼에 대한 [Azure 리소스 관리자 템플릿을](../lighthouse/how-to/onboard-customer.md) 만듭니다. **Customer** Azure `mspOfferName` `mspOfferDescription` 포털의 [서비스 공급자 페이지에서](../lighthouse/how-to/view-manage-service-providers.md) 제안 세부 정보를 볼 때 및 값이 표시됩니다.

| 필드   | 정의 |
|---------|------------|
| `mspOfferName`                     | 이 정의를 설명하는 이름입니다. 예를 들어 *Azure AD B2C 관리 서비스.* 이 값은 고객에게 제품의 제목으로 표시됩니다. |
| `mspOfferDescription`              | 오퍼에 대한 간략한 설명입니다. 예를 들어 *Azure AD B2C에서 Azure 모니터를 활성화합니다.*|
| `rgName`                           | Azure AD 테넌트에서 이전에 만든 리소스 그룹의 이름입니다. 예를 들어 *azure-ad-b2c-모니터.* |
| `managedByTenantId`                | Azure AD B2C 테넌트의 **디렉터리** ID(테넌트 ID라고도 함). |
| `authorizations.value.principalId` | 이 Azure 구독의 리소스에 액세스할 수 있는 B2C 그룹 또는 사용자의 **개체 ID입니다.** 이 연습에서는 이전에 기록한 사용자의 개체 ID를 지정합니다. |

Azure 리소스 관리자 템플릿 및 매개 변수 파일을 다운로드합니다.

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

그런 다음 매개 변수 파일을 이전에 기록한 값으로 업데이트합니다. 다음 JSON 코드 조각은 Azure 리소스 관리자 템플릿 매개 변수 파일의 예를 보여 주며 있습니다. 의 `authorizations.value.roleDefinitionId`경우 *참여자 역할에*대한 기본 제공 `b24988ac-6180-42a0-ab88-20f7382dd24c` [역할](../role-based-access-control/built-in-roles.md) 값을 사용합니다.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 배포

매개 변수 파일을 업데이트한 후에는 Azure 리소스 관리자 템플릿을 구독 수준 배포로 Azure 테넌트에 배포합니다. 이 배포는 구독 수준 배포이므로 Azure Portal에서 시작할 수 없습니다. Azure PowerShell 모듈 또는 Azure CLI를 사용하여 배포할 수 있습니다. Azure PowerShell 방법은 다음과 같습니다.

[Connect-AzAccount](/powershell/azure/authenticate-azureps)를 사용하여 구독이 포함된 디렉터리에 로그인합니다. 플래그를 `-tenant` 사용하여 올바른 디렉터리로 인증을 강제로 합니다.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet을 사용하여 현재 계정이 Azure AD 테넌트에서 액세스할 수 있는 구독을 나열합니다. Azure AD B2C 테넌트에 프로젝션할 구독의 ID를 기록합니다.

```PowerShell
Get-AzSubscription
```

그런 다음 Azure AD B2C 테넌트에 프로젝션할 구독으로 전환합니다.

``` PowerShell
Select-AzSubscription <subscription ID>
```

마지막으로 이전에 다운로드하고 업데이트한 Azure 리소스 관리자 템플릿 및 매개 변수 파일을 배포합니다. 에 `Location`이에 따라 및 `TemplateFile` `TemplateParameterFile` 값을 바꿉습니다.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

템플릿을 성공적으로 배포하여 다음과 유사한 출력을 생성합니다(간결성을 위해 잘린 출력):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

템플릿을 배포한 후 리소스 프로젝션을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 구독을 선택하려면 다음 섹션으로 이동하기 전에 몇 분(일반적으로 5분 이하)을 기다려야 할 수 있습니다.

## <a name="select-your-subscription"></a>구독 선택

템플릿을 배포하고 리소스 프로젝션이 완료될 때까지 몇 분 동안 기다렸다면 구독을 Azure AD B2C 디렉터리에 다음 단계와 연결합니다.

1. 현재 로그인한 경우 Azure 포털에서 **로그아웃합니다.** 이 단계와 다음 단계는 포털 세션에서 자격 증명을 새로 고치기 위해 수행됩니다.
1. Azure AD B2C 관리 계정으로 [Azure 포털에](https://portal.azure.com) 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. 구독이 포함된 디렉터리를 선택합니다.

    ![디렉터리 전환](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. 올바른 디렉터리 및 구독을 선택했는지 확인합니다. 이 예제에서는 모든 디렉터리 및 구독이 선택됩니다.

    ![디렉토리 & 구독 필터에서 선택한 모든 디렉터리](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>진단 설정 구성

진단 설정은 리소스에 대한 로그 및 메트릭을 보낼 위치를 정의합니다. 가능한 목적지는 다음과 같습니다.

- [Azure 저장소 계정](../azure-monitor/platform/resource-logs-collect-storage.md)
- [이벤트 허브](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 솔루션.
- [Log Analytics 작업 영역](../azure-monitor/platform/resource-logs-collect-workspace.md)

아직 지정하지 않은 경우 [Azure Resource Manager 템플릿에서](#create-an-azure-resource-manager-template)지정한 리소스 그룹에서 선택한 대상 유형의 인스턴스를 만듭니다.

### <a name="create-diagnostic-settings"></a>진단 설정 만들기

Azure 포털에서 [진단 설정을 만들](../active-directory/reports-monitoring/overview-monitoring.md) 준비가 되었습니다.

Azure AD B2C 활동 로그에 대한 모니터링 설정을 구성하려면 다음을 수행하십시오.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 활성 디렉터리** 선택
1. **모니터링** 아래에서 **진단 설정**을 선택합니다.
1. 리소스에 기존 설정이 있는 경우 이미 구성된 설정 목록이 표시됩니다. 진단 **추가 설정을** 선택하여 새 설정을 추가하거나 **설정을 편집하여** 기존 설정을 편집합니다. 각 설정에는 각 대상 유형 중 하나만 있을 수 있습니다.

    ![Azure 포털의 진단 설정 창](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 아직 이름이 없는 경우 설정을 지정합니다.
1. 로그를 보낼 각 대상에 대한 확인란을 선택합니다. 다음 표에 설명된 대로 설정을 지정하려면 **구성을** 선택합니다.

    | 설정 | Description |
    |:---|:---|
    | 스토리지 계정에 보관 | 저장소 계정의 이름입니다. |
    | 이벤트 허브로 스트림 | 이벤트 허브가 만들어지거나(이 경우 처음으로 스트리밍 로그를 하는 경우) 또는 스트리밍된 네임스페이스(해당 로그 범주를 이 네임스페이스로 스트리밍하는 리소스가 이미 있는 경우)입니다.
    | Log Analytics에 보내기 | 작업 영역의 이름입니다. |

1. **감사 로그** 및 **로그인 로그를 선택합니다.**
1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Monitor에서 진단 설정을 추가 및 구성하는 자세한 내용은 [Azure 리소스에서 리소스 로그를 수집 및 분석하는 자습서를](../azure-monitor/insights/monitor-azure-resource.md)참조하십시오.

Azure AD 로그를 이벤트 허브로 스트리밍하는 것에 대한 자세한 내용은 [자습서: Azure Active Directory 로그를 Azure 이벤트 허브로 스트리밍합니다.](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
