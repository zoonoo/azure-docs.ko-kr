---
title: Azure 고객 사용 특성
description: 상업적 marketplace에서 Azure 응용 프로그램에 대 한 고객 사용을 추적 하는 개요와 파트너가 개발한 기타 배포 가능한 IP를 확인 하세요.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/09/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 8f84d77b5a424d5f7273c7e748c35c52882819c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608396"
---
# <a name="azure-customer-usage-attribution"></a>Azure 고객 사용 특성

고객 사용 특성은 파트너와 함께 IP를 배포 하는 동안 생성 된 고객 구독에서 Azure 리소스의 사용량을 연결 합니다. 내부 Microsoft 시스템에 이러한 연결을 구성하면 소프트웨어를 실행하는 Azure의 메모리 공간을 보다 잘 파악할 수 있습니다. [상업적 marketplace의 Azure 애플리케이션 제품](#commercial-marketplace-azure-apps)의 경우이 추적 기능을 사용 하면 microsoft 영업 팀에 맞추고 microsoft 파트너 프로그램에 대 한 크레딧을 얻을 수 있습니다.

고객 사용량 특성은 세 가지 배포 옵션을 지원합니다.

1. Azure Resource Manager 템플릿 (상용 marketplace에서 "솔루션 템플릿" 또는 "관리 되는 앱"이 라고도 하는 Azure 앱의 일반적인 기초가 되 파트너: 파트너는 Azure 솔루션의 인프라와 구성을 정의 하는 리소스 관리자 템플릿을 만듭니다. 리소스 관리자 템플릿을 통해 고객은 솔루션의 리소스를 일관 되 고 반복 가능한 상태로 배포할 수 있습니다.
1. Azure Resource Manager Api: 파트너는 리소스 관리자 Api를 호출 하 여 리소스 관리자 템플릿을 배포 하거나 Azure 서비스를 직접 프로 비전 할 수 있습니다.
1. Terraform: 파트너는 Terraform을 사용 하 여 리소스 관리자 템플릿을 배포 하거나 Azure 서비스를 직접 배포할 수 있습니다.

[이 문서의 뒷부분에](#other-use-cases)설명 된 상용 marketplace 외부에서 고객 사용 특성에 대 한 보조 사용 사례가 있습니다.

>[!IMPORTANT]
>- 고객 사용 특성은 주로 Azure 리소스를 배포 하 고 관리 하기 위해 설계 된 시스템 통합 업체, 관리 서비스 공급자 또는 도구의 작업을 추적 하기 위한 것이 아닙니다.
>- 고객 사용 특성은 새 배포에 사용 되며 이미 배포 된 리소스를 추적 하는 것을 지원 하지 않습니다.
>- 모든 Azure 서비스는 고객 사용 특성과 호환 되지 않습니다. AKS (Azure Kubernetes Services) 및 VM Scale Sets에는 사용 현황 보고를 야기 하는 알려진 문제가 있습니다.

## <a name="commercial-marketplace-azure-apps"></a>상업적 marketplace Azure 앱

상업적 marketplace에 게시 된 Azure 앱에서 Azure 사용량을 추적 하는 것은 대부분 자동입니다. [Marketplace Azure 앱 계획의 기술 구성](https://docs.microsoft.com/azure/marketplace/create-new-azure-apps-offer-solution#define-the-technical-configuration)의 일부로 리소스 관리자 템플릿을 업로드 하는 경우 파트너 센터는 Azure Resource Manager 여 읽을 수 있는 추적 ID를 추가 합니다.

Azure Resource Manager Api를 사용 하는 경우 코드에서 리소스를 배포 하는 것 처럼 Azure Resource Manager에 전달 하려면 [아래 지침](#use-resource-manager-apis) 에 따라 추적 ID를 추가 해야 합니다. 이 ID는 계획의 기술 구성 페이지에서 파트너 센터에 표시 됩니다. 

> [!NOTE]
> 기존 Azure 앱의 경우 각 계획의 기술 구성에서 추적 Id를 업데이트 하기 위해 3 월 2021 일에 일회성 마이그레이션이 수행 되었습니다. 이러한 제품의 이전 배포에 대 한 사용량은 Microsoft 시스템에서 계속 추적 됩니다.

## <a name="other-use-cases"></a>기타 사용 사례 

고객 사용 특성을 사용 하 여 상업적 marketplace에서 사용할 수 없는 솔루션의 Azure 사용 현황을 추적할 수 있습니다. 이러한 솔루션은 일반적으로 빠른 시작 리포지토리, 개인 GitHub 리포지토리에 위치 하거나 내구성이 있는 IP (예: 배포 가능 하 고 확장 가능한 앱)를 만드는 1:1 고객 계약에서 제공 됩니다.

몇 가지 수동 단계가 필요 합니다.

1. 추적 Id로 사용할 Guid를 하나 이상 만듭니다.
1. 파트너 센터에 해당 Guid를 등록 합니다.
1. 등록 된 Guid를 Azure 앱 및/또는 사용자 에이전트 문자열에 추가 합니다.

### <a name="create-guids"></a>GUID 만들기

파트너 센터가 상업적 marketplace에서 Azure 앱을 대신 하 여 만드는 추적 Id와 달리, 다른 사용자는 추적 ID로 사용할 GUID를 만들어야 합니다. GUID는 32자리의 16진수가 있는 고유 참조 식별자입니다. 추적할 Guid를 만들려면 PowerShell을 통해 GUID 생성기를 사용 해야 합니다.

```powershell
[guid]::NewGuid()
```

각 제품 및 배포 채널에 대해 고유한 GUID를 만들어야 합니다. 보고를 분할 하지 않으려면 제품의 여러 배포 채널에 단일 GUID를 사용할 수 있습니다. 보고는 Microsoft 파트너 네트워크 ID 및 GUID에 의해 발생 합니다.

### <a name="register-guids"></a>GUID 등록

다음 Guid는 파트너 센터에 등록 해야 파트너와 연결할 수 있습니다.

1. [파트너 센터](https://partner.microsoft.com/dashboard)에 로그인합니다.

1. [상업용 마켓플레이스 게시자](https://aka.ms/JoinMarketplace)로 등록합니다.

1. 오른쪽 위 모서리에서 **설정** (기어 아이콘)을 선택 하 고 **계정 설정** 을 선택 합니다.

1. **조직 프로필**  >  **식별자**  >  **추가 추적 GUID** 를 선택 합니다.

1. **GUID** 상자에 추적 GUID를 입력합니다. 접두사 없이 GUID만 입력 합니다 `pid-` . **설명** 상자에 솔루션 이름 또는 설명을 입력 합니다.

1. 여러 GUID를 등록하려면 **추적 GUID 추가** 를 다시 선택합니다. 추가 상자가 페이지에 표시됩니다.

1. **저장** 을 선택합니다.

### <a name="add-a-guid-to-a-resource-manager-template"></a>리소스 관리자 템플릿에 GUID 추가

리소스 관리자 템플릿에 등록 된 GUID를 추가 하려면 기본 템플릿 파일을 단일 수정 합니다.

1. Resource Manager 템플릿을 엽니다.

1. 주 템플릿 파일에 [Microsoft .resources/배포](/azure/templates/microsoft.resources/deployments) 유형의 새 리소스를 추가 합니다. 리소스는 중첩 또는 연결 된 템플릿이 아닌 **mainTemplate.json** 또는 **azuredeploy.js** 파일에만 있어야 합니다.

1. 접두사 뒤에 GUID 값을 입력 하 여 `pid-` 리소스의 이름으로 입력 합니다. 예를 들어 GUID가 eb7927c8-dd66-43e1-b0cf-c346a422063 인 경우 리소스 이름은 **pid-eb7927c8-dd66-43e1-b0cf-c346a422063** 가 됩니다. 예제:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. 템플릿에 오류가 있는지 확인 합니다.

1. 적절한 리포지토리에 템플릿을 다시 게시합니다.

1. [템플릿 배포에서 GUID 성공을 확인](#verify-deployments-tracked-with-a-guid)합니다.

> [!TIP]
> 리소스 관리자 템플릿 만들기 및 게시에 대 한 자세한 내용은 [첫 번째 리소스 관리자 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조 하세요.

### <a name="verify-deployments-tracked-with-a-guid"></a>GUID로 추적 되는 배포 확인

템플릿을 수정 하 고 테스트 배포를 실행 한 후 다음 PowerShell 스크립트를 사용 하 여 배포 하 고 태그를 지정 하는 리소스를 검색 합니다.

이 스크립트를 사용하여 GUID가 Resource Manager 템플릿에 성공적으로 추가되었는지 확인할 수 있습니다. Resource Manager API 또는 Terraform 배포에는 이 스크립트가 적용되지 않습니다.

Azure에 로그인합니다. 스크립트를 실행 하기 전에 확인 하려는 배포에 대 한 구독을 선택 합니다. 배포의 구독 컨텍스트 내에서 스크립트를 실행합니다.

"DeploymentName" 이라는 **GUID** 와 배포의 **resourceGroupName** 이름이 필수 매개 변수입니다.

GitHub에서 [원래의 스크립트](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1)를 가져올 수 있습니다.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>고객에게 알림

파트너는 고객 사용량 특성을 사용하는 배포에 대해 고객에게 알려야 합니다. Microsoft는 파트너에 대 한 이러한 배포와 연관 된 Azure 사용량을 보고 합니다. 다음 예제에는 이러한 배포에 대해 고객에게 알리는 데 사용할 수 있는 콘텐츠가 포함되어 있습니다. 예제에서을 \<PARTNER> 회사 이름으로 바꿉니다. 파트너는 추적에서 제외할 고객에 대 한 옵션을 포함 하 여 알림이 데이터 개인 정보 및 수집 정책과 일치 하는지 확인 해야 합니다.

#### <a name="notification-for-resource-manager-template-deployments"></a>Resource Manager 템플릿 배포에 대한 알림

이 템플릿을 배포할 때 Microsoft는 \<PARTNER> 배포 된 Azure 리소스를 사용 하 여 소프트웨어 설치를 식별할 수 있습니다. Microsoft는 소프트웨어를 지 원하는 데 사용 되는 이러한 리소스의 상관 관계를 지정할 수 있습니다. Microsoft는 제품에 최상의 환경을 제공하고 비즈니스를 운영하기 위해 이 정보를 수집합니다. 데이터는에 있는 Microsoft의 개인 정보 취급 방침에 의해 수집 되 고 관리 됩니다 [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>SDK 또는 API 배포 알림

소프트웨어를 배포 하는 경우 \<PARTNER> Microsoft는 \<PARTNER> 배포 된 Azure 리소스를 사용 하 여 소프트웨어 설치를 식별할 수 있습니다. Microsoft는 소프트웨어를 지 원하는 데 사용 되는 이러한 리소스의 상관 관계를 지정할 수 있습니다. Microsoft는 제품에 최상의 환경을 제공하고 비즈니스를 운영하기 위해 이 정보를 수집합니다. 데이터는에 있는 Microsoft의 개인 정보 취급 방침에 의해 수집 되 고 관리 됩니다 [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>리소스 관리자 Api 사용

경우에 따라 리소스 관리자 REST Api에 대해 직접 호출 하 여 Azure 서비스를 배포할 수 있습니다. Azure는 이러한 호출을 사용하도록 설정하는 [여러 SDK를 지원](../index.yml?pivot=sdkstools)합니다. Sdk 중 하나를 사용 하거나 REST Api를 직접 호출 하 여 리소스를 배포할 수 있습니다.

고객 사용 특성을 사용 하도록 설정 하려면 API 호출을 디자인할 때 요청의 사용자 에이전트 헤더에 추적 ID를 포함 합니다. 접두사를 사용 하 여 문자열의 형식을 지정 합니다 `pid-` . 예제:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> 상용 marketplace에서 Azure 앱과 함께 리소스 관리자 Api를 사용 하는 경우 파트너 센터에 제공 된 추적 ID를 사용 합니다. GUID를 사용 하지 마세요.

다양 한 Sdk가 리소스 관리자 Api와 다르게 상호 작용 하 고 코드에서 약간의 차이점이 필요 합니다. 아래 예제에서는 GUID를 사용 하는 비상업용 marketplace 방법을 사용 하 고 다양 한 인기 Azure Sdk를 소개 합니다.

#### <a name="example-python-sdk"></a>예: Python SDK

Python의 경우 **config** 특성을 사용합니다. 이 특성은 UserAgent에만 추가할 수 있습니다. 예제:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> 각 클라이언트에 대한 특성을 추가합니다. 글로벌 정적 구성이 없습니다. 모든 클라이언트가 추적 하도록 클라이언트 팩터리에 태그를 추가할 수 있습니다. 자세한 내용은 [GitHub의 클라이언트 팩터리 샘플](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)을 참조하세요.

#### <a name="example-net-sdk"></a>예: .NET SDK

.NET의 경우 사용자 에이전트를 설정 해야 합니다. [흐름](/dotnet/api/microsoft.azure.management.fluent) 라이브러리를 사용 하 여 다음 코드를 사용 하 여 사용자 에이전트를 설정 합니다 (c #의 경우).

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>예: Azure PowerShell

Azure PowerShell를 통해 리소스를 배포 하는 경우이 방법을 사용 하 여 GUID를 추가 합니다.

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>예: Azure CLI

Azure CLI를 사용 하 여 GUID를 추가 하는 경우 스크립트의 범위 내에서 **AZURE_HTTP_USER_AGENT** 환경 변수를 설정 합니다. 셸 범위에 대한 글로벌 변수를 설정할 수도 있습니다.

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

자세한 내용은 [Go용 Azure SDK](/azure/developer/go/)를 참조하세요.

## <a name="use-terraform"></a>Terraform 사용

Terraform에 대 한 지원은 Azure 공급자의 1.21.0 릴리스를 통해 제공 됩니다 [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . 이는 Terraform을 통해 솔루션을 배포 하는 모든 파트너와 Azure 공급자 (버전 1.21.0 이상)에서 배포 및 계량 되는 모든 리소스에 적용 됩니다.

Terraform 용 Azure 공급자는 솔루션에 사용 되는 추적 GUID를 지정 하는 [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) 라는 새로운 선택적 필드를 추가 했습니다. 이 필드의 값은 *ARM_PARTNER_ID* 환경 변수에서 소싱될 수도 있습니다.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
*Partner_id* 의 값을 등록 된 GUID로 설정 합니다. GUID에 "pid-"를 접두사로 사용 하지 말고 실제 GUID로 설정 합니다.

> [!IMPORTANT]
> 상용 marketplace에서 Azure 앱과 함께 Terraform을 사용 하는 경우 파트너 센터에 제공 된 전체 추적 ID를 사용 합니다. GUID를 사용 하지 마세요.

## <a name="get-support"></a>지원 받기

[파트너 센터에서 상업적 marketplace 프로그램 지원](support.md)의 지원 옵션에 대해 알아봅니다.

### <a name="how-to-submit-a-technical-consultation-request"></a>기술 상담 요청을 제출하는 방법

1. [파트너 기술 서비스](https://aka.ms/TechnicalJourney)을 방문하세요.
1. 기술 경험을 보려면 **클라우드 인프라 및 관리** 를 선택 합니다.
1. **배포 서비스**  >  **요청 제출을** 선택 합니다.
1. MSA (MPN 계정) 또는 AAD (파트너 대시보드 계정)를 사용 하 여 로그인 합니다.
1. 열리는 양식에서 연락처 정보를 완료 하거나 검토 합니다. 컨설팅 세부 정보는 미리 채우거 나 드롭다운 옵션이 있을 수 있습니다.
1. 제목 및 문제에 대 한 자세한 설명을 입력 합니다.
1. **제출** 을 선택합니다.

[기술 예약 판매 및 배포 서비스 사용](https://aka.ms/TechConsultInstructions)에서 스크린샷과 함께 단계별 지침을 확인하세요.

Microsoft 파트너 기술 컨설턴트로부터 요구 사항의 범위를 확인하는 전화 약속을 잡기 위한 연락을 받게 됩니다.

## <a name="report"></a>보고서
현재 ISV 파트너의 경우 고객 사용 특성을 통해 추적 되는 Azure 사용량에 대 한 보고를 사용할 수 없습니다. 고객 사용 특성을 다루는 상용 Marketplace 프로그램에 보고 기능을 추가 하는 것은 2021의 두 번째 절반을 대상으로 합니다.

## <a name="faq"></a>FAQ

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>추적 ID를 추가한 후에는 변경할 수 있나요?

상업적 marketplace의 Azure 앱에 대 한 추적 Id는 파트너 센터에 의해 자동으로 관리 됩니다. 그러나 고객은 템플릿을 다운로드 하 고 추적 ID를 변경 하거나 제거할 수 있습니다. 파트너는 제거 또는 편집을 방지 하기 위해 추적 ID의 역할을 고객에 게 사전에 설명 해야 합니다. 추적 ID를 변경 하면 새 배포 및 리소스에만 영향을 주며 기존 배포에는 영향을 주지 않습니다.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>GitHub 등의 타사 리포지토리에서 배포된 템플릿을 추적할 수 있나요?

예, 템플릿이 배포 될 때 추적 ID가 있는 한 사용이 추적 됩니다. Microsoft 이외의 리포지토리에서 배포 된 게시자와 사용자 간의 연결을 유지 하려면 먼저 Azure Portal의 상용 marketplace 목록에서 게시 된 템플릿 (추적 ID 포함)의 복사본을 다운로드 합니다. 해당 버전을 GitHub 또는 다른 타사 리포지토리에 게시 합니다.

템플릿이 상용 marketplace에 나열 되어 있지 않고 등록 된 GUID를 포함 하는 경우 GitHub 또는 다른 타사 리포지토리에 게시 하는 버전에 GUID가 있는지 확인 합니다.

#### <a name="does-the-customer-receive-reporting-as-well"></a>고객도 보고를 받나요?

아니요. 고객은 Azure Portal 내에서 모든 리소스 또는 리소스 그룹의 용도를 추적할 수 있습니다. 고객은 추적 ID를 사용 하 여 사용이 중단 되는 것을 볼 수 없습니다.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>고객 사용 특성이 사용자의 디지털 파트너 레코드 (DPOR) 또는 파트너 관리자 링크 (PAL)와 유사 합니까?

고객 사용 특성은 Azure 사용량을 파트너의 반복 가능 하 고 배포 가능한 IP와 연결 하 여 배포 시 연결을 형성 하는 메커니즘입니다. DPOR 및 PAL은 고객이 참여 하는 시간 동안 컨설팅 (시스템 통합자) 또는 관리 (관리 서비스 공급자) 파트너를 고객의 관련 Azure 공간과 연결 하는 데 사용 됩니다.