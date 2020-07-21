---
title: Profiler &에 대 한 BYOS 구성 (사용자 고유의 저장소 가져오기) 스냅숏 디버거
description: Profiler &에 대 한 BYOS 구성 (사용자 고유의 저장소 가져오기) 스냅숏 디버거
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 04/14/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 50dcd3f438645c99e0ed3cfdded7a101ee5f1852
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539859"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Application Insights Profiler 및 스냅숏 디버거에 대 한 사용자 고유의 저장소 (BYOS)를 구성 합니다.

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>사용자 고유의 저장소 (BYOS)를 가져오는 것은 무엇 이며 필요한 이유는 무엇 인가요? 
Application Insights Profiler 또는 스냅숏 디버거를 사용 하는 경우 응용 프로그램에서 생성 된 아티팩트는 공용 인터넷을 통해 Azure storage 계정에 업로드 됩니다. 이러한 계정은 Microsoft에서 처리 및 분석을 위해 지불 하 고 제어 합니다. Microsoft는 이러한 아티팩트에 대 한 미사용 암호화 및 수명 관리 정책을 제어 합니다.

사용자 고유의 저장소를 사용 하 여 이러한 아티팩트는 사용자가 제어 하는 저장소 계정에 업로드 됩니다. 즉, rest 암호화 정책, 수명 관리 정책 및 네트워크 액세스를 제어 합니다. 그러나 해당 저장소 계정과 관련 된 비용을 담당 하 게 됩니다.

> [!NOTE]
> 개인 링크를 사용 하도록 설정 하는 경우 사용자 고유의 저장소를 요구 해야 합니다. Application Insights에 대 한 개인 링크에 대 한 자세한 내용은 [설명서를 참조 하십시오.](../platform/private-link-security.md)
>
> 고객 관리 키를 사용 하도록 설정 하는 경우 사용자 고유의 저장소를 요구 해야 합니다. Application Insights에 대 한 고객 관리 키에 대 한 자세한 내용은 [설명서를 참조 하세요.](../platform/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>내 저장소 계정에 액세스 하는 방법
1. Virtual Machines 또는 App Service에서 실행 되는 에이전트는 아티팩트 (프로필, 스냅숏 및 기호)를 계정의 blob 컨테이너에 업로드 합니다. 이 프로세스에는 저장소 계정에서 새 blob에 대 한 SAS (공유 액세스 서명) 토큰을 가져오기 위해 Application Insights Profiler 또는 스냅숏 디버거 서비스에 연결 하는 과정이 포함 됩니다.
1. Application Insights Profiler 또는 스냅숏 디버거 서비스는 들어오는 blob를 분석 하 고 분석 결과 및 로그 파일을 blob 저장소에 다시 씁니다. 사용 가능한 계산 용량에 따라 업로드 후 언제 든 지이 프로세스가 발생할 수 있습니다.
1. 프로파일러 추적 또는 스냅숏 디버거 분석을 볼 때이 서비스는 blob 저장소에서 분석 결과를 인출 합니다.

## <a name="prerequisites"></a>사전 준비 사항
* Application Insights 리소스와 동일한 위치에 저장소 계정을 만들어야 합니다. 예: Application Insights 리소스가 미국 서 부 2에 있는 경우 저장소 계정은 미국 서 부에도 있어야 합니다. 
* Access Control (IAM) UI를 통해 저장소 계정의 AAD 응용 프로그램 "진단 서비스의 신뢰할 수 있는 저장소 액세스"에 "저장소 Blob 데이터 참가자" 역할을 부여 합니다.
* 개인 링크를 사용 하도록 설정한 경우 Virtual Network에서 신뢰할 수 있는 Microsoft 서비스에 대 한 연결을 허용 하도록 추가 설정을 구성 합니다. 

## <a name="how-to-enable-byos"></a>BYOS를 사용 하도록 설정 하는 방법

### <a name="create-storage-account"></a>스토리지 계정 만들기
Application Insights 리소스와 동일한 위치에 새 저장소 계정 (없는 경우)을 만듭니다.
Application Insights 리소스가 켜져 있으면 `West US 2` 저장소 계정이에 있어야 합니다 `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>저장소 계정에 진단 서비스에 대 한 액세스 권한 부여
BYOS 저장소 계정은 Application Insights 리소스에 연결 됩니다. Application Insights 리소스 마다 저장소 계정이 하나만 있을 수 있으며 둘 다 동일한 위치에 있어야 합니다. 둘 이상의 Application Insights 리소스에 동일한 저장소 계정을 사용할 수 있습니다.

먼저 Application Insights Profiler 및 스냅숏 디버거 서비스에 저장소 계정에 대 한 액세스 권한을 부여 해야 합니다. 액세스 권한을 부여 하려면 `Storage Blob Data Contributor` `Diagnostic Services Trusted Storage Access` 그림 1.0에 표시 된 것 처럼 저장소 계정의 ACCESS CONTROL (IAM) 페이지를 통해 명명 된 AAD 응용 프로그램에 역할을 추가 합니다. 

단계: 
1. "역할 할당 추가" 섹션에서 "추가" 단추를 클릭 합니다. 
1. "Storage Blob 데이터 기여자" 역할 선택 
1. "액세스 할당 대상" 섹션에서 "Azure AD 사용자, 그룹 또는 서비스 사용자"를 선택 합니다. 
1. 검색 & "진단 서비스의 신뢰할 수 있는 저장소 액세스" 앱을 선택 합니다. 
1. 변경 내용 저장

_ ![ 그림 1.0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _그림 1.0_ 

역할을 추가한 후 아래 그림 1.1과 같이 "역할 할당" 섹션 아래에 표시 됩니다. 
_ ![ 그림 1.1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _그림 1.1_ 

또한 개인 링크를 사용 하는 경우 Virtual Network에서 신뢰할 수 있는 Microsoft 서비스에 대 한 연결을 허용 하는 추가 구성이 하나 필요 합니다. [저장소 네트워크 보안 설명서](../../storage/common/storage-network-security.md#trusted-microsoft-services)를 참조 하세요.

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>저장소 계정을 Application Insights 리소스와 연결
코드 수준 진단 (프로파일러/디버거)에 대해 BYOS를 구성 하려면 다음 두 가지 옵션을 사용할 수 있습니다.

* Azure PowerShell Cmdlet 사용
* Azure CLI (명령줄 인터페이스) 사용
* Azure Resource Manager 템플릿 사용

#### <a name="configure-using-azure-powershell-cmdlets"></a>Azure PowerShell Cmdlet을 사용 하 여 구성

1. Az PowerShell 4.2.0 이상을 설치 했는지 확인 합니다.

    Azure PowerShell를 설치 하려면 [공식 Azure PowerShell 설명서](/powershell/azure/install-az-ps)를 참조 하세요.

1. Application Insights PowerShell 확장을 설치 합니다.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Azure 계정으로 로그인
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    로그인 하는 방법에 대 한 자세한 내용은 [AzAccount 설명서](/powershell/module/az.accounts/connect-azaccount)를 참조 하세요.

1. Application Insights 리소스에 연결 된 이전 저장소 계정을 제거 합니다.

    패턴
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    예제:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. 저장소 계정을 Application Insights 리소스와 연결 합니다.
    
    패턴
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    예제:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Azure CLI를 사용하여 구성

1. Azure CLI를 설치 했는지 확인 합니다.

    Azure CLI를 설치 하려면 [공식 Azure CLI 설명서](/cli/azure/install-azure-cli)를 참조 하세요.

1. Application Insights CLI 확장을 설치 합니다.
    ```powershell
    az extension add -n application-insights
    ```

1. 저장소 계정을 Application Insights 리소스와 연결 합니다.

    패턴
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    예제:
    ```powershell
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
    > Application Insights 리소스에 대 한 연결 된 저장소 계정에 대 한 업데이트를 수행 하려면 [APPLICATION INSIGHTS CLI 설명서](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)를 참조 하세요.

#### <a name="configure-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 구성

1. 다음 콘텐츠 (byos.template.js)를 사용 하 여 Azure Resource Manager 템플릿 파일을 만듭니다.
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

1. 다음 PowerShell 명령을 실행 하 여 이전 템플릿 (연결 된 저장소 계정 만들기)을 배포 합니다.

    패턴
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    예제:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. PowerShell 콘솔에서 메시지가 표시 되 면 다음 매개 변수를 제공 합니다.
    
    |           매개 변수           |                                설명                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | BYOS를 사용 하도록 설정 하는 Application Insights 리소스의 이름입니다.            |
    | storage_account_name          | BYOS로 사용할 저장소 계정 리소스의 이름입니다. |
    
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

1. Azure Portal를 통해 관심 있는 작업에서 코드 수준 진단 (프로파일러/디버거)을 사용 하도록 설정 합니다. (App Service > Application Insights) _ ![ 그림 2.0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _그림 2.0_

## <a name="troubleshooting"></a>문제 해결
### <a name="template-schema-schema_uri-isnt-supported"></a>템플릿 스키마 ' {schema_uri} '은 (는) 지원 되지 않습니다.
* `$schema`템플릿의 속성이 유효한 지 확인 합니다. 다음 패턴을 따라야 합니다.`https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* `schema_version`템플릿의이 유효한 값 내에 있는지 확인 `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` 합니다.
    오류 메시지:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>위치 ' {location} '에 대해 등록 된 리소스 공급자를 찾을 수 없습니다.
* `apiVersion`리소스의이 인지 확인 `microsoft.insights/components` `2015-05-01` 합니다.
* `apiVersion`리소스의이 인지 확인 `linkedStorageAccount` `2020-03-01-preview` 합니다.
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
### <a name="storage-account-location-should-match-ai-component-location"></a>저장소 계정 위치는 AI 구성 요소 위치와 일치 해야 합니다.
* Application Insights 리소스의 위치가 저장소 계정과 동일한 지 확인 합니다.
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

일반적인 프로파일러 문제 해결에 대해서는 [Profiler 문제 해결 설명서](profiler-troubleshooting.md)를 참조 하세요.

일반적인 스냅숏 디버거 문제 해결에 대해서는 [스냅숏 디버거 문제 해결 설명서](snapshot-debugger-troubleshoot.md)를 참조 하세요. 

## <a name="faqs"></a>FAQ
* 프로파일러 또는 스냅숏을 사용 하도록 설정 하 고 BYOS를 사용 하도록 설정한 경우 내 데이터는 내 저장소 계정으로 마이그레이션 되나요?
    _아니요, 그렇지 않습니다._

* BYOS는 미사용 및 고객이 관리 하는 키에서 암호화를 사용 하나요?
    _예, 정확 하 게 하려면 BYOS가 고객 관리자 키로 프로파일러/디버거를 사용 하도록 설정 해야 합니다._

* BYOS는 인터넷에서 격리 된 환경에서 작동 하나요?
    _예로. 실제로 BYOS는 격리 된 네트워크 시나리오에 대 한 요구 사항입니다._

* 고객 관리 키와 개인 링크를 모두 사용 하도록 설정한 경우 BYOS가 작동 하나요? 
    _예, 가능 합니다._

* BYOS를 사용 하도록 설정한 경우 진단 서비스 저장소 계정을 사용 하 여 수집 된 데이터를 저장할 수 있나요? 
    _예, 이제 BYOS에서 데이터 마이그레이션을 지원 하지 않습니다._

* BYOS를 사용 하도록 설정한 후에는 저장소 및 네트워킹에 대 한 모든 관련 비용을 고려 합니다. 
    _예_
