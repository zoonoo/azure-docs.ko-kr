<properties 
	pageTitle="Azure 리소스 관리자로 Azure PowerShell 사용" 
	description="Azure에 리소스 그룹으로 여러 리소스를 배포하려면 Azure PowerShell을 사용합니다." 
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
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Azure 리소스 관리자로 Azure PowerShell 사용

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Azure 리소스 관리자는 Azure 리소스에 대해 완전히 새로운 방식으로 접근합니다. 개별 리소스를 만들어서 관리하는 대신 블로그, 사진 갤러리, SharePoint 포털, wiki 등의 복잡한 서비스를 생각해 보십시오. 템플릿(서비스의 리소스 모델)을 사용하여 서비스를 지원하는 데 필요한 리소스를 포함하는 리소스 그룹을 만듭니다. 그런 다음 해당 리소스 그룹을 논리 단위로 관리 및 배포할 수 있습니다.

이 자습서에서는 Microsoft Azure용 Azure 리소스 관리자에서 Azure PowerShell을 사용하는 방법에 대해 알아봅니다. 또한 이 자습서에서는 SQL 데이터베이스와 지원하는 데 필요한 모든 리소스를 사용하여 Azure에 호스트된 웹 앱에 대한 리소스 그룹을 만들어서 배포하는 과정을 안내합니다.

## 필수 조건

이 자습서를 완료하려면 Azure PowerShell 버전 0.8.0 이상이 있어야 합니다. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하세요.

이 자습서는 PowerShell 초보자용으로 설계되었지만, 모듈, cmdlet, 세션 등과 같은 기본 개념을 잘 알고 있다고 가정합니다. Windows PowerShell에 대한 자세한 내용은 [Windows PowerShell 시작](http://technet.microsoft.com/library/hh857337.aspx)(영문)을 참조하십시오.

이 설명서에 나오는 cmdlet에 대한 자세한 도움말을 보려면 Get-Help cmdlet을 사용합니다.

	Get-Help <cmdlet-name> -Detailed

예를 들어 Add-AzureAccount cmdlet에 대한 도움말을 보려면 다음과 같이 입력합니다.

	Get-Help Add-AzureAccount -Detailed

## Azure PowerShell 모듈 정보
Azure PowerShell 버전 0.8.0 이상을 설치하면 다음과 같은 하나 이상의 PowerShell 모듈이 포함되어 있습니다. Azure 모듈 또는 Azure 리소스 관리자 모듈에서 사용할 수 있는 명령을 사용할지 여부를 명시적으로 결정해야 합니다. 두 모듈 간을 쉽게 전환할 수 있도록 **Switch-AzureMode**라는 새로운 cmdlet을 Azure 프로필 모듈에 추가했습니다.

Azure PowerShell을 사용할 경우 Azure 모듈의 cmdlet을 기본적으로 가져옵니다. Azure 리소스 관리자 모듈로 전환하려면 Switch-AzureMode cmdlet을 사용합니다. 이 cmdlet은 Azure 모듈을 세션에서 제거하고 Azure 리소스 관리자 및 Azure 프로필 모듈을 가져옵니다.

AzureResoureManager 모듈로 전환하려면 다음을 입력합니다.

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Azure 모듈로 전환하려면 다음을 입력합니다.

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

기본적으로 Switch-AzureMode는 현재 세션에만 영향을 줍니다. 스위치를 모든 PowerShell 세션에서 유효하도록 만들려면 Switch-AzureMode의 **전역** 매개 변수를 사용합니다.

Switch-AzureMode cmdlet에 대한 도움말을 보려면 `Get-Help Switch-AzureMode` 또는 [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398)를 참조하십시오.
  
AzureResourceManager 모듈의 cmdlet 목록을 도움말 개요와 함께 가져오려면 다음을 입력합니다.

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

출력은 다음 발췌와 유사합니다.

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

cmdlet에 대한 전체 도움말을 가져오려면 다음 형식으로 명령을 입력합니다.

	Get-Help <cmdlet-name> -Full

예를 들면 다음과 같습니다.

	Get-Help Get-AzureLocation -Full

Azure 리소스 관리자 명령 전체 집합에 대 해서는 [Azure 리소스 관리자 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765)를 참조하십시오.
  
## 리소스 그룹 만들기

자습서의 이 섹션에서는 SQL 데이터베이스에서 웹 앱에 대한 리소스 그룹을 만들어서 배포하는 과정을 안내합니다.

이 작업은 Azure, SQL, 웹 앱 또는 리소스 관리 전문가가 아니라도 수행할 수 있습니다. 템플릿을 통해 필요한 모든 리소스를 포함하는 리소스 그룹 모델을 제공합니다. Windows PowerShell을 사용하여 작업을 자동화하고 있으므로 이러한 프로세스를 대규모 작업을 스크립팅하기 위한 모델로 활용할 수 있습니다.

### 1단계: Azure 리소스 관리자로 전환 
1. PowerShell을 시작합니다. Azure PowerShell 콘솔, Windows PowerShell ISE 등 원하는 호스트 프로그램을 사용할 수 있습니다.

2. **Switch-AzureMode** cmdlet을 사용하여 AzureResourceManager 및 AzureProfile 모듈에서 cmdlet을 가져옵니다.

        PS C:\> Switch-AzureMode AzureResourceManager

3. Windows PowerShell 세션에 Azure 계정을 추가하려면 **Add-AzureAccount** cmdlet을 사용합니다.

        PS C:\> Add-AzureAccount

Cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Windows PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

계정 설정은 만료되므로 자주 새로 고쳐야 합니다. 계정 설정을 새로 고치려면 **Add-AzureAccount**를 다시 실행합니다.

>[AZURE.NOTE]AzureResourceManager 모듈은 Add-AzureAccount가 필요합니다. 게시 설정 파일로는 충분하지 않습니다.

### 2단계: 갤러리 템플릿 선택

리소스 그룹과 리소스를 만드는 여러 가지 방법이 있지만, 리소스 그룹 템플릿을 사용하는 것이 가장 쉬운 방법입니다. *리소스 그룹 템플릿*은 리소스 그룹에서 리소스를 정의하는 JSON 문자열입니다. 이 문자열은 이름, 크기와 같은 사용자 정의 값에 대한 "매개 변수"라는 자리 표시자를 포함합니다.

Azure는 리소스 그룹 템플릿 갤러리를 호스트하므로 사용자가 템플릿을 처음부터 직접 만들거나 갤러리 템플릿을 편집하여 만들 수 있습니다. 이 자습서에서는 갤러리 템플릿을 사용합니다.

Azure 리소스 그룹 템플릿 갤러리의 모든 템플릿을 보려면 **Get-AzureResourceGroupGalleryTemplate** cmdlet를 사용합니다. 그러나 이 명령은 템플릿 중 많은 수를 반환합니다. 더 많은 관리가 용이한 템플릿을 보려면 게시자 매개 변수를 지정합니다.

Powershell 프롬프트에서 다음을 입력합니다.
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

cmdlet은 Microsoft를 게시자로 사용하여 갤러리 템플릿 목록을 반환합니다. **Identity** 속성을 사용하여 명령에서 템플릿을 식별합니다.

Microsoft.WebSiteSQLDatabase.0.2.6-preview 템플릿은 유용합니다. 명령을 실행하려면 새 버전이 릴리스되었기 때문에 템플릿의 버전이 약간 달라질 수 있습니다. 최신 버전의 템플릿을 사용합니다. 갤러리 템플릿에 대한 자세한 내용을 보려면 **Identity** 매개 변수를 사용합니다. Identity 매개 변수 값은 템플릿의 ID입니다.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

이 cmdlet은 요약 및 설명을 포함하여 템플릿에 대한 자세한 정보와 개체를 반환합니다.

이 템플릿은 요구 사항을 충족하는 것처럼 보입니다. 따라서 이 템플릿을 디스크에 저장하고 자세히 살펴보겠습니다.

### 3단계: 템플릿 검사

템플릿을 디스크에 JSON 파일로 저장하겠습니다. 이 단계는 필수 사항은 아니지만 템플릿을 보기 쉽게 만들어줍니다. 템플릿을 저장하려면 **Save-AzureResourceGroupGalleryTemplate** cmdlet을 사용합니다. **Identity** 매개 변수를 사용하여 템플릿을 지정하고 **Path** 매개 변수를 사용하여 디스크의 경로를 지정합니다.

Save-AzureResourceGroupGalleryTemplate은 템플릿을 저장하고 경로를 JSON 템플릿 파일의 파일 이름으로 반환합니다.

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


메모장과 같은 텍스트 편집기에서 템플릿 파일을 볼 수 있습니다. 각 템플릿에는 **매개 변수** 섹션과 **리소스** 섹션이 있습니다.

템플릿의 **parameters** 섹션은 모든 리소스에서 정의되는 매개 변수 모음입니다. 리소스 그룹을 설정하는 경우 제공할 수 있는 속성 값을 포함합니다.

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

일부 매개 변수는 기본값이 있습니다. 템플릿을 사용할 때 이러한 매개 변수에 대한 값을 제공할 필요가 없습니다. 값을 지정하지 않으면 기본값이 사용됩니다.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

열거된 값이 있는 매개 변수의 경우 유효한 값이 매개 변수와 함께 나열됩니다. 예를 들어 **sku** 매개 변수의 값은 Free, Shared, Basic 또는 Standard입니다. **sku** 매개 변수 값을 지정하지 않을 경우 기본값인 Free가 사용됩니다.

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },


**administratorLoginPassword** 매개 변수는 일반 텍스트가 아닌 보안 문자열을 사용합니다. 보안 문자열에 대한 값을 제공할 경우 해당 값은 가려집니다.

	"administratorLoginPassword": {
      "type": "securestring"
    },

템플릿의 **resources** 섹션에는 템플릿이 만드는 리소스가 나열됩니다. 이 템플릿은 SQL 데이터베이스 서버 및 SQL 데이터베이스, 서버 팜 및 웹 사이트, 다양한 관리 설정을 만듭니다.
  
각 리소스 정의에는 사용자 정의 값에 대한 속성(이름, 유형, 위치 등)과 매개 변수가 포함되어 있습니다. 예를 들어 템플릿의 이 섹션에서 SQL 데이터베이스를 정의할 경우, 이 정의에는 데이터베이스 이름([parameters('databaseName')]), 데이터베이스 서버 위치[parameters('serverLocation')] 및 collation 속성[parameters('collation')]에 대한 매개 변수가 포함되어 있습니다.

    {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[parameters('serverLocation')]",
        "apiVersion": "2.0",
        "dependsOn": [
          "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


항상 템플릿을 사용할 수 있지만, 먼저 각 리소스에 대한 위치를 찾아야 합니다.

### 4단계: 리소스 유형 위치 가져오기

대부분의 템플릿은 리소스 그룹의 각 리소스에 대한 위치를 지정하라는 메시지를 표시합니다. 모든 리소스는 Azure 데이터 센터에 있지만, 모든 Azure 데이터 센터에서 모든 리소스 유형을 지원하는 것은 아닙니다.

리소스 유형을 지원하는 위치를 선택합니다. 동일한 위치에서 리소스 그룹의 모든 리소스를 만들 필요는 없습니다. 그러나 가능 하면 성능을 최적화하기 위해 동일한 위치에 리소스를 만들게 됩니다. 특히, 앱이 액세스할 때 데이터베이스가 동일한 위치에 있는지 확인합니다.

각 리소스 유형을 지원하는 위치를 가져오려면 **Get-AzureLocation** cmdlet을 사용합니다. ResourceGroup와 같은 특정 유형의 리소스에 대해 출력을 제한하려면 다음을 사용합니다.

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

다음과 유사하게 출력됩니다.

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

이제 리소스 그룹을 만드는 데 필요한 정보가 모두 수집되었습니다.

### 5단계: 리소스 그룹 만들기
 
이 단계에서는 리소스 그룹 템플릿을 사용하여 리소스 그룹을 만듭니다. 예를 들어 디스크에서 New_WebSite_And_Database.json 파일을 열고 안내를 따릅니다. 템플릿 파일은 리소스에 대한 올바른 ApiVersion과 같이 전달할 매개 변수 값을 결정하는 데 매우 유용할 수 있습니다.

리소스 그룹을 만들려면 **New-AzureResourceGroup** cmdlet을 사용합니다.

이 명령은 **Name** 매개 변수를 사용하여 리소스 그룹에 대한 이름을 지정하고 **Location** 매개 변수를 사용하여 위치를 지정합니다. **Get-AzureLocation** 출력을 사용하여 리소스 그룹에 대한 위치를 선택합니다. 이 명령은 **GalleryTemplateIdentity** 매개 변수를 사용하여 갤러리 템플릿을 지정합니다.

	PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

템플릿 이름을 입력하면 New-AzureResourceGroup이 템플릿을 가져와서 구문 분석하고, 템플릿 매개 변수를 명령에 동적으로 추가합니다. 따라서 템플릿 매개 변수 값을 매우 쉽게 지정할 수 있습니다. 필수 매개 변수 값을 잊은 경우 Windows PowerShell이 값을 묻는 메시지를 표시합니다.

**동적 템플릿 매개 변수**

매개 변수를 가져오려면 매개 변수 이름을 나타내는 빼기 기호(-)를 입력한 다음 TAB 키를 누릅니다. 또는 매개 변수 이름의 처음 몇 글자(예: siteName)를 입력한 다음 TAB 키를 누릅니다.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

PowerShell이 매개 변수 이름을 완성합니다. 매개 변수 이름을 순환하려면 TAB 키를 반복해서 누릅니다.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

웹 사이트에 대한 이름을 입력하고 각 매개 변수에 대해 TAB 프로세스를 반복합니다. 기본값이 있는 매개 변수는 선택 사항입니다. 기본값을 적용하려면 명령에서 매개 변수를 생략합니다.

이 템플릿의 sku 매개 변수처럼 템플릿 매개 변수에 열거된 값이 있는 경우 매개 변수 값을 순환하려면 TAB 키를 누릅니다.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

다음은 필수 템플릿 매개 변수와 **Verbose** 공통 매개 변수만 지정하는 New-AzureResourceGroup 명령의 예입니다. **administratorLoginPassword**는 생략됩니다.

	PS C:\> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

명령을 입력하면 누락된 필수 매개 변수 **administratorLoginPassword**를 묻는 메시지가 표시됩니다. 암호를 입력하면 보안 문자열 값이 가려집니다. 이 전략은 암호를 일반 텍스트로 제공할 위험을 제거합니다.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-AzureResourceGroup**은 만들어서 배포한 리소스 그룹을 반환합니다.

단 몇 개의 단계를 수행하여 복잡한 웹 사이트에 필요한 리소스를 만들어 배포했습니다. 갤러리 템플릿은 이 작업을 수행하는 데 필요한 거의 모든 정보를 제공했습니다. 또한 작업은 쉽게 자동화됩니다.

## 리소스 그룹에 대한 정보 가져오기

리소스 그룹을 만든 후 AzureResourceManager 모듈에서 cmdlet을 사용하여 리소스 그룹을 관리할 수 있습니다.

- 구독에서 모든 리소스 그룹을 가져오려면 **Get-AzureResourceGroup cmdlet**을 사용합니다.

		PS C:\>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- 리소스 그룹에서 리소스를 가져오려면 **Get-AzureResource** cmdlet과 ResourceGroupName 매개 변수를 사용합니다. 매개 변수를 사용하지 않고 Get-AzureResource를 입력하면 Azure 구독에서 모든 리소스를 가져옵니다.

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## 리소스 그룹 추가

- 리소스 그룹에 리소스를 추가하려면 **New-AzureResource** cmdlet을 사용합니다. 이 명령은 TestRG 리소스 그룹에 새 웹 사이트를 추가합니다. 이 명령은 템플릿을 사용하지 않기 때문에 좀 더 복잡합니다. 

        PS C:\>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- 리소스 그룹에 새 템플릿 기반 배포를 추가하려면 **New-AzureResourceGroupDeployment** 명령을 사용합니다.

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## 리소스 이동

기존 리소스를 새 리소스 그룹으로 이동할 수 있습니다. 예제를 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## 리소스 그룹 삭제

- 리소스 그룹에서 리소스를 삭제하려면 **Remove-AzureResource** cmdlet을 사용합니다. 이 cmdlet은 리소스를 삭제하지만 리소스 그룹은 삭제하지 않습니다.

	이 명령은 TestRG 리소스 그룹에서 TestSite2 웹 사이트를 제거합니다.

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- 리소스 그룹을 삭제하려면 **Remove-AzureResourceGroup** cmdlet을 사용합니다. 이 cmdlet은 리소스 그룹과 해당 리소스를 삭제합니다.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## 리소스 그룹 문제 해결
AzureResourceManager 모듈에서 cmdlet 실험 중에 오류가 발생할 수 있습니다. 이 섹션을 팁을 사용하여 오류를 해결하십시오.

### 오류 방지

AzureResourceManager 모듈에는 오류를 방지하는 데 유용한 cmdlet이 포함되어 있습니다.


- **Get-AzureLocation**: 이 cmdlet은 각 리소스 유형을 지원하는 위치를 가져옵니다. 리소스에 대한 위치를 입력하기 전에 이 cmdlet을 사용하여 해당 위치에서 리소스 유형을 지원하는지 확인하세요.


- **Test-AzureResourceGroupTemplate**: 템플릿과 템플릿 매개 변수를 사용하기 전에 테스트합니다. 사용자 지정 또는 갤러리 템플릿과 사용할 템플릿 매개 변수 값을 입력합니다. 이 cmdlet은 템플릿이 내부적으로 일관되고 설정된 매개 변수 값이 템플릿과 일치하는지 여부를 테스트합니다.



### 오류 수정

- **Get-AzureResourceGroupLog**: 이 cmdlet은 로그에서 리소스 그룹의 각 배포에 대한 항목을 가져옵니다. 오류가 있는 경우 배포 로그 검사를 시작합니다. 

- **세부 정보 표시 및 디버그**: AzureResourceManager 모듈의 cmdlet은 실제 작업을 수행하는 REST API를 호출합니다. API가 반환하는 메시지를 표시하려면 $DebugPreference 변수를 "Continue"로 설정하고 명령에서 Verbose 공통 매개 변수를 사용합니다. 메시지에 오류의 원인에 대한 중요한 단서가 제공되는 경우도 있습니다.

- **Azure 자격 증명이 설정되지 않았거나 만료된 경우**: Windows PowerShell 세션에서 자격 증명을 새로 고치려면 Add-AzureAccount cmdlet을 사용합니다. 게시 설정 파일의 자격 증명으로는 AzureResourceManager 모듈의 cmdlet을 지원할 수 없습니다.


## 다음 단계
시작하기

- [Azure 리소스 관리자 개요](./resource-group-overview.md)
- [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](./xplat-cli-azure-resource-manager.md)
- [Azure 포털을 사용하여 Azure 리소스 관리](./resource-group-portal.md)

응용 프로그램 만들기 및 배포

- [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)
- [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](./resource-group-template-deploy.md)
- [Azure에서 예측 가능하도록 복잡한 응용 프로그램을 배포](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Azure에서 리소스 그룹 배포 문제 해결](./resource-group-deploy-debug.md)
- [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)
- [고급 템플릿 작업](./resource-group-advanced-template.md)

리소스 구성

- [태그를 사용하여 Azure 리소스 구성](./resource-group-using-tags.md)

액세스 관리 및 감사

- [리소스에 대한 액세스 관리 및 감사](./resource-group-rbac.md)
- [Azure 리소스 관리자를 사용하여 서비스 사용자 인증](./resource-group-authenticate-service-principal.md)
- [Azure 클래식 포털을 사용하여 새 Azure 서비스 사용자 만들기](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO4-->