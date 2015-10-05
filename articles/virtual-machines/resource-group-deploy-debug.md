<properties
   pageTitle="리소스 그룹 배포 문제 해결 | Microsoft Azure"
   description="리소스 관리자 배포 모델을 사용하여 만든 리소스를 배포하는 일반적인 문제를 설명하고 이러한 문제를 감지하고 해결하는 방법을 보여줍니다."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="09/18/2015"
   ms.author="rasquill"/>

# Azure에서 리소스 그룹 배포 문제 해결

배포하는 동안 문제가 발생하면 무엇이 잘못되었는지 검색해야 합니다. 리소스 관리자에서는 두 가지 방법으로 무슨 일이 발생했는지와 발생 이유를 확인할 수 있습니다. 리소스 그룹에 대한 특정 배포에 대한 정보를 검색하는 배포 명령을 사용할 수 있습니다. 또는 감사 로그를 사용하여 리소스 그룹에서 수행된 모든 작업에 대한 정보를 검색할 수 있습니다. 이 정보를 사용하여 문제를 해결하고 솔루션에서 작업을 다시 시작할 수 있습니다.

이 항목은 주로 배포 명령을 사용하여 배포 문제를 해결하는 데 중점을 둡니다. 감사 로그를 사용하여 리소스에서 모든 작업을 추적하기 위한 정보는 [리소스 관리자로 작업 감사](../resource-group-audit.md)를 참조하세요.

이 항목에서는 Azure PowerShell, Azure CLI 및 REST API를 통해 문제 해결 정보를 검색하는 방법을 보여줍니다. 배포 문제를 해결하기 위한 미리 보기 포털 사용에 대한 내용은 [Azure Preview 포털을 사용하여 Azure 리소스 관리](../azure-portal/resource-group-portal.md)를 참조하세요.

또한 사용자에게 발생할 수 있는 일반적인 오류에 대한 솔루션을 이 항목에서 설명합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 리소스 관리자 배포 모델을 사용하여 만든 리소스 그룹 문제를 해결하는 방법을 설명합니다. 클래식 배포 모델을 사용하여 리소스 그룹을 만들 수 없습니다.


## PowerShell 문제 해결

**Get AzureResourceGroupDeployment** 명령을 사용하여 배포의 전반적인 상태를 가져올 수 있습니다. 아래 예제에서는 배포에 실패했습니다.

    PS C:\> Get-AzureResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

    DeploymentName    : ExampleDeployment
    ResourceGroupName : ExampleGroup
    ProvisioningState : Failed
    Timestamp         : 8/27/2015 8:03:34 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    siteName         String                     ExampleSite
                    hostingPlanName  String                     ExamplePlan
                    siteLocation     String                     West US
                    sku              String                     Free
                    workerSize       String                     0

    Outputs           :

각 배포는 일반적으로 배포 프로세스의 단계를 나타내는 각 작업을 사용하여 여러 작업으로 구성됩니다. 배포에서 무엇이 잘못 되었는지 검색하려면 일반적으로 배포 작업에 대한 세부 정보를 확인해야 합니다. **Get AzureResourceGroupDeploymentOperation**를 사용하여 작업의 상태를 확인할 수 있습니다.

    PS C:\> Get-AzureResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

배포에 있는 두 작업을 보여줍니다. 하나는 실패한 프로비전 상태이고 다른 하나는 성공한 프로비전 상태입니다.

다음 명령을 사용하여 상태 메시지를 검색할 수 있습니다.

    PS C:\> (Get-AzureResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Azure CLI 문제 해결

**Azure 그룹 배포 표시** 명령을 사용하여 배포의 전반적인 상태를 가져올 수 있습니다. 아래 예제에서는 배포에 실패했습니다.

    azure group deployment show ExampleGroup ExampleDeployment

    info:    Executing command group deployment show
    + Getting deployments
    data:    DeploymentName     : ExampleDeployment
    data:    ResourceGroupName  : ExampleGroup
    data:    ProvisioningState  : Failed
    data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
    data:    Mode               : Incremental
    data:    Name             Type    Value
    data:    ---------------  ------  ------------
    data:    siteName         String  ExampleSite
    data:    hostingPlanName  String  ExamplePlan
    data:    siteLocation     String  West US
    data:    sku              String  Free
    data:    workerSize       String  0
    info:    group deployment show command OK


감사 로그에서 배포에 실패한 이유에 대해 자세한 내용을 찾을 수 있습니다. 감사 로그를 확인하려면 **Azure 그룹 로그 표시** 명령을 실행합니다. **--last-deployment** 옵션을 포함하여 가장 최근의 배포에 대한 로그를 검색할 수 있습니다.

    azure group log show ExampleGroup --last-deployment

**Azure 그룹 로그 표시** 명령은 많은 정보를 반환할 수 있습니다. 문제 해결을 위해 일반적으로 실패한 작업에 초점을 둡니다. 다음의 스크립트를 사용하여 **--json** 옵션 및 **jq**의 배포 오류에 대한 로그를 검색 합니다. **jq**와 같은 도구에 대해 자세히 알아 보려면 [Azure와 상호작용하는 유용한 도구](#useful-tools-to-interact-with-azure)를 참조하세요.

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
        "appidacr": "0",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "ipaddr": "000.000.000.000"
      },
      "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name 
          mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
          "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
      },
    ...

실패한 작업에 대한 json의 정보를 포함하는 **속성**을 확인할 수 있습니다.

**-verbose** 및 **-vv** 옵션을 사용하여 로그에서 자세한 정보를 볼 수 있습니다. **-verbose** 옵션을 사용하여 작업이 `stdout`를 통과하는 단계를 표시합니다. 완전한 요청 기록의 경우 **-vv** 옵션을 사용합니다. 메시지에 오류의 원인에 대한 중요한 단서가 제공되는 경우도 있습니다.

## REST API 문제 해결

리소스 관리자 REST API는 배포에 대한 정보를 검색하는 URI, 배포에 대한 세부 정보, 특정 작업에 대한 세부 정보를 제공합니다. 이러한 명령에 대한 전체 설명은 다음을 참조하세요.

- [템플릿 배포에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [모든 템플릿 배포 작업 나열](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [템플릿 배포 작업에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## 만료된 자격 증명 새로 고침

Azure 자격 증명이 만료되거나 Azure 계정에 로그인하지 않은 경우 배포가 실패합니다. 세션이 너무 오랫동안 열려 있으면 자격 증명이 만료될 수 있습니다. 다음 옵션으로 자격 증명을 새로 고칠 수 있습니다.

- PowerShell의 경우 **Add-AzureAccount** cmdlet을 사용합니다. 게시 설정 파일의 자격 증명으로는 AzureResourceManager 모듈의 cmdlet을 지원할 수 없습니다.
- Azure CLI의 경우 **Azure 로그인**을 사용합니다. 인증 오류에 대한 도움말을 보려면 [Azure CLI를 올바르게 구성](../xplat-cli-connect.md)했는지 확인합니다.

## 템플릿 및 매개 변수 형식 검사

템플릿 또는 매개 변수 파일이 올바른 형식이 아닌 경우 배포가 실패합니다. 배포를 실행하기 전에 템플릿 및 매개 변수의 유효성을 테스트할 수 있습니다.

### PowerShell

PowerShell의 경우 **Test-AzureResourceGroupTemplate**을 사용합니다.

    PS C:\> Test-AzureResourceGroupTemplate -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### Azure CLI

Azure CLI의 경우 **Azure 그룹 템플릿의 유효성 검사<resource group>**를 사용합니다.

다음 예제에서는 템플릿 및 필수 매개 변수의 유효성을 검사하는 방법을 보여줍니다. Azure CLI는 필요한 매개 변수 값을 표시합니다.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

### REST API

REST API의 경우 [템플릿 배포의 유효성 검사](https://msdn.microsoft.com/library/azure/dn790547.aspx)를 참조하세요.

## 리소스를 지완하는 위치 확인하기

리소스에 위치를 지정하는 경우 리소스를 지원하는 위치 중 하나를 사용해야 합니다. 리소스에 대한 위치를 입력하기 전에 다음 명령 중 하나를 사용하여 해당 위치에서 리소스 형식을 지원하는지 확인하세요.

### PowerShell

PowerShell의 경우 **Get-AzureLocation** 명령을 사용하여 리소스 및 위치의 전체 목록을 확인할 수 있습니다.

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

다음을 사용하여 특정 형식의 리소스를 지정할 수 있습니다.

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

### Azure CLI

Azure CLI의 경우 **Azure 위치 목록**을 사용할 수 있습니다. 위치 목록이 길 수도 있고 많은 공급자가 있으므로, 아직 사용할 수 없는 위치를 사용하기 전에 도구를 사용하여 공급자와 위치를 검사할 수 있습니다. 다음 스크립트에서는 **jq**를 사용하여 Azure 가상 컴퓨터에 리소스 공급자를 사용할 수 있는 위치를 검색합니다.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### REST API
        
REST API의 경우 [리소스 공급자에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790534.aspx)를 참조하세요.

## 고유한 리소스 이름 만들기

일부 리소스, 가장 주목할 만한 저장소 계정, 데이터베이스 서버 및 웹사이트의 경우 Azure의 모든 고유한 리소스에 이름을 제공해야 합니다. 현재 이름이 고유한지 여부를 테스트할 방법이 없습니다. 다른 조직에서 사용될 가능성이 없는 명명 규칙을 사용하는 것이 좋습니다.

## 인증, 구독, 역할 및 할당량 문제

인증, 권한 부여, Azure Active Directory를 비롯하여 성공적인 배포를 방해하는 하나 이상의 문제가 존재할 수 있습니다. Azure 리소스 그룹을 관리하는 방법에 상관없이 계정에 로그인하는 데 사용하는 ID는 Azure Active Directory 개체이거나 서비스 사용자여야 합니다. 이를 회사 또는 학교 계정, 조직 ID라고도 합니다.

개발자 또는 관리자는 Azure Active Directory를 사용하여 ID와 해당 ID가 액세스할 수 있는 리소스를 자세히 제어할 수 있습니다. 배포가 실패할 경우 요청에 인증 또는 권한 부여 문제가 있는지 확인하고 배포 로그에서 리소스 그룹을 검사합니다. 일부 리소스에 대해서는 권한이 있지만, 다른 리소스에 대해서는 권한이 없을 수도 있습니다. Azure CLI를 사용하여 `azure ad` 명령으로 Azure Active Directory 테넌트 및 사용자를 검사할 수 있습니다. (Azure CLI 명령에 대한 전체 목록은 [Azure 리소스 관리자에서 Mac, Linux 및 Windows용 Azure CLI 사용](azure-cli-arm-commands.md)을 참조하세요.)

또한 배포가 리소스 그룹, 구독, 계정 및 기타 범위당 기본 할당량에 도달할 경우 문제가 발생할 수 있습니다. 올바르게 배포할 수 있는 리소스가 있는지 확인합니다. 전체 할당량 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

코어에 대한 고유의 구독 할당량을 검사하려면 Azure CLI의 `azure vm list-usage` 명령 및 Powershell의 **Get-AzureVMUsage** cmdlet을 사용합니다. 다음은 Azure CLI에 명령과 무료 평가판 계정에 대한 코어 할당량이 4개 임을 보여줍니다.

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

상기 구독을 미국 서부지역에서 4개 이상의 코어를 만드는 템플릿을 배포하려고 한다면, (포털에서나 혹은 배포 로그를 조사하여) 이와 같은 오류를 얻게 됩니다

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

이러한 경우 포털로 이동하여 사용자가 배포하고 싶은 지역의 할당량을 올려서 지원 문제를 해결합니다.

> [AZURE.NOTE]리소스 그룹에 대해서는 이것을 기억하세요. 할당량은 개별적인 지역을 위한 것이지, 전체 구독을 위한 것이 아닙니다. 사용자가 미국 서부에 30개의 코어를 배포해야 하면 미국 서부에 30개의 리소스 관리자 코어를 요청해야 합니다. 사용자가 액세스하는 임의적인 지역에 30개의 코어를 배포해야 하는 경우 모든 지역에 30개의 리소스 관리자 코어를 요청해야 합니다. <!-- -->예를 들어 특정한 코어를 만들려면 json 구문 분석을 위해 **jq**를 빼낸 아래의 명령을 이용하여 적정한 할당량을 요청해야 하는 지역을 확인할 수 있습니다. <!-- -->azure 공급자 표시 Microsoft.Compute--json | q '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' { "name": "virtualMachines", "apiVersions": [ "2015-05-01-preview", "2014-12-01-preview" ], "locations": [ "East US", "West US", "West Europe", "East Asia", "Southeast Asia" ] }


## 리소스 공급자 등록 확인

리소스 공급자가 리소스를 관리하고, 계정 또는 구독이 특정 공급자를 사용하도록 설정되었을 수 있습니다. 공급자를 사용하도록 설정한 경우 공급자가 사용하도록 등록되어 있어야 합니다. 대부분의 공급자는 Azure 포털이나 사용 중인 명령줄 인터페이스에 의해 자동으로 등록되지만, 그렇지 않은 경우도 있습니다.

### PowerShell

리소스 공급자 및 사용자 등록 상태 목록을 가져오려면 **AzureProvider 가져오기**를 사용합니다.

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

공급자를 등록하려면 **Register-AzureProvider**를 사용합니다.

### Azure CLI

Azure CLI를 사용하여 공급자가 사용하도록 등록되어 있는지 여부를 확인하려면 `azure provider list` 명령을 사용합니다. 다음 예에서는 출력이 잘려져 있습니다.

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

마찬가지로 국가별 가용성을 포함한 공급자에 대한 더 많은 정보를 원하면 `azure provider list --json`을 입력합니다. 다음은 살펴보기 위해 목록의 오직 첫 번째 것만을 추려낸 것입니다.

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }

공급자를 등록해야 하는 경우 `azure provider register <namespace>` 명령을 사용합니다. 여기서 *namespace* 값은 이전 목록에서 가져옵니다.

### REST API

등록 상태를 가져오려면 [리소스 공급자에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790534.aspx)를 참조하세요.

공급자를 등록하려면 [리소스 공급자로 구독 등록](https://msdn.microsoft.com/library/azure/dn790548.aspx)을 참조하세요.


## 사용자 지정 템플릿에 대해 배포가 성공하는 경우 이해

사용자가 만든 템플릿을 사용하는 경우 모든 공급자가 배포에서 성공적으로 반환할 때 Azure 리소스 관리자 시스템은 배포가 성공한 것으로 보고한다는 것을 이해하는 것이 중요합니다. 즉, 모든 템플릿 항목이 사용하도록 배포된 것을 의미합니다.

하지만 반드시 리소스 그룹이 "활성 상태이고 사용자를 위해 준비된" 것을 의미하는 것은 아닙니다. 예를 들어 대부분의 배포에서는 업그레이드를 다운로드하거나, 다른 템플릿이 아닌 리소스를 대기하거나, 공급자가 추적하는 활동이 아니므로 Azure에서 알지 못하는 다른 실행 가능한 활동이나 복잡한 스크립트를 설치해야 합니다. 이러한 경우 리소스를 실제로 사용하기 위해 준비하는 데 시간이 걸릴 수 있습니다. 따라서 배포를 사용할 수 있기 이전에 배포 상태가 성공한 것으로 간주해야 합니다.

하지만 전체 배포에서 시스템 전체 준비 상태를 모니터링하는 방법을 알려주고 사용자가 전체 배포를 조작할 수 있는 경우에만 성공적으로 반환되는 사용자 지정 템플릿에 대한 사용자 지정 스크립트를 작성하여(예: [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) 사용) Azure에서 배포 성공을 보고하지 못하도록 차단할 수 있습니다. 확장이 마지막에 실행되도록 하려면 템플릿에서 **dependsOn** 속성을 사용합니다. 예제는 [여기서](https://msdn.microsoft.com/library/azure/dn790564.aspx) 확인할 수 있습니다.

## Azure를 조작하는 데 유용한 도구
명령줄에서 Azure 리소스를 작업하는 경우 작업에 도움이 되도록 도구를 수집합니다. Azure 리소스 그룹 템플릿은 JSON 문서이고 Azure 리소스 관리자 API는 JSON을 수락하고 반환하므로, 리소스에 대한 정보를 탐색하거나 템플릿 및 템플릿 매개 변수 파일을 설계 또는 조작할 때 일반적으로 가장 먼저 사용하는 것은 JSON 구문 분석 도구입니다.

### Mac, Linux 및 Windows 도구
Mac, Linux 및 Windows용 Azure 명령줄 인터페이스를 사용하는 경우 표준 다운로드 도구(예: **[curl](http://curl.haxx.se/)**, **[wget](https://www.gnu.org/software/wget/)**, **[Resty](https://github.com/beders/Resty)**) 및 JSON 유틸리티(예: **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** 및 JSON을 처리하는 언어 라이브러리)에 대해 잘 알고 있을 것입니다. 이러한 도구는 대부분 Windows용 포트(예: [wget](http://gnuwin32.sourceforge.net/packages/wget.htm))가 있습니다. 실제로 Windows에서 실행 중인 Linux 및 기타 오픈 소스 소프트웨어 도구를 다양한 방법으로 가져올 수 있습니다.

이 항목에는 원하는 정보를 보다 정확하고 효율적으로 가져오기 위해 **jq**와 함께 사용할 수 있는 일부 Azure CLI 명령이 포함되어 있습니다. Azure 리소스 사용을 쉽게 파악할 수 있도록 잘 알고 있는 도구 집합을 선택해야 합니다.

### PowerShell

PowerShell에는 동일한 절차를 수행하는 몇 가지 기본 명령이 있습니다.

- **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** cmdlet을 사용하여 파일(예: 리소스 그룹 템플릿 또는 매개 변수 JSON 파일)을 다운로드합니다.
- **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** cmdlet을 사용하여 JSON 문자열을 JSON 문자열의 각 필드에 대한 속성이 있는 사용자 지정 개체([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx))로 변환합니다.


## 다음 단계

템플릿 만들기를 마스터하려면 [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)을 읽고 [Azure 빠른 시작 템플릿 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 배포 가능한 예제를 살펴보세요. **dependsOn** 속성의 예는 [액세스할 수 있는 여러 NIC 및 RDP를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics)입니다.

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!---HONumber=Sept15_HO4-->