<properties
	pageTitle="Azure CLI 및 API 앱 | Microsoft Azure"
	description="Azure API 앱을 사용한 Mac, Linux 및 Windows용 Microsoft Azure 명령줄 인터페이스(CLI) 사용 방법."
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Azure CLI(명령줄 인터페이스) 및 API 앱

이 문서에서는 Mac, Linux 및 Windows용 Azure CLI(명령줄 인터페이스)를 사용하여 Azure 앱 서비스에서 API 앱을 만들고 관리하고 삭제하는 방법을 보여 줍니다.

## 필수 조건

이 문서에서는 Azure CLI를 설치했고 기본 작업을 수행하는 방법을 안다고 가정합니다. CLI에 대한 소개는 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하세요.

> [AZURE.NOTE] [Azure 구독에 연결](../xplat-cli-connect.md)의 지침은 두 가지 대안, 회사 또는 학교 계정을 사용하여 로그인 및 *.publishsettings* 파일 다운로드를 제공합니다. API 앱의 경우, *.publishsettings* 파일 인증 방법이 작동하지 않습니다. 리소스 관리 모드를 사용하여 API 앱을 작업해야 하기 때문이며 *.publishsettings* 파일 인증 방법은 리소스 관리자와 작동하지 않습니다.

## 리소스 관리 모드 사용

CLI는 [서비스 관리(asm)](../virtual-machines/virtual-machines-command-line-tools.md) 모드 또는 [리소스 관리(arm)](../xplat-cli-azure-resource-manager.md)모드에서 사용될 수 있습니다. API 앱의 경우, 리소스 관리 모드를 사용해야 합니다. `arm` 모드가 기본적으로 활성화되지 않기 때문에 `config mode arm` 명령을 사용하여 활성화합니다.

	azure config mode arm

## API 앱 작업에 사용할 수 있는 명령 목록

현재 API 앱 작업에 사용할 수 있는 모든 명령을 보려면, `apiapp` 명령을 실행합니다.

	azure apiapp

## 구독 또는 리소스 그룹의 모든 API 앱 목록

구독에 있는 모든 API 앱을 나열하려면 매개 변수 없이 `apiapp list` 명령을 실행합니다.

	azure apiapp list

목록은 리소스 그룹, API 앱 이름, 패키지 ID 및 각 API 앱의 URL을 표시합니다.

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

지정된 리소스 그룹으로 목록을 제한하려면 그룹(`-g`) 매개 변수를 추가합니다.

	azure apiapp list -g <resource group name>

예:

	azure apiapp list -g mygroup

API 앱 버전 및 액세스 수준 정보를 목록에 추가하려면 추가 세부 정보(`-d`) 매개 변수를 추가합니다.

	azure apiapp list -d

추가 필드가 있는 `list` 출력은 다음 예제와 같습니다.

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### API 앱에 대한 정보 나열

하나의 API 앱에 대한 정보를 나열하려면, 그룹(`-g`) 및 API 앱 이름(`-n`) 매개 변수가 있는 `apiapp show` 명령을 사용합니다.

	azure apiapp show -g <resource group name> -n <API app name>

예:

	azure apiapp show -g mygroup -n SimpleDropbox

출력은 다음과 같이 표시됩니다.

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## API 앱 만들기

API 앱을 만드는 방법에는 다음 두 가지가 있습니다. 명령적 CLI 명령을 사용하여 Azure 리소스를 개별적으로 만들거나 템플릿의 선언적 구문을 사용하여 필요한 리소스의 모든 작업을 함께 정의하고 CLI 명령으로 해당 템플릿을 배포할 수 있습니다. 선언적 방식은 [다음 단계](#next-steps)를 참조하세요.

명령적 방법을 사용하여 API 앱을 만들려면 다음 단계를 수행합니다.

1. 올바른 위치 선택
1. 사용할 리소스 그룹 만들기 또는 검색
2. 사용할 앱 서비스 계획 만들기 또는 검색
4. API 앱 만들기

### 올바른 위치 선택

리소스 그룹을 만들 때 위치를 지정해야 합니다. 다음은 API 앱에 사용할 수 있는 몇 가지 위치입니다.

* 미국 동부
* 미국 서부
* 미국 중남부
* 북유럽
* 동아시아
* 일본 동부
* 서유럽
* 동남아시아
* 일본 서부
* 미국 중북부
* 미국 중부
* 브라질 남부
* 미국 동부 2

전체 최신 위치 목록을 가져오려면 `location list` 명령을 사용하고 `Microsoft.AppService/apiapps` 리소스 공급자 행을 참조하세요.

	azure location list

다음은 `location list` 명령의 샘플 출력입니다.

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### 사용할 리소스 그룹 만들기 또는 검색

리소스 그룹을 만들려면 이름(`n`) 및 위치(`l`) 매개 변수인 `group create` 명령을 사용합니다.

	azure group create -n <name> -l <location>

예:

	azure group create -n "mygroup" -l "West US"

기존 리소스 그룹을 찾으려면 `group list` 명령을 사용하고 API 앱에 대해 올바른 위치에서 리소스 그룹을 선택합니다.

	azure group list

### 사용할 앱 서비스 계획 만들기 또는 검색

앱 서비스 계획을 만들려면, `resource create` 명령을 사용하고 리소스 형식 매개 변수(`-r`)를 사용하여 만들려는 리소스의 유형을 앱 서비스 계획이라고 지정합니다.

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
예:

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

REST API의 일부 최근 변경 내용 때문에 `properties`(`-p`) 매개 변수에 대한 JSON 문자열이 필요합니다. 이후에 `-p` 매개 변수는 선택적입니다.

샘플 명령은 이 기사가 작성된 날짜를 기준으로 최신 API 버전을 지정합니다. 최신 버전이 사용 가능한지를 확인하려면 `provider show` 명령을 사용하고 `resourceTypes` 배열의 `sites` 개체에 대한 `apiVersions` 배열을 참조합니다.

	azure provider show -n Microsoft.Web --json
   
다음은 명령 출력의 `sites` 개체 샘플입니다.

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

기존 앱 서비스 계획을 나열하려면, `resource list` 명령을 사용하고 `-r` 매개 변수를 사용하여 리소스 유형으로 앱 서비스 계획을 지정합니다.

	azure resource list -r Microsoft.Web/serverfarms

출력은 다음과 같이 표시됩니다.

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### 빈(사용자 지정) API 앱 만들기

빈 API 앱을 만들려면(자신에 대한 코드를 작성한 앱), `apiapp create` 명령을 사용하고 `Microsoft.ApiApp` Nuget 패키지(`-u`매개 변수)를 지정합니다.

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

예:

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

API 앱으로서의 출력 보고서 진행률이 생성됩니다.

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### 마켓플레이스에서 API 앱 만들기

마켓플레이스에서 사용할 수 있는 API 앱 패키지의 목록을 가져오려면, `group template list` 명령을 사용하고 범주(`-c`) 매개 변수를 사용하여 API 앱을 지정합니다.

	azure group template list -c apiapps

출력은 다음 예제와 같이 표시됩니다.

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

마켓플레이스에서 API 앱을 만들려면 `apiapap create` 명령을 사용하고 NuGet 패키지(`-u`) 매개 변수를 사용하여 만들려는 API 앱의 이름을 지정합니다.

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

`-u` 매개 변수로 사용할 값은 마켓플레이스 이름의 가운데 섹션입니다. 예를 들어, microsoft\_com.MicrosoftSqlConnector.0.2.2의 경우 명령은 다음과 같습니다.

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
SQL 커넥터에 대한 연결 문자열 및 서버 이름과 같은 모든 API 앱 템플릿 매개 변수가 필요한 경우, 필요한 데이터를 입력하라는 메시지가 나타납니다. `--parameters` 또는 `--parameters-file`을 사용하여 매개 변수 값에 전달하는 옵션이 있습니다. 매개 변수 파일에 대한 자세한 내용은 [새 게이트웨이로 API 앱 프로비전](app-service-api-arm-new-gateway-provision.md)을 참조하세요.

## 다음 단계

이 문서에서는 개별 Azure CLI 명령을 사용하여 사용자 지정 API 앱 또는 마켓플레이스 템플릿에서 만든 API 앱을 작업하는 방법을 설명합니다. API 앱 만들기를 자동화하는 사용자 지정 템플릿을 사용 하는 방법에 대한 정보는 다음 리소스를 참조하세요.

* [기존 게이트웨이로 API 앱을 프로비전](app-service-api-arm-existing-gateway-provision.md)
* [새 게이트웨이로 API 앱을 프로비전](app-service-api-arm-new-gateway-provision.md)

Azure 리소스 관리자와 함께 Azure 명령줄 유틸리티를 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.
 
* [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](../xplat-cli-azure-resource-manager.md).
* [Azure 리소스 관리자로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)
 

<!---HONumber=AcomDC_0114_2016--->