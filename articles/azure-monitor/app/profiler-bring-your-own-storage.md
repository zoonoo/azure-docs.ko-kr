---
title: Profiler 및 스냅샷 디버거에 대한 BYOS(Bring Your Own Storage) 구성
description: Profiler 및 스냅샷 디버거에 대한 BYOS(Bring Your Own Storage) 구성
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 830e0904d6aa905a621b245adae2b2d94b46e243
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109847169"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Application Insights Profiler 및 스냅샷 디버거에 대한 BYOS(Bring Your Own Storage)를 구성합니다.

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>BYOS(Bring Your Own Storage)란 무엇이며 왜 필요한가요? 
Application Insights Profiler 또는 스냅샷 디버거를 사용하는 경우 애플리케이션에서 생성된 아티팩트는 공용 인터넷을 통해 Azure Storage 계정에 업로드됩니다. 이러한 계정은 유료이며 처리 및 분석을 위해 Microsoft에서 제어합니다. Microsoft는 이러한 아티팩트에 대한 미사용 데이터 암호화 및 수명 관리 정책을 제어합니다.

BYOS(Bring Your Own Storage)를 사용하면 이러한 아티팩트는 사용자가 제어하는 스토리지 계정에 업로드됩니다. 즉, 미사용 데이터 암호화 정책, 수명 관리 정책 및 네트워크 액세스를 제어할 수 있습니다. 그러나 해당 스토리지 계정과 관련된 비용은 사용자가 부담합니다.

> [!NOTE]
> Private Link를 사용하도록 설정하는 경우 BYOS는 필수 조건입니다. Application Insights용 Private Link에 대한 자세한 내용은 [설명서](../logs/private-link-security.md)를 참조하세요.
>
> 고객 관리형 키를 사용하도록 설정하는 경우 BYOS는 필수 조건입니다. Application Insights용 고객 관리형 키에 대한 자세한 내용은 [설명서](../logs/customer-managed-keys.md)를 참조하세요.

## <a name="how-will-my-storage-account-be-accessed"></a>내 스토리지 계정에는 어떻게 액세스하나요?
1. Virtual Machines 또는 App Service에서 실행되는 에이전트는 아티팩트(프로필, 스냅샷 및 기호)를 계정의 Blob 컨테이너에 업로드합니다. 이 프로세스에는 스토리지 계정에서 새 Blob에 대한 SAS(공유 액세스 서명) 토큰을 가져오기 위해 Application Insights Profiler 또는 스냅샷 디버거 서비스에 연결하는 과정이 포함됩니다.
1. Application Insights Profiler 또는 스냅샷 디버거 서비스는 들어오는 Blob을 분석하고 분석 결과 및 로그 파일을 Blob Storage에 다시 씁니다. 사용 가능한 컴퓨팅 용량에 따라 업로드 후 언제든지 이 프로세스가 발생할 수 있습니다.
1. Profiler 추적 또는 스냅샷 디버거 분석을 볼 때 이 서비스는 Blob Storage에서 분석 결과를 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항
* 스토리지 계정을 Application Insights 리소스와 동일한 위치에 만들어야 합니다. 예: Application Insights 리소스가 미국 서부 2에 있으면 스토리지 계정도 미국 서부 2에 있어야 합니다. 
* 스토리지 계정에서 액세스 제어(IAM) UI를 통해 AAD 애플리케이션 'Diagnostic Services Trusted Storage Access'에 '스토리지 Blob 데이터 참가자' 역할을 부여합니다.
* Private Link를 사용하도록 설정한 경우 Virtual Network에서 신뢰할 수 있는 Microsoft 서비스로의 연결을 허용하도록 추가 설정을 구성합니다. 

## <a name="how-to-enable-byos"></a>BYOS를 사용하도록 설정하는 방법

### <a name="create-storage-account"></a>스토리지 계정 만들기
Application Insights 리소스와 동일한 위치에 새 스토리지 계정(없는 경우)을 만듭니다.
Application Insights 리소스가 `West US 2`에 있으면 스토리지 계정이 `West US 2`에 있어야 합니다.

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>스토리지 계정에 진단 서비스에 대한 액세스 권한 부여
BYOS 스토리지 계정은 Application Insights 리소스에 연결됩니다. 각 Application Insights 리소스에 스토리지 계정이 하나만 있을 수 있으며 둘 다 동일한 위치에 있어야 합니다. 둘 이상의 Application Insights 리소스에 동일한 스토리지 계정을 사용할 수 있습니다.

먼저 Application Insights Profiler 및 스냅샷 디버거 서비스에 스토리지 계정에 대한 액세스 권한을 부여해야 합니다. 액세스 권한을 부여하려면 그림 1.0에 표시된 것처럼 스토리지 계정에서 액세스 제어(IAM) 페이지를 통해 `Diagnostic Services Trusted Storage Access`라는 AAD 애플리케이션에 `Storage Blob Data Contributor` 역할을 추가합니다. 

단계: 
1. '역할 할당 추가' 섹션에서 '추가' 단추를 클릭합니다. 
1. "Storage Blob 데이터 기여자" 역할 선택 
1. '다음에 대한 액세스 할당' 섹션에서 'Azure AD 사용자, 그룹 또는 서비스 주체'를 선택합니다. 
1. 'Diagnostic Services Trusted Storage Access' 앱을 검색하여 선택합니다. 
1. 변경 내용 저장

_![그림 1.0](media/profiler-bring-your-own-storage/figure-10.png)_
_그림 1.0_ 

추가한 역할은 아래 그림 1.1과 같이 '역할 할당' 섹션 아래에 표시됩니다. 
_![그림 1.1](media/profiler-bring-your-own-storage/figure-11.png)_
_그림 1.1_ 

또한 Private Link를 사용하는 경우 Virtual Network에서 신뢰할 수 있는 Microsoft 서비스로의 연결을 허용하는 추가 구성이 하나 필요합니다. [스토리지 네트워크 보안 설명서](../../storage/common/storage-network-security.md#trusted-microsoft-services)를 참조하세요.

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>스토리지 계정을 Application Insights 리소스와 연결
BYOS에서 코드 수준 진단(Profiler/디버거)을 구성하려면 다음 세 가지 옵션을 사용할 수 있습니다.

* Azure PowerShell cmdlet 사용
* Azure CLI(명령줄 인터페이스) 사용
* Azure Resource Manager 템플릿 사용

#### <a name="configure-using-azure-powershell-cmdlets"></a>Azure PowerShell cmdlet을 사용하여 구성

1. Az PowerShell 4.2.0 이상을 설치했는지 확인합니다.

    Azure PowerShell을 설치하려면 [공식 Azure PowerShell 설명서](/powershell/azure/install-az-ps)를 참조하세요.

1. Application Insights PowerShell 확장을 설치합니다.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Azure 계정으로 로그인합니다.
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    로그인하는 방법에 대한 자세한 내용은 [Connect-AzAccount 설명서](/powershell/module/az.accounts/connect-azaccount)를 참조하세요.

1. Application Insights 리소스에 연결된 이전 스토리지 계정을 제거합니다.

    패턴
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    예:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. 스토리지 계정을 Application Insights 리소스와 연결합니다.
    
    패턴
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    예:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Azure CLI를 사용하여 구성

1. Azure CLI를 설치했는지 확인합니다.

    Azure CLI를 설치하려면 [공식 Azure CLI 설명서](/cli/azure/install-azure-cli)를 참조하세요.

1. Application Insights CLI 확장을 설치합니다.
    ```azurecli
    az extension add -n application-insights
    ```

1. 스토리지 계정을 Application Insights 리소스와 연결합니다.

    패턴
    ```azurecli
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    예:
    ```azurecli
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    예상 출력:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Application Insights 리소스에 연결된 스토리지 계정에 대한 업데이트를 수행하려면 [Application Insights CLI 설명서](/cli/azure/monitor/app-insights/component/linked-storage)를 참조하세요.

#### <a name="configure-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 구성

1. 다음 내용(byos.template.json)을 사용하여 Azure Resource Manager 템플릿 파일을 만듭니다.
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 다음 PowerShell 명령을 실행하여 위의 템플릿(연결된 스토리지 계정 만들기)을 배포합니다.

    패턴
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    예:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. PowerShell 콘솔에서 메시지가 표시되면 다음 매개 변수를 제공합니다.
    
    |           매개 변수           |                                설명                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | BYOS를 사용하도록 설정할 Application Insights 리소스의 이름입니다.            |
    | storage_account_name          | BYOS로 사용할 스토리지 계정 리소스의 이름입니다. |
    
    예상 출력:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Azure Portal을 통해 관심 워크로드에서 코드 수준 진단(Profiler/디버거)을 사용하도록 설정합니다. (App Service > Application Insights) _![그림 2.0](media/profiler-bring-your-own-storage/figure-20.png)_
_그림 2.0_

## <a name="troubleshooting"></a>문제 해결
### <a name="template-schema-schema_uri-isnt-supported"></a>템플릿 스키마 '{schema_uri}'는 지원되지 않습니다.
* 템플릿의 `$schema` 속성이 유효한지 확인합니다. 다음 패턴을 따라야 합니다. `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* 템플릿의 `schema_version`이 유효한 값 범위에 있는지 확인합니다. `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01`.
    오류 메시지:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>위치 '{location}'에 등록된 리소스 공급자를 찾을 수 없습니다.
* 리소스 `microsoft.insights/components`의 `apiVersion`이 `2015-05-01`인지 확인합니다.
* 리소스 `linkedStorageAccount`의 `apiVersion`이 `2020-03-01-preview`인지 확인합니다.
    오류 메시지:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>스토리지 계정 위치는 AI 구성 요소 위치와 일치해야 합니다.
* Application Insights 리소스의 위치가 스토리지 계정과 동일한지 확인합니다.
    오류 메시지:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

일반적인 Profiler 문제 해결에 대해서는 [Profiler 문제 해결 설명서](profiler-troubleshooting.md)를 참조하세요.

일반적인 스냅샷 디버거 문제 해결에 대해서는 [스냅샷 디버거 문제 해결 설명서](snapshot-debugger-troubleshoot.md)를 참조하세요. 

## <a name="faqs"></a>FAQ
* Profiler 또는 스냅샷을 사용하도록 설정하고 BYOS를 사용하도록 설정하면 내 데이터가 내 스토리지 계정으로 마이그레이션되나요?
    아니요, 그렇지 않습니다.

* BYOS가 미사용 데이터 암호화와 고객 관리형 키에서 작동하나요?
    예, 정확히 말하면 BYOS는 Profiler/디버거와 고객 관리형 키를 사용하도록 설정하기 위한 필수 조건입니다.

* BYOS는 인터넷에서 격리된 환경에서 작동하나요?
    예, 실제로 BYOS는 격리된 네트워크 시나리오에 대한 요구 사항입니다.

* 고객 관리형 키와 Private Link를 모두 사용하도록 설정한 경우 BYOS가 작동하나요? 
    예, 가능합니다.

* BYOS를 사용하도록 설정한 경우 수집된 내 데이터를 저장하기 위해 진단 서비스 스토리지 계정을 다시 사용할 수 있나요? 
    예, 가능합니다. 하지만 현재는 BYOS로부터 데이터 마이그레이션을 지원하지 않습니다.

* BYOS를 사용하도록 설정한 후에는 모든 관련 비용, 즉 스토리지 및 네트워킹 비용을 부담하게 되나요? 
    _예_
