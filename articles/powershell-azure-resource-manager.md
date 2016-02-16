<properties 
	pageTitle="리소스 관리자 포함 Azure PowerShell | Microsoft Azure" 
	description="Azure PowerShell을 사용하여 여러 리소스를 Azure에 리소스로 배포하는 작업을 소개합니다." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="01/08/2016" 
	ms.author="tomfitz"/>

# Azure 리소스 관리자로 Azure PowerShell 사용

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Azure 리소스 관리자는 Azure 리소스에 대해 완전히 새로운 방식으로 접근합니다. 개별 리소스를 만들어서 관리하는 대신 블로그, 사진 갤러리, SharePoint 포털, wiki 등의 전체 솔루션을 생각해 보십시오. 템플릿(솔루션의 선언적 표현)을 사용하여 해당 솔루션을 지원하는 데 필요한 모든 리소스가 포함된 리소스 그룹을 만듭니다. 그런 다음 해당 리소스 그룹을 논리 단위로 관리 및 배포합니다.

이 자습서에서는 Azure 리소스 관리자에서 Azure PowerShell을 사용하는 방법에 대해 알아봅니다. 또한 이 자습서에서는 SQL 데이터베이스와 지원하는 데 필요한 모든 리소스를 사용하여 Azure에 호스트된 웹 앱에 대한 리소스 그룹을 만들어서 배포하는 과정을 안내합니다.

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Azure 계정.
  + [Azure 계정을 무료로 개설](/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있음: 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스)를 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.
  
  + [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있음: MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.
- Azure PowerShell 1.0 이 릴리즈에 대한 정보 및 설치하는 방법은 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)을 참조하세요.

이 자습서는 PowerShell 초보자용으로 설계되었지만, 모듈, cmdlet, 세션 등과 같은 기본 개념을 잘 알고 있다고 가정합니다. Windows PowerShell에 대한 자세한 내용은 [Windows PowerShell 시작](http://technet.microsoft.com/library/hh857337.aspx)(영문)을 참조하십시오.

## 배포할 내용

이 자습서에서는 Azure PowerShell을 사용하여 웹 앱 및 SQL 데이터베이스를 배포합니다. 이 웹 앱 및 SQL 데이터베이스 솔루션은 함께 작동하는 여러 리소스 유형으로 이루어집니다. 배포할 실제 리소스는 다음과 같습니다.

- SQL 서버 - 데이터베이스 호스팅
- SQL 데이터베이스 - 데이터 저장
- 방화벽 규칙 - 웹 앱을 데이터베이스에 연결하도록 허용
- 앱 서비스 계획 - 웹 앱의 기능과 비용 정의
- 웹 사이트, 웹 앱 실행
- 웹 구성 - 연결 문자열을 데이터베이스에 저장 

## Cmdlet에 대한 도움말 보기

이 설명서에 나오는 cmdlet에 대한 자세한 도움말을 보려면 Get-Help cmdlet을 사용합니다.

	Get-Help <cmdlet-name> -Detailed

예를 들어 AzureRmResource Cmdlet에 대한 도움말을 보려면 다음과 같이 입력합니다.

	Get-Help Get-AzureRmResource -Detailed

리소스 모듈의 Cmdlet 목록을 도움말 개요와 함께 가져오려면 다음을 입력합니다.

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

출력은 다음 발췌와 유사합니다.

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

cmdlet에 대한 전체 도움말을 가져오려면 다음 형식으로 명령을 입력합니다.

	Get-Help <cmdlet-name> -Full
  
## Azure 계정에 로그인합니다.

솔루션에서 작업을 하기 전에 본인의 계정에 로그인해야 합니다.

Azure 계정에 로그인하려면 **Login-AzureRmAccount** Cmdlet을 사용합니다.

    PS C:\> Login-AzureRmAccount

Cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

계정 설정은 만료되므로 자주 새로 고쳐야 합니다. 계정 설정을 새로 고치려면 **Login-AzureRmAccount**를 다시 실행합니다.

>[AZURE.NOTE] 리소스 관리자 모듈을 사용하려면 Login-AzureRmAccount가 필요합니다. 게시 설정 파일로는 충분하지 않습니다.

## 리소스 유형 위치 가져오기

리소스를 배포하는 경우 리소스를 호스팅할 위치를 지정해야 합니다. 어떤 지역에서는 일부 리소스 유형을 지원하지 않을 수 있습니다. 웹 앱 및 SQL 데이터베이스를 배포 하기 전에 해당 유형을 지원하는 지역을 파악해야 합니다. 리소스 그룹에는 서로 다른 지역에 있는 리소스가 포함될 수 있지만, 성능을 최적화하기 위해 가능하면 언제나 동일한 위치에 리소스를 만들어야 합니다. 특히, 앱이 액세스할 때 데이터베이스가 동일한 위치에 있는지 확인합니다.

각 리소스 유형을 지원하는 위치를 가져오려면 **Get-AzureRmResourceProvider** Cmdlet을 사용해야 합니다. 첫째,이 명령에서 반환하는 것이 무엇인지 살펴보겠습니다.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

ProviderNamespace는 관련 리소스 유형의 컬렉션을 표시합니다. 이러한 네임스페이스는 일반적으로 Azure에서 만들려고 하는 서비스와 일치합니다. **Unregistered**으로 나열된 리소스 공급자를 사용하려는 경우 **Register-AzureRmResourceProvider** Cmdlet을 실행하고 등록할 공급자 네임스페이스를 지정하여 해당 리소스 공급자를 등록할 수 있습니다. 대부분의 경우 이 자습서에서 사용할 리소스 공급자는 사용자의 구독에 대해 이미 등록되어 있을 것입니다.

해당 네임스페이스를 지정하여 공급자에 관한 자세한 정보를 얻을 수 있습니다.

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

출력을 특정 유형의 리소스에 대해 지원되는 위치(웹 사이트 등)로 제한하려면 다음을 사용합니다.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
다음과 유사하게 출력됩니다.

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

표시되는 위치는 앞의 결과와 조금 다를 수 있습니다. 결과가 다를 수 있는 이유는 조직의 관리자가 사용자의 구독에 사용할 수 있는 지역을 제한하는 정책을 만들었거나 사용자의 모국의 세금 정책과 관련된 제한이 있을 수 있기 때문입니다.

데이터베이스에 대해 동일한 명령을 실행해 보겠습니다.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

이러한 리소스는 많은 지역에서 사용할 수 있는 것 같습니다. 이 항목에서는 **서부 미국**을 사용하지만 지원되는 어떤 지역이든 지정할 수 있습니다.

## 리소스 그룹 만들기

자습서의 이 섹션에서는 리소스 그룹을 만드는 과정을 안내합니다. 리소스 그룹은 같은 수명 주기를 공유하는 솔루션의 모든 리소스에 대한 컨테이너 역할을 합니다. 자습서의 뒷부분에서는 이 리소스 그룹에 웹 앱 및 SQL 데이터베이스를 배포합니다.

리소스 그룹을 만들려면 **New-AzureRmResourceGroup** Cmdlet을 사용합니다.

이 명령은 **Name** 매개 변수를 사용하여 리소스 그룹에 대한 이름을 지정하고 **Location** 매개 변수를 사용하여 위치를 지정합니다. 이전 섹션에서 발견한 내용을 바탕으로 “서부 미국"을 위치로 사용합니다.

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

리소스 그룹은 성공적으로 만들어졌습니다.


## 리소스에 사용할 수 있는 API 버전 가져오기

서식 파일을 배포할 때 리소스를 만들기 위해 사용하는 API 버전을 지정해야 합니다. 사용할 수 있는 API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 REST API의 새 버전을 릴리스합니다. 따라서 템플릿에 지정하는 API의 버전은 템플릿을 만들 때 사용할 수 있는 속성에 영향을 줍니다. 일반적으로 새 템플릿을 만들 때 가장 최근의 API 버전을 선택하려고 합니다. 기존 템플릿의 경우 배포를 변경하지 않을 것으로 알고 있는 API 버전을 계속 사용할지 여부 또는 새 기능을 이용하도록 최신 버전에 맞게 템플릿을 업데이트할지 여부를 결정할 수 있습니다.

이 단계는 혼동스러워 보이지만 리소스에 사용할 수 있는 API 버전을 검색하는 것은 어렵지 않습니다. **Get-AzureRmResourceProvider** 명령을 다시 사용합니다.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

보다시피 이 API는 자주 업데이트되었습니다. 일반적으로 리소스 공급자의 모든 리소스에 대해 동일한 API 버전 번호를 사용할 수 있습니다. 유일한 예외는 어떤 지점에서 리소스가 추가 또는 제거되었는지 여부입니다. 여기서는 serverFarms 리소스에 동일한 API 버전을 사용할 수 있다고 가정하지만, 사용할 수 있는 API 버전의 서로 다른 목록이 있을 수 있다고 생각하는 리소스에 대해서는 다시 한 번 점검할 수 있습니다.

데이터베이스에 대해 다음 사항이 표시됩니다.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## 템플릿 이미지 만들기

이 항목에서는 템플릿을 만드는 방법을 보여 주거나 템플릿의 구조를 설명하지 않습니다. 이러한 정보는 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요. 배포할 템플릿은 다음과 같습니다. 템플릿이 이전 섹션에서 검색한 API 버전을 사용하는 것을 확인할 수 있습니다. 모든 리소스가 같은 지역에 상주하도록 템플릿 식 **resourceGroup().location**을 사용하여 리소스 그룹의 위치를 사용합니다.

매개 변수에 대한 섹션도 참고하세요. 이 섹션에서는 리소스를 배포할 때 제공할 수 있는 값을 정의합니다. 이 값을 이 자습서의 뒷부분에서 사용합니다.

템플릿을 복사하고 로컬 컴퓨터에 .json 파일로 저장할 수 있습니다. 이 자습서에서는 c:\\Azure\\Templates\\azuredeploy.json에 저장되었다고 가정하지만 편리한 위치에 요구사항에 맞는 이름으로 저장할 수 있습니다.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "tags": {
                    "team": "webdev"
                },
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## 템플릿 배포

리소스 그룹이 있고 템플릿이 있으므로 이제 템플릿에 정의된 인프라를 리소스 그룹에 배포할 준비가 되었습니다. **New-AzureRmResourceGroupDeployment** Cmdlet을 사용하여 리소스를 배포합니다. 기본 구문은 다음과 같습니다.

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

리소스 그룹 및 템플릿 위치를 지정합니다. 템플릿이 로컬이 아닌 경우 **-TemplateUri** 매개 변수를 사용하고 템플릿에 대한 URI를 지정할 수 있습니다. **-Mode** 매개 변수를 **Incremental** 또는 **Complete**로 설정할 수 있습니다. 기본적으로 리소스 관리자는 배포하는 동안 증분 업데이트를 수행하므로 **Incremental**을 원하는 경우 **-Mode**를 설정하지 않아도 됩니다. 이러한 배포 모드 간의 차이점을 이해하려면 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

###동적 템플릿 매개 변수

PowerShell에 익숙한 경우 빼기 기호(-)를 입력하고 TAB 키를 눌러 Cmdlet에 사용할 수 있는 매개 변수를 순환시켜 볼 수 있습니다. 이 기능은 템플릿에 정의하는 매개 변수에 대해서도 작동합니다. 템플릿 이름을 입력하자마자 Cmdlet이 템플릿을 인출하고 분석한 다음 템플릿 매개 변수를 명령에 동적으로 추가합니다. 따라서 템플릿 매개 변수 값을 매우 쉽게 지정할 수 있습니다. 필수 매개 변수 값을 잊은 경우 PowerShell이 값을 묻는 메시지를 표시합니다.

다음은 매개 변수가 포함된 전체 명령입니다. 리소스의 이름에 대한 고유한 값을 제공할 수 있습니다.

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

명령을 입력하면 누락된 필수 매개 변수 **administratorLoginPassword**를 묻는 메시지가 표시됩니다. 암호를 입력하면 보안 문자열 값이 가려집니다. 이 전략은 암호를 일반 텍스트로 제공할 위험을 제거합니다.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

명령이 실행되고 리소스가 만들어질 때 메시지를 반환합니다. 결국 배포 결과를 표시합니다.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

단 몇 개의 단계를 수행하여 복잡한 웹 사이트에 필요한 리소스를 만들어 배포했습니다.

## 리소스 그룹에 대한 정보 가져오기

리소스 그룹을 만든 후 리소스 관리자 모듈에서 Cmdlet을 사용하여 리소스 그룹을 관리할 수 있습니다.

- 구독에서 모든 리소스 그룹을 가져오려면 **Get-AzureRmResourceGroup** cmdlet를 사용합니다.

		PS C:\> Get-AzureRmResourceGroup

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

      특정 리소스 그룹만 가져오려면 **Name** 매개 변수를 제공합니다.
      
          PS C:\> Get-AzureRmResourceGroup -Name TestRG1

- 리소스 그룹에서 리소스를 가져오려면 **Get-AzureRmResource** cmdlet와 **ResourceGroupName** 매개 변수를 사용합니다. 매개 변수를 사용하지 않고 Find-AzureRmResource를 입력하면 Azure 구독에서 모든 리소스를 가져옵니다.

        PS C:\> Find-AzureRmResource -ResourceGroupNameContains TestRG1
		
        Name              : exampleserver
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
        ResourceName      : exampleserver
        ResourceType      : Microsoft.Sql/servers
        Kind              : v12.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
        ...
	        
- 위의 템플릿에는 하나의 리소스에 대한 태그가 포함됩니다. 태그를 사용하여 구독에서 리소스를 논리적으로 구성할 수 있습니다. **Find-AzureRmResource** 및 **Find-AzureRmResourceGroup** 명령을 사용하여 태그별로 리소스를 쿼리할 수 있습니다.

        PS C:\> Find-AzureRmResource -TagName team

        Name              : ExampleSiteuxq53xiz5etmq
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/ExampleSiteuxq53xiz5etmq
        ResourceName      : ExampleSiteuxq53xiz5etmq
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      태그를 사용하여 더 많은 작업을 수행할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.

## 리소스 그룹 추가

리소스 그룹에 리소스를 추가하려면 **New-AzureRmResource** cmdlet를 사용하면 됩니다. 그러나 이 방법으로 리소스를 추가하면 새 리소스가 템플릿에 존재하지 않기 때문에 나중에 혼동을 일으킬 수 있습니다. 기존 템플릿을 다시 배포하는 경우 불완전한 솔루션을 배포할 수 있습니다. 자주 배포하는 경우 새 리소스를 템플릿에 추가하고 다시 배포하는 것이 더 쉽고 안정됩니다.

## 리소스 이동

기존 리소스를 새 리소스 그룹으로 이동할 수 있습니다. 예제를 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## 리소스 그룹 삭제

- 리소스 그룹에서 리소스를 삭제하려면 **Remove-AzureRmResource** cmdlet를 사용합니다. 이 cmdlet은 리소스를 삭제하지만 리소스 그룹은 삭제하지 않습니다.

	이 명령은 TestRG 리소스 그룹에서 TestSite 웹 사이트를 제거합니다.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- 리소스 그룹을 삭제하려면 **Remove-AzureRmResourceGroup** cmdlet를 사용합니다. 이 cmdlet은 리소스 그룹과 해당 리소스를 삭제합니다.

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## 다음 단계

- 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)을 참조하세요.
- 템플릿 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](./resource-group-template-deploy.md)를 참조하세요.
- 프로젝트 배포의 자세한 예제를 보려면 [Azure에서 예측 가능한 방식으로 microservices 배포](app-service-web/app-service-deploy-complex-application-predictably.md)를 참조하세요.
- 실패한 배포 문제 해결에 대해 알아보려면 [Azure에서 리소스 그룹 배포 문제 해결](./virtual-machines/resource-group-deploy-debug.md)을 참조하세요.

<!---HONumber=AcomDC_0211_2016-->