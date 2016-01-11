<properties
   pageTitle="개발 및 테스트 환경 | Microsoft Azure"
   description="Azure 리소스 관리자 템플릿을 사용하여 개발 및 테스트 환경을 빠르고 일관성 있게 만들고 삭제하는 방법에 대해 알아봅니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="jdial"/>

# Microsoft Azure의 개발 및 테스트 환경

사용자 지정 응용 프로그램은 일반적으로 프로덕션 환경으로 배포하기 전에 여러 개발 및 테스트 환경에 배포됩니다. 온-프레미스 환경을 만들면 각 응용 프로그램의 각 환경에 대해 컴퓨팅 리소스가 조달 또는 할당됩니다. 환경에는 흔히 수동으로 또는 복잡한 자동화 스크립트를 사용하여 배포된 특정 구성을 가진 여러 물리적 또는 가상 컴퓨터가 포함됩니다. 배포는 흔히 시간이 오래 걸리고 환경에 걸쳐 일관되지 않은 구성을 초래합니다.

## 시나리오 ##

Microsoft Azure에서 개발 및 테스트 환경을 프로비전하는 경우 사용하는 리소스에 대해서만 지불합니다. 이 문서에서는 아래 그림과 같이 Azure 리소스 관리자 템플릿과 매개 변수 파일을 사용하여 개발 및 테스트 환경을 빠르고 일관성 있게 만들고, 유지관리 및 삭제하는 방법을 설명합니다.

![시나리오](./media/solution-dev-test-environments-preview-portal/scenario.png)

위 그림에 세 가지 개발 및 테스트 환경이 나와 있습니다. 각 환경에 웹 응용 프로그램 및 SQL 데이터베이스가 있습니다. 각 환경의 응용 프로그램 및 데이터베이스의 이름은 서로 다릅니다. 이 문서에서는 템플릿을 사용하여 환경 전체에 걸쳐 같은 리소스를 배포하고, 고유한 매개 변수 파일을 사용하여 환경 전체에 걸쳐 리소스에 대해 서로 다른 구성을 지정할 수 있는 방법을 설명합니다.

Azure 리소스 관리자 개념에 익숙하지 않은 경우 이 문서를 읽기 전에 [Azure 리소스 관리자 개요](resource-group-overview.md)를 읽는 것이 좋습니다.

Azure 리소스 관리자 템플릿을 사용하여 어느 정도 경험을 빨리 얻기 위해 참조 문서를 읽지 않고 먼저 이 문서의 단계를 나열된 순서대로 수행하는 것이 좋습니다. 단계를 수행하다 보면 많은 궁금증이 해소될 것입니다. 단계를 한 번 완료한 후 단계를 더 연습하면서 참조 문서를 읽으면 대부분의 질문에 대한 답을 얻을 수 있습니다.

## Azure 리소스 사용 계획
응용 프로그램에 대한 높은 수준의 디자인을 설정한 후 다음 사항을 정의할 수 있습니다.

- 사용자의 응용 프로그램에 포함될 Azure 리소스는 다음을 포함합니다. 응용 프로그램을 빌드하고 Azure SQL 데이터베이스를 사용하여 Azure 웹 앱으로 배포할 수 있습니다. PHP 및 MySQL 또는 IIS 및 SQL Server 또는 다른 구성 요소를 사용하여 가상 컴퓨터에서 응용 프로그램을 빌드할 수 있습니다. [Azure 앱 서비스, 클라우드 서비스 및 가상 컴퓨터 비교](app-service-web/choose-web-site-cloud-service-vm.md) 문서는 사용자의 응용 프로그램에 이용할 수 있는 Azure 리소스를 결정하는 데 도움이 됩니다.
- 사용자의 응용 프로그램에 맞는 가용성, 보안 및 규모 등과 같은 서비스 수준 요구 사항.

## Azure 리소스 관리자 템플릿 사용
Azure 리소스 관리자 템플릿은 사용자의 응용 프로그램이 이용하는 모든 Azure 리소스를 정의합니다. Azure Preview 포털에서 직접 배포하거나 사용자의 응용 프로그램 코드를 사용하여 소스 제어 시스템에서 다운로드, 수정 및 저장할 수 있는 여러 템플릿이 이미 있습니다. 사용자의 응용 프로그램과 함께 사용하려는 자원을 포함하고 있는 기회가 있고 기존 템플릿이 존재합니다. [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/) GitHub 리포지토리에서 사용 가능한 템플릿 목록을 찾을 수 있습니다.

목록에 "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)" 폴더가 나타납니다. 많은 사용자 지정 응용 프로그램은 웹 응용 프로그램 및 SQL 데이터베이스를 포함하고 있으므로, 사용자가 템플릿 사용 방법을 이해할 수 있도록 이 템플릿을 이 문서의 나머지 부분에서 예제로 사용합니다. 이 템플릿으로 만들고 구성하는 모든 것을 완전하게 설명하는 것은 이 기사의 범위를 벗어나지만, 사용자의 조직에서 이 템플릿을 사용하여 실제 환경을 만들려는 경우 [SQL 데이터베이스를 사용하여 웹 앱 프로비전](app-service-web/app-service-web-arm-with-sql-database-provision.md) 문서를 읽고 완전히 이해하는 것이 좋습니다.

> [AZURE.NOTE][SQL 데이터베이스를 사용하여 웹 앱 프로비전](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)의 "Azure에 배포" 단추를 클릭하여 Azure에 템플릿을 배포할 수 있습니다. 이 작업이 템플릿에 관하여 배우는 데 유용할 수 있지만, 그럴 경우 사용자의 응용 프로그램 코드를 사용하여 템플릿 및 매개 변수 값을 편집, 버전 관리 및 저장할 수 없습니다. 이 문서의 단계는 사용자의 응용 프로그램 코드를 사용하여 템플릿 및 매개 변수 값을 저장하고 버전 관리할 수 있는 방법을 설명합니다.

  **1 단계:** 201-web-app-sql-database 폴더에서 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) 파일의 내용을 봅니다. 이 파일은 Azure 리소스 관리자 템플릿 파일입니다. 보기 모드에서 "[원시](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json)" 단추를 클릭합니다. 마우스로 이 파일의 전체 내용을 선택하고 "TestApp1 Template.json"이라는 이름의 파일로 컴퓨터에 저장합니다.

템플릿 파일에 이 템플릿으로 만든 Azure 리소스를 정의하는 "리소스" 섹션이 표시됩니다. 다른 리소스 유형 중에서도 이 템플릿은 [Azure 웹 앱](app-service-web/app-service-web-overview.md) 및 [Azure SQL 데이터베이스](sql-database/sql-database-technical-overview.md) 리소스를 만듭니다.

또한 각 리소스를 구성할 수 있는 매개 변수를 정의하는 "매개 변수" 섹션도 나타납니다. 서식 파일에 지정한 일부 매개 변수는 "defaultValue" 속성을 가지고 있는 반면, 다른 매개 변수는 이 속성을 가지고 있지 않습니다. 템플릿 사용하여 Azure 리소스를 배포할 때는 defaultValue 속성을 갖고 있지 않은 모든 매개 변수에 값을 제공해야 합니다. DefaultValue 속성을 가지고 있는 매개 변수에 대해 값을 제공하지 않으면 템플릿의 defaultValue 매개 변수에 대해 지정된 값이 사용 됩니다.

템플릿은 만들어진 Azure 리소스 및 각 리소스가 구성할 수 있는 매개 변수를 정의합니다. 일반적으로 각 개발 및 테스트 환경에서 동일한 리소스를 만들려고 합니다. [Azure 리소스 관리자 템플릿 디자인 모범 사례](best-practices-resource-manager-design-templates.md) 문서를 읽으면 템플릿 및 직접 디자인하는 방법을 더 자세히 배울 수 있습니다.

## 매개 변수 파일 만들기

아마 각 환경에서 동일한 Azure 리소스를 만들려 하겠지만 각 환경에서 리소스를 서로 다르게 구성하는 것이 좋습니다. 이 리소스는 매개 변수 파일 들어오는 장소입니다.

  **2 단계:** 201-web-app-sql-database 폴더에서 [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) 파일의 내용을 봅니다. 이 파일은 1 단계에서 저장한 템플릿 파일에 대한 매개 변수 파일입니다. 보기 모드에서 "[원시](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json)" 단추를 클릭합니다. 마우스로 이 파일의 전체 내용을 선택하고 다음과 같은 이름으로 컴퓨터의 서로 다른 파일 3개에 저장합니다.

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **3 단계:** 텍스트 또는 JSON 편집기를 사용하여 2 단계에서 만든 개발 및 테스트 환경 매개 변수 파일을 편집하고 파일의 기존 값을 아래 값으로 바꿉니다.

  --TestApp1-Parameters-Development.json--

| 매개 변수 | 설명 |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | 미국 중부 |
| **serverName** | testapp1dev |
| **serverLocation** | 미국 중부 |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| 매개 변수 | 설명 |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | 미국 중부 |
| **serverName** | testapp1test |
| **serverLocation** | 미국 중부 |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

6 단계에서 이 매개 변수 파일을 사용하여 개발 및 테스트 환경에서 Azure웹 앱 및 Azure SQL 데이터베이스 리소스에 대한 고유한 구성을 만듭니다.

 **4 단계:** 2 단계에서 만든 TestApp1-Parameters-Pre-Production.json 매개 변수 파일을 편집합니다. 파일의 전체 내용을 아래 내용으로 바꿉니다.

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

위의 프로덕션 전 매개 변수 파일에서 **sku** 및 **requestedServiceObjectiveName** 매개 변수는 *추가*된 반면에, 개발 및 테스트 매개 변수 파일에는 이들이 추가되지 않았습니다. 왜냐하면 템플릿에는 이 매개 변수에 대해 지정된 기본값이 있고 개발 및 테스트 환경에는 기본값이 사용되기 때문이지만, 프로덕션 전 환경에서는 이 매개 변수에 대해 기본값이 아닌 값이 사용됩니다.

프로덕션 전 환경에서 이 매개 변수에 대해 기본값이 아닌 값이 사용되는 이유는 이 매개 변수에 대해 사용자의 프로덕션 환경에 선호할 수 있는 값을 테스트하기 위해서입니다. 이러한 매개 변수는 모두 Azure [웹 앱 호스팅 계획](http://azure.microsoft.com/pricing/details/app-service/) 또는 **sku** 및 Azure [SQL 데이터베이스](http://azure.microsoft.com/pricing/details/sql-database/), 또는 응용 프로그램에서 사용하는 **requestedServiceObjectiveName**과 관계가 있습니다. sku 및 서비스 대상 이름이 목표 이름이 서로 다르면 비용 및 기능이 서로 다르며 서로 다른 서비스 수준 메트릭을 지원합니다.

아래 표는 템플릿에 지정된 이 매개 변수에 대한 기본값 및 프로덕션 전 매개 변수 파일의 기본값 대신 사용되는 값을 나열합니다.

| 매개 변수 | 기본값 | 매개 변수 파일 |
|---|---|---|
| **sku** | 무료 | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## 환경 만들기
모든 Azure 리소스는 [Azure 리소스 그룹](azure-portal/resource-group-portal.md) 내에 만들어야 합니다. 리소스 그룹을 사용하여 Azure 리소스를 모아서 관리할 수 있도록 그룹화 할 수 있습니다. 조직의 특정인이 리소스 그룹 및 해당 그룹 내의 리소스를 만들거나, 수정하거나, 삭제하거나, 볼 수 있도록 해당 그룹에 [권한](./active-directory/role-based-access-control-configure.md)을 할당할 수 있습니다. 리소스 그룹의 리소스에 대한 경고 및 청구 정보는 [Azure Preview 포털](https://portal.azure.com)에서 볼 수 있습니다. 리소스 그룹은 Azure [위치](http://azure.microsoft.com/regions/)에 만들어집니다. 이 문서에서 모든 리소스는 미국 중부 위치에 만들어집니다. 실제 환경을 만들기 시작할 때 사용자의 요구 사항에 가장 알맞은 위치를 선택합니다.

  **5 단계:** 아래 방법 중 하나를 사용하여 개발 및 테스트 환경에 대한 리소스 그룹을 만듭니다. 두 방법 모두 정확히 같은 결과를 달성합니다.

  **방법 1:** Azure 명령줄 인터페이스(CLI)

  Windows, OS X 또는 Linux 컴퓨터에 CLI를 [설치했는지](xplat-cli-install.md), 그리고 조직 ID를 Azure 구독에 [연결했는지](xplat-cli-connect.md) 확인합니다. CLI 명령줄에서 개발 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

	azure group create "TestApp1-Development" "Central US"

  명령이 성공하면 다음 결과를 반환합니다.

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  테스트 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

	azure group create "TestApp1-Test" "Central US"

  프로덕션 전 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

	azure group create "TestApp1-Pre-Production" "Central US"

  **방법 2:** PowerShell

  [Azure PowerShell 설치 및 구성 방법](powershell-install-configure.md) 문서에 자세히 설명한 대로 Windows 컴퓨터에 PowerShell을 설치하고 사용자의 구독에 연결했는지 확인합니다. PowerShell 명령 프롬프트에서 개발 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다. Azure PowerShell 1.0 Preview를 사용하는 경우 명령은 아래 나와 있는 것처럼 **New-AzureRmResourceGroup**입니다. Azure PowerShell 1.0 Preview 이전 버전을 사용하는 경우 명령은 **New-AzureResourceGroup**입니다.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

  명령이 성공하면 다음 결과를 반환합니다.

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  테스트 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

  프로덕션 전 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **6단계:** 아래 방법 중 하나를 수행하여 응용 프로그램용 템플릿 파일 및 각 환경용 매개 변수 파일을 사용하여 Azure 리소스를 각 환경에 대한 리소스 그룹에 배포합니다. 두 방법 모두 정확히 같은 결과를 달성합니다.

  **방법 1:** Azure CLI(명령줄 인터페이스)

  CLI 명령줄에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 개발 환경에 대해 만든 리소스 그룹에 배포합니다.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  명령이 성공하면 다음 결과를 반환합니다.

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  명령이 성공하지 못하면, 모든 오류 메시지를 해결하고 다시 시도하십시오. 일반적인 문제는 Azure 리소스 명명 제약 조건을 준수하지 않는 매개 변수 값을 사용하는 것입니다. 다른 문제 해결 팁은 [Azure에서 리소스 그룹 배포 문제 해결](virtual-machines/resource-group-deploy-debug.md) 문서에서 찾을 수 있습니다.

  CLI 명령줄에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 테스트 환경에 대해 만든 리소스 그룹에 배포합니다.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  CLI 명령줄에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 프로덕션 전 환경에 대해 만든 리소스 그룹에 배포합니다.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **방법 2:** PowerShell

  PowerShell 명령프롬프트에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 개발 환경에 대해 만든 리소스 그룹에 배포합니다. Azure PowerShell 1.0 Preview를 사용하는 경우 명령은 아래 나와 있는 것처럼 **New-AzureRmResourceGroupDeployment**입니다. Azure PowerShell 1.0 Preview 이전 버전을 사용하는 경우 명령은 **New-AzureResourceGroupDeployment**입니다.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  명령이 성공하면 다음 결과를 반환합니다.

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  명령이 성공하지 못하면, 모든 오류 메시지를 해결하고 다시 시도하십시오. 일반적인 문제는 Azure 리소스 명명 제약 조건을 준수하지 않는 매개 변수 값을 사용하는 것입니다. 다른 문제 해결 팁은 [Azure에서 리소스 그룹 배포 문제 해결](virtual-machines/resource-group-deploy-debug.md) 문서에서 찾을 수 있습니다.

  PowerShell 명령프롬프트에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 테스트 환경에 대해 만든 리소스 그룹에 배포합니다.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  PowerShell 명령프롬프트에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 프로덕션 전 환경에 대해 만든 리소스 그룹에 배포합니다.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

소스 제어 시스템의 응용 프로그램 코드를 사용하여 템플릿 및 매개 변수 파일을 버전 지정하고 유지 관리할 수 있습니다. 또한 위의 명령을 스크립트 파일에 저장하고 사용자의 코드도 함께 저장할 수 있습니다.

## 환경 유지 관리
개발 과정 전체에 걸쳐 서로 다른 환경에서 Azure 리소스의 구성이 의도적으로 또는 실수로 일관성 없이 변경될 수 있습니다. 이 때문에 응용 프로그램 개발 주기에 불필요한 문제 해결을 야기할 수 있습니다.

  **7단계:** 환경을 변경합니다. [Azure Preview 포털](https://portal.azure.com)을 열고 위의 단계를 완료하는 데 사용한 것과 동일한 계정을 사용하여 로그인합니다. 아래 그림과 같이 모두 찾아보기-->리소스 그룹을 클릭합니다(리소스 그룹을 보려면 찾아보기 블레이드에서 아래로 스크롤해야 할 수 있음). 이전 단계의 방법 중 하나를 사용하여 만든 리소스 그룹 3개가 모두 나타납니다. 아래와 같이 TestApp1-Development 리소스 그룹을 클릭합니다.

  ![포털](./media/solution-dev-test-environments-preview-portal/portal1.png)

  TestApp1-Development 리소스 그룹을 클릭한 후 이전 단계에서 완료한 리소스 그룹 배포의 템플릿으로 만든 리소스를 나열하는 블레이드가 나타납니다. 아래와 같이 TestApp1Dev-->삭제를 클릭하여 TestApp1Dev 웹 앱 리소스를 삭제합니다.

  ![포털](./media/solution-dev-test-environments-preview-portal/portal2.png)

  리소스를 삭제할지 여부에 관하여 묻는 포털 프롬프트가 표시될 때 "예"를 클릭합니다. 리소스 그룹의 내용은 이제 예상과 다릅니다. 여러 리소스 그룹에서 여러 리소스를 삭제하거나 심지어 일부 리소스에 대한 구성 설정을 변경하여 좀더 실험해 볼 수 있습니다.

> [AZURE.NOTE]Azure Preview 포털을 사용하여 리소스 그룹에서 리소스를 삭제하는 대신 CLI에서 [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) 명령 또는 "azure resource delete" 명령을 사용하여 같은 작업을 달성할 수 있습니다.

  **8단계:** 6단계에서 사용한 것과 동일한 명령을 사용하여 환경을 리소스 그룹에 다시 배포하되 "Deployment1"을 "Deployment2"로 바꿉니다. 아래 그림의 요약 섹션과 같이 템플릿에서 나온 모든 리소스가 TestApp1-Development 리소스 그룹에 다시 존재하는 것을 볼 수 있습니다. Azure 리소스 관리자 템플릿을 사용하여 환경을 배포하는 방법의 이점 중 하나는 환경을 언제든지 알려진 상태로 되돌려 쉽게 다시 배포할 수 있다는 것입니다.

  ![포털](./media/solution-dev-test-environments-preview-portal/portal3.png)

  그림에서 "마지막 배포" 아래에 있는 텍스트를 클릭하면 리소스 그룹에 대한 배포 기록을 보여 주는 블레이드가 표시됩니다. 첫 번째 배포에 이름 "Deployment1"을 사용하고 두 번째 배포에 "Deployment2"를 사용했으므로 항목이 두 개입니다. 배포를 클릭하면 각 배포에 대한 결과 보여주는 블레이드가 표시됩니다.

## 환경 삭제
환경에 대한 작업을 마친 후 더 이상 사용하지 않는 Azure 리소스에 대한 사용량 수수료가 발생하지 않도록 해당 리소스를 삭제하는 것이 좋습니다. 환경을 삭제하는 것은 환경을 만드는 것보다 더 쉽습니다. 이전 단계에서 각 환경에 대해 개별 Azure 리소스 그룹이 만들어졌습니다. 리소스 그룹을 삭제하면 해당 그룹에 포함된 리소스도 모두 삭제됩니다. 아래 방법 중 하나를 수행하면 환경에서 이전에 배포한 모든 Azure 리소스와 함께 환경(리소스 그룹)이 삭제됩니다.

  **9단계:** 아래 방법 중 하나를 사용하여 환경을 삭제합니다. 두 방법 모두 정확히 같은 결과를 달성합니다.

  **방법 1: Azure CLI**

  CLI 프롬프트에서 다음을 입력합니다.

	azure group delete "TestApp1-Development"

  프롬프트가 표시될 때 “y”를 입력하면 명령이 다음 결과를 반환합니다.

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  CLI 프롬프트에서 다음을 입력하여 남은 환경을 삭제합니다.

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **방법 2:** PowerShell

  Azure PowerShell 1.0 Preview를 사용하는 경우 리소스 그룹을 삭제하는 명령은 아래 나와 있는 것처럼 **Remove-AzureRmResourceGroup**입니다. Azure PowerShell 1.0 Preview 이전 버전을 사용하는 경우 명령은 **Remove-AzureResourceGroup**입니다. PowerShell 프롬프트에서 다음을 입력합니다.

	Remove-AzureRmResourceGroup -Name TestApp1-Development

  프롬프트가 표시될 때 “y”를 입력하면 명령이 다음 결과를 반환합니다.

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  PowerShell 프롬프트에서 다음을 입력하여 남은 환경을 삭제합니다.

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

사용하는 방법에 상관없이 명령이 실행을 마친 후 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 더 이상 존재하지 않으며 리소스에 대한 비용 청구가 더 이상 발생하지 않습니다.

응용 프로그램 개발 중에 발생하는 Azure 리소스 사용률 비용을 최소화하기 위해 [Azure 자동화](automation/automation-intro.md)를 사용하여 다음과 같은 작업을 예약할 수 있습니다.

- 각 날짜가 끝날 때 가상 컴퓨터를 중지하고 각 날짜가 시작할 때 다시 시작합니다.
- 각 날짜가 끝날 때 전체 환경을 삭제하고 각 날짜가 시작할 때 다시 만듭니다.
 
이제 개발 및 테스트 환경을 쉽게 만들고 유지 관리 및 삭제하는 방법을 경험했으므로 위 단계를 더 실험하고 이 문서에 포함된 참조 자료를 읽어서 방금 했던 작업에 관하여 더 자세히 배울 수 있습니다.

## 다음 단계

- Microsoft Azure AD 그룹 또는 사용자를 Azure 리소스에 대한 작업의 하위 집합을 수행하는 기능을 가진 특정 역할에 할당하여 각 환경의 서로 다른 리소스에 [관리 제어를 위임](role-based-access-control-configure.md)합니다.
- 각 환경에 대한 리소스 그룹 및/또는 개별 리소스에 [태그를 할당](resource-group-using-tags.md)합니다. 리소스 그룹에 "환경" 태그를 추가하고 해당 태그의 값을 사용자의 환경 이름과 일치하도록 설정합니다. 태그는 청구 또는 관리에 대한 리소스를 구성해야 하는 경우 특히 유용할 수 있습니다.
- [Azure Preview 포털](https://portal.azure.com)의 리소스 그룹 리소스에 대한 경고 및 청구를 모니터링합니다.

## 추가 리소스

- Azure SDK 2.6이 설치된 상태에서 [Visual Studio에서 Azure 리소스 관리자 템플릿을 만들고 배포](http://msdn.microsoft.com/library/azure/Dn872471.aspx)합니다.
- [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs), [Visual Studio Code](http://www.visualstudio.com/products/code-vs) 또는 [Web Matrix](http://www.microsoft.com/web/webmatrix/)을 사용하여 응용 프로그램을 만듭니다.
- 만든 환경에 [웹앱을 배포](app-service-web/web-sites-deploy.md)합니다.
- [Visual Studio 릴리스 관리](http://msdn.microsoft.com/Library/vs/alm/Release/overview)를 사용하여 관리되는 지속적인 배포 파이프라인을 만들어 빠르고 쉽게 자주 해제합니다.
- [Azure Dev/Test Lab](http://azure.microsoft.com/campaigns/devtest-lab/)의 미리 보기에 대한 초대를 요청합니다. 이 초대를 통해 템플릿을 사용하여 Dev 및 Test Lab 환경을 관리하고, 조직 내에서 사용할 할당량 및 정책을 구성합니다.

<!---HONumber=AcomDC_1223_2015-->