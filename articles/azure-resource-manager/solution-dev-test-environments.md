---
title: "개발 및 테스트 환경 | Microsoft Docs"
description: "Azure 리소스 관리자 템플릿을 사용하여 개발 및 테스트 환경을 빠르고 일관성 있게 만들고 삭제하는 방법에 대해 알아봅니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0d5c0ef4-3ba3-4db6-9e56-0440a7bf05d0
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0782000e87bed0d881be5238c1b91f89a970682c
ms.openlocfilehash: 01a554815a48cf1b81037eaa2f55bc4f07838721


---
# <a name="development-and-test-environments-in-microsoft-azure"></a>Microsoft Azure의 개발 및 테스트 환경
사용자 지정 응용 프로그램은 일반적으로 프로덕션 환경으로 배포하기 전에 여러 개발 및 테스트 환경에 배포됩니다. 온-프레미스 환경을 만들면 각 응용 프로그램의 각 환경에 대해 컴퓨팅 리소스가 조달 또는 할당됩니다. 환경에는 흔히 수동으로 또는 복잡한 자동화 스크립트를 사용하여 배포된 특정 구성을 가진 여러 물리적 또는 가상 컴퓨터가 포함됩니다. 배포는 흔히 시간이 오래 걸리고 환경에 걸쳐 일관되지 않은 구성을 초래합니다.

## <a name="scenario"></a>시나리오
Microsoft Azure에서 개발 및 테스트 환경을 프로비전하는 경우 사용하는 리소스에 대해서만 지불합니다.  이 문서에서는 아래 그림과 같이 Azure 리소스 관리자 템플릿과 매개 변수 파일을 사용하여 개발 및 테스트 환경을 빠르고 일관성 있게 만들고, 유지관리 및 삭제하는 방법을 설명합니다.

![시나리오](./media/solution-dev-test-environments/scenario.png)

위 그림에 세 가지 개발 및 테스트 환경이 나와 있습니다.  각각 템플릿 파일에 지정된 웹 응용 프로그램과 SQL 데이터베이스 리소스가 있습니다.  응용 프로그램과 데이터베이스 이름은 환경마다 다르며 고유의 매개 변수 파일에서 지정됩니다.  

Azure Resource Manager 개념에 익숙하지 않은 경우 이 문서를 읽기 전에 [Azure Resource Manager 개요](resource-group-overview.md) 를 읽는 것이 좋습니다.

Azure 리소스 관리자 템플릿을 사용하여 어느 정도 경험을 빨리 얻기 위해 참조 문서를 읽지 않고 먼저 이 문서의 단계를 나열된 순서대로 수행하는 것이 좋습니다. 단계를 한 번 완료한 후 단계를 더 연습하면서 참조 문서를 읽으면 처음에 갖게 되었던 대부분의 질문에 대한 답을 얻을 수 있습니다.

## <a name="plan-azure-resource-use"></a>Azure 리소스 사용 계획
응용 프로그램에 대한 높은 수준의 디자인을 설정한 후 다음 사항을 정의할 수 있습니다.

* 사용자의 응용 프로그램에 포함될 Azure 리소스는 다음을 포함합니다. 응용 프로그램을 빌드하고 Azure SQL 데이터베이스를 사용하여 Azure 웹 앱으로 배포할 수 있습니다.  PHP 및 MySQL 또는 IIS 및 SQL Server 또는 다른 구성 요소를 사용하여 가상 컴퓨터에서 응용 프로그램을 빌드할 수 있습니다. [Azure App Service, Cloud Services 및 Virtual Machines 비교](../app-service-web/choose-web-site-cloud-service-vm.md) 문서는 사용자의 응용 프로그램에 이용할 수 있는 Azure 리소스를 결정하는 데 도움이 됩니다.
* 사용자의 응용 프로그램에 맞는 가용성, 보안 및 규모 등과 같은 서비스 수준 요구 사항.

## <a name="download-an-existing-template"></a>기존 템플릿 다운로드
Azure 리소스 관리자 템플릿은 사용자의 응용 프로그램이 이용하는 모든 Azure 리소스를 정의합니다. Azure Preview 포털에서 직접 배포하거나 사용자의 응용 프로그램 코드를 사용하여 소스 제어 시스템에서 다운로드, 수정 및 저장할 수 있는 여러 템플릿이 이미 있습니다.  아래의 단계를 완료하여 기존 템플릿을 다운로드합니다.

1. [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/) GitHub 리포지토리에서 기존 템플릿을 탐색합니다. 목록에 "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)" 폴더가 나타납니다. 많은 사용자 지정 응용 프로그램은 웹 응용 프로그램 및 SQL 데이터베이스를 포함하고 있으므로, 사용자가 템플릿 사용 방법을 이해할 수 있도록 이 템플릿을 이 문서의 나머지 부분에서 예제로 사용합니다. 이 템플릿으로 만들고 구성하는 모든 것을 완전하게 설명하는 것은 이 기사의 범위를 벗어나지만, 사용자의 조직에서 이 템플릿을 사용하여 실제 환경을 만들려는 경우 [SQL 데이터베이스를 사용하여 웹 앱 프로비전](../app-service-web/app-service-web-arm-with-sql-database-provision.md) 문서를 읽고 완전히 이해하는 것이 좋습니다.
   참고: 이 문서는 [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) 템플릿 2015년 12월 버전을 위해 작성되었습니다. 그 시점 이하의 템플릿 및 매개 변수 파일에 대한 링크는 그 템플릿 버전에 대한 링크입니다.
2. 201-web-app-sql-database 폴더에서 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) 파일을 클릭하여 내용을 봅니다. 이 파일은 Azure 리소스 관리자 템플릿 파일입니다. 
3. 보기 모드에서 "[원시](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)" 단추를 클릭합니다. 
4. 마우스로 이 파일의 내용을 선택하고 "TestApp1-Template.json"이라는 이름의 파일로 컴퓨터에 저장합니다. 
5. 템플릿 내용을 검토하여 다음을 확인합니다.
   * **리소스** 섹션: 이 섹션은 해당 템플릿에서 만든 Azure 리소스의 유형을 정의합니다. 다른 리소스 유형 중에서도 이 템플릿은 [Azure Web App](../app-service-web/app-service-web-overview.md) 및 [Azure SQL Database](../sql-database/sql-database-technical-overview.md) 리소스를 만듭니다. 가상 컴퓨터에서 웹 및 SQL Server를 실행 및 관리하려는 경우 "[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" 또는 "[lamp-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)" 템플릿을 사용할 수 있지만 이 글의 지침은 [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) 템플릿에 기초하고 있습니다.
   * **매개 변수** 섹션: 이 섹션에서는 각 리소스를 구성할 수 있는 매개 변수를 정의합니다. 서식 파일에 지정한 일부 매개 변수는 "defaultValue" 속성을 가지고 있는 반면, 다른 매개 변수는 이 속성을 가지고 있지 않습니다. 템플릿 사용하여 Azure 리소스를 배포할 때는 템플릿에서 지정한 defaultValue 속성을 갖고 있지 않은 모든 매개 변수에 값을 제공해야 합니다.  DefaultValue 속성을 가지고 있는 매개 변수에 대해 값을 제공하지 않으면 템플릿의 defaultValue 매개 변수에 대해 지정된 값이 사용 됩니다.

템플릿은 만들어진 Azure 리소스 및 각 리소스가 구성할 수 있는 매개 변수를 정의합니다. [Azure 리소스 관리자 템플릿 디자인 모범 사례](best-practices-resource-manager-design-templates.md) 문서를 읽으면 템플릿 및 직접 디자인하는 방법을 더 자세히 배울 수 있습니다.

## <a name="download-and-customize-an-existing-parameter-file"></a>기존 매개 변수 다운로드 및 사용자 지정
각 환경에서 *동일한* Azure 리소스를 만들고자 할 수도 있고, 각 환경에서 리소스를 서로 *다르게* 구성하고자 할 수도 있습니다.  이 리소스는 매개 변수 파일 들어오는 장소입니다. 다음 단계를 통해 각 환경마다 고유 값을 포함하는 매개 변수 파일을 만듭니다.   

1. 201-web-app-sql-database 폴더에서 [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) 파일의 내용을 봅니다. 이 파일은 이전 섹션에서 저장한 템플릿 파일에 대한 매개 변수 파일입니다. 
2. 보기 모드에서 "[원시](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)" 단추를 클릭합니다. 
3. 마우스로 이 파일의 내용을 선택하고 다음과 같은 이름으로 컴퓨터의 서로 다른 파일 3개에 저장합니다.
   
   * TestApp1-Parameters-Development.json
   * TestApp1-Parameters-Test.json
   * TestApp1-Parameters-Pre-Production.json
4. 텍스트나 JSON 편집기를 사용하여 3단계에서 만든 개발 환경 매개 변수 파일을 편집합니다. 아래 **매개 변수**의 오른쪽에 열거된 *값*으로 파일의 매개 변수 값 오른쪽의 값을 대체합니다. 
   
   * **siteName**: *TestApp1DevApp*
   * **hostingPlanName**: *TestApp1DevPlan*
   * **siteLocation**: *Central US*
   * **serverName**: *testapp1devsrv*
   * **serverLocation**: *Central US*
   * **administratorLogin**: *testapp1Admin*
   * **administratorLoginPassword**: *해당 암호로 대체*
   * **databaseName**: *testapp1devdb*
5. 텍스트나 JSON 편집기를 사용하여 3단계에서 만든 테스트 환경 매개 변수 파일을 편집합니다. 아래 **매개 변수**의 오른쪽에 열거된 *값*으로 파일의 매개 변수 값 오른쪽의 값을 대체합니다.
   
   * **siteName**: *TestApp1TestApp*
   * **hostingPlanName**: *TestApp1TestPlan*
   * **siteLocation**: *Central US*
   * **serverName**: *testapp1testsrv*
   * **serverLocation**: *Central US*
   * **administratorLogin**: *testapp1Admin*
   * **administratorLoginPassword**: *해당 암호로 대체*
   * **databaseName**: *testapp1testdb*
6. 텍스트 또는 JSON 편집기를 사용하여 3단계에서 만든 사전 프러덕션 매개 변수 파일을 편집합니다.  파일의 전체 내용을 아래 내용으로 바꿉니다.
   
        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
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

프로덕션 전 환경에서 이 매개 변수에 대해 기본값이 아닌 값이 사용되는 이유는 이 매개 변수에 대해 역시 테스트 가능한 프로덕션 환경에 선호할 수 있는 값을 테스트하기 위해서입니다.  이러한 매개 변수는 모두 Azure [Web App 호스팅 계획](https://azure.microsoft.com/pricing/details/app-service/) 또는 **sku** 및 Azure [SQL Database](https://azure.microsoft.com/pricing/details/sql-database/), 또는 응용 프로그램에서 사용하는 **requestedServiceObjectiveName**과 관계가 있습니다.  sku 및 서비스 대상 이름이 목표 이름이 서로 다르면 비용 및 기능이 서로 다르며 서로 다른 서비스 수준 메트릭을 지원합니다.

아래 표는 템플릿에 지정된 이 매개 변수에 대한 기본값 및 프로덕션 전 매개 변수 파일의 기본값 대신 사용되는 값을 나열합니다.

| 매개 변수 | 기본값 | 매개 변수 파일  |
| --- | --- | --- |
| **sku** |무료 |Standard |
| **requestedServiceObjectiveName** |S0 |S1 |

## <a name="create-environments"></a>환경 만들기
모든 Azure 리소스는 [Azure 리소스 그룹](resource-group-overview.md) 내에 만들어야 합니다. 리소스 그룹을 사용하여 Azure 리소스를 모아서 관리할 수 있도록 그룹화 할 수 있습니다.  [권한](../active-directory/role-based-access-built-in-roles.md) 을 할당할 수 있습니다.  리소스 그룹의 리소스에 대한 경고 및 청구 정보는 [Azure 포털](https://portal.azure.com)에서 볼 수 있습니다. 리소스 그룹은 Azure [지역](https://azure.microsoft.com/regions/)에 만들어집니다.  이 문서에서 모든 리소스는 미국 중부 지역에 만들어집니다. 실제 환경을 만들기 시작할 때 사용자의 요구 사항에 가장 알맞은 지역을 선택합니다. 

다음 방법 중 하나를 사용하여 각 환경에 대한 리소스 그룹을 만듭니다.  모든 방법이 정확히 같은 결과를 달성합니다.

### <a name="azure-command-line-interface-cli"></a>Azure CLI(명령줄 인터페이스)
Windows, OS X 또는 Linux 컴퓨터에 CLI를 [설치했으며](../xplat-cli-install.md), Azure 구독에 [Azure AD 계정](../active-directory/active-directory-how-subscriptions-associated-directory.md)(회사 또는 학교 계정)을 [연결](../xplat-cli-connect.md)했는지 확인합니다. CLI 명령줄에서 개발 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

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

### <a name="powershell"></a>PowerShell
[Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 문서에 자세히 설명한 대로 Windows 컴퓨터에 Azure PowerShell 1.01 이상을 설치하고 [Azure AD 계정](../active-directory/active-directory-how-subscriptions-associated-directory.md)(회사 또는 학교 계정)을 구독에 연결했는지 확인합니다. PowerShell 명령 프롬프트에서 개발 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

명령이 성공하면 다음 결과를 반환합니다.

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

테스트 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

프로덕션 전 환경에 대한 리소스 그룹을 만들려면 아래 명령을 입력합니다.

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

### <a name="azure-portal"></a>Azure 포털
1. [Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md)(회사 또는 학교 계정)로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 아래 그림처럼 새로 만들기-->관리-->리소스 그룹을 클릭하고 리소스 그룹 이름 상자에 "TestApp1-Development"를 입력한 다음 구독을 선택하고 리소스 그룹 위치 상자에서 "Central US"를 선택합니다.
   ![포털](./media/solution-dev-test-environments/rgcreate.png)
2. 만들기 단추를 클릭하여 리소스 그룹을 만듭니다.
3. 아래와 같이 찾아보기를 클릭하고 목록에서 리소스 그룹까지 아래쪽으로 스크롤하여 리소스 그룹을 클릭합니다.
   ![포털](./media/solution-dev-test-environments/rgbrowse.png) 
4. 리소스 그룹을 클릭하면 새 리소스 그룹이 리소스 그룹 블레이드에 표시됩니다.
   ![포털](./media/solution-dev-test-environments/rgview.png)
5. 위의 TestApp1-Development 리소스 그룹과 동일한 방법으로 TestApp1-Test 및 TestApp1-Pre-Production 리소스 그룹을 만듭니다.

## <a name="deploy-resources-to-environments"></a>환경에 리소스 배포
아래 방법 중 하나를 수행하여 각각의 솔루션용 템플릿 파일 및 각 환경용 매개 변수 파일을 사용하여 Azure 리소스를 각 환경에 대한 리소스 그룹에 배포합니다.  두 방법 모두 결과는 정확히 같습니다.

### <a name="azure-command-line-interface-cli"></a>Azure CLI(명령줄 인터페이스)
CLI 명령줄에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 개발 환경에 대해 만든 리소스 그룹에 배포합니다.

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

몇 분 동안 "배포 완료를 기다리는 중
" 메시지가 표시된 뒤 명령이 성공하면 다음 결과를 반환합니다.

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
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

명령이 성공하지 못하면, 모든 오류 메시지를 해결하고 다시 시도하십시오.  일반적인 문제는 Azure 리소스 명명 제약 조건을 준수하지 않는 매개 변수 값을 사용하는 것입니다. 다른 문제 해결 팁은 [Azure에서 리소스 그룹 배포 문제 해결](resource-manager-common-deployment-errors.md) 문서에서 찾을 수 있습니다.

CLI 명령줄에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 테스트 환경에 대해 만든 리소스 그룹에 배포합니다.

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

CLI 명령줄에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 프로덕션 전 환경에 대해 만든 리소스 그룹에 배포합니다.

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json

### <a name="powershell"></a>PowerShell
Azure PowerShell(버전 1.01 이상) 명령 프롬프트에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 개발 환경에 대해 만든 리소스 그룹에 배포합니다.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

몇 분 동안 커서가 깜박인 뒤 명령이 성공하면 다음 결과를 반환합니다.

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        

    Outputs           :

  명령이 성공하지 못하면, 모든 오류 메시지를 해결하고 다시 시도하십시오.  일반적인 문제는 Azure 리소스 명명 제약 조건을 준수하지 않는 매개 변수 값을 사용하는 것입니다. 다른 문제 해결 팁은 [Azure에서 리소스 그룹 배포 문제 해결](resource-manager-common-deployment-errors.md) 문서에서 찾을 수 있습니다.

  PowerShell 명령프롬프트에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 테스트 환경에 대해 만든 리소스 그룹에 배포합니다.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  PowerShell 명령프롬프트에서 [path]를 이전 단계에서 저장한 파일에 대한 경로로 바꾸고 아래 명령을 입력하여 리소스를 프로덕션 전 환경에 대해 만든 리소스 그룹에 배포합니다.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

소스 제어 시스템의 응용 프로그램 코드를 사용하여 템플릿 및 매개 변수 파일을 버전 지정하고 유지 관리할 수 있습니다.  또한 위의 명령을 스크립트 파일에 저장하고 사용자의 코드도 함께 저장할 수 있습니다.

> [!NOTE]
> [SQL 데이터베이스를 사용하여 웹 앱 프로비전](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) 의 "Azure에 배포" 단추를 클릭하여 Azure에 템플릿을 배포할 수 있습니다.  이 작업이 템플릿에 관하여 배우는 데 유용할 수 있지만 그럴 경우 사용자의 응용 프로그램 코드를 사용하여 템플릿 및 매개 변수 값을 편집, 버전 관리 및 저장할 수 없으므로 이 문서에서는 더 이상 이 방법을 상세히 다루지 않습니다.
> 
> 

## <a name="maintain-environments"></a>환경 유지 관리
개발 과정 전체에 걸쳐 서로 다른 환경에서 Azure 리소스의 구성이 의도적으로 또는 실수로 일관성 없이 변경될 수 있습니다.  이 때문에 응용 프로그램 개발 주기에 불필요한 문제 해결을 야기할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)을 열어 환경을 변경합니다. 
2. 위의 단계를 완료하는 데 사용한 것과 동일한 계정을 사용하여 로그인합니다. 
3. 아래 그림과 같이 찾아보기-->리소스 그룹을 클릭합니다(리소스 그룹을 보려면 아래로 스크롤해야 할 수 있음).
   ![포털](./media/solution-dev-test-environments/rgbrowse.png)
4. 위 그림에서 리소스 그룹을 클릭하면 리소스 그룹 블레이드와 앞의 단계에서 만든 3개의 리소스 그룹이 아래 그림처럼 나타납니다. TestApp1-Development 리소스 그룹을 클릭하면 이전 단계에서 완료한 TestApp1-Development 리소스 그룹 배포에서 템플릿으로 만든 리소스를 나열하는 블레이드가 나타납니다.  TestApp1-Development 리소스 그룹 블레이드에서 TestApp1DevApp을 클릭한 다음 TestApp1DevApp 웹앱 블레이드에서 삭제를 클릭하여 TestApp1DevApp 웹앱 리소스를 삭제합니다.
   ![포털](./media/solution-dev-test-environments/portal2.png)
5. 리소스를 삭제할지 여부에 관하여 묻는 포털 프롬프트가 표시될 때 "예"를 클릭합니다. TestApp1-Development 리소스 그룹 블레이드를 닫았다가 다시 열면 방금 삭제한 웹 앱이 이제 표시되지 않습니다.  리소스 그룹의 내용은 이제 예상과 다릅니다. 여러 리소스 그룹에서 여러 리소스를 삭제하거나 심지어 일부 리소스에 대한 구성 설정을 변경하여 좀더 실험해 볼 수 있습니다. Azure 포털을 사용하여 리소스 그룹에서 리소스를 삭제하는 대신 CLI에서 [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) 명령 또는 "azure resource delete" 명령을 사용하여 같은 태스크를 달성할 수 있습니다.
6. 리소스 그룹에서 있게 될 모든 리소스와 구성을 가져오려면 적용되는 상태로 돌아가 [환경에 리소스 배포](#deploy-resources-to-environments) 에서 사용한 것과 동일한 명령을 사용하여(단 "Deployment1"을 "Deployment2"로 대체) 리소스 그룹에 환경을 다시 배포합니다.
7. 4단계 그림의 TestApp1-Development 블레이드 요약 섹션에서 보듯이, 이전 단계를 통해 포털에서 삭제한 웹 앱이 다시 표시됩니다. 다른 리소스를 삭제했어도 마찬가지입니다. 리소스의 구성을 변경한 경우 해당 값도 매개 변수 파일의 값으로 복원된 것을 확인할 수 있습니다. Azure 리소스 관리자 템플릿을 사용하여 환경을 배포하는 방법의 이점 중 하나는 환경을 언제든지 알려진 상태로 되돌려 쉽게 다시 배포할 수 있다는 것입니다.
8. 아래 그림에서 "마지막 배포" 아래에 있는 텍스트를 클릭하면 리소스 그룹에 대한 배포 기록을 보여 주는 블레이드가 표시됩니다.  첫 번째 배포에 이름 "Deployment1"을 사용하고 두 번째 배포에 "Deployment2"를 사용했으므로 항목이 두 개입니다.  배포를 클릭하면 각 배포에 대한 결과 보여주는 블레이드가 표시됩니다.
   ![포털](./media/solution-dev-test-environments/portal3.png)

## <a name="delete-environments"></a>환경 삭제
환경을 통한 작업을 마친 후 더 이상 사용하지 않는 Azure 리소스에 대한 사용량 수수료가 발생하지 않도록 해당 리소스를 삭제하는 것이 좋습니다.  환경을 삭제하는 것은 환경을 만드는 것보다 더 쉽습니다.  이전 단계에서는 각 환경마다 개별 Azure 리소스 그룹을 만든 다음 리소스를 리소스 그룹에 배포했습니다. 

아래 방법 중 하나를 사용하여 환경을 삭제합니다.  모든 방법이 정확히 같은 결과를 달성합니다.

### <a name="azure-cli"></a>Azure CLI
CLI 프롬프트에서 다음을 입력합니다.

    azure group delete "TestApp1-Development"

대화 상자가 나타나면 y를 입력하고 Enter를 눌러 개발 환경과 해당 리소스를 모두 제거합니다. 몇 분 후 명령이 다음 결과를 반환합니다.

    info:    group delete command OK

CLI 프롬프트에서 다음을 입력하여 남은 환경을 삭제합니다.

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"

### <a name="powershell"></a>PowerShell
Azure PowerShell(버전 1.01 이상) 명령 프롬프트에서 아래 명령을 입력하여 리소스 그룹과 모든 해당 내용을 삭제합니다.    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

리소스 그룹을 제거할 것인지 묻는 대화 상자가 나타나면 y와 Enter 키를 차례로 입력합니다.

다음을 입력하여 남은 환경을 삭제합니다.

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Azure 포털
1. Azure 포털에서 이전 단계에서와 같이 방법으로 리소스 그룹을 찾습니다. 
2. TestApp1-Development 리소스 그룹을 선택한 다음 TestApp1-Development 리소스 그룹 블레이드에서 삭제를 클릭합니다. 새 블레이드가 표시됩니다. 리소스 그룹 이름을 입력하고 삭제 단추를 클릭합니다.
   ![포털](./media/solution-dev-test-environments/rgdelete.png)
3. TestApp1-Development 리소스 그룹과 동일한 방법으로 TestApp1-Test 및 TestApp1-Pre-Production 리소스 그룹을 삭제합니다.

사용하는 방법에 상관없이 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 더 이상 존재하지 않으며 리소스에 대한 비용 청구가 더 이상 발생하지 않습니다.  

응용 프로그램 개발 중에 발생하는 Azure 리소스 사용률 비용을 최소화하기 위해 [Azure 자동화](../automation/automation-intro.md) 를 사용하여 다음과 같은 작업을 예약할 수 있습니다.

* 각 날짜가 끝날 때 가상 컴퓨터를 중지하고 각 날짜가 시작할 때 다시 시작합니다.
* 각 날짜가 끝날 때 전체 환경을 삭제하고 각 날짜가 시작할 때 다시 만듭니다.

이제 개발 및 테스트 환경을 쉽게 만들고 유지 관리 및 삭제하는 방법을 경험했으므로 위 단계를 더 실험하고 이 문서에 포함된 참조 자료를 읽어서 방금 했던 작업에 관하여 더 자세히 배울 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [관리 제어를 위임](../active-directory/role-based-access-control-configure.md) 합니다.
* [태그를 할당](resource-group-using-tags.md) 합니다. 리소스 그룹에 "환경" 태그를 추가하고 해당 태그의 값을 사용자의 환경 이름과 일치하도록 설정합니다. 태그는 청구 또는 관리에 대한 리소스를 구성해야 하는 경우 특히 유용할 수 있습니다.
* [Azure 포털](https://portal.azure.com)의 리소스 그룹 리소스에 대한 경고 및 청구를 모니터링합니다.




<!--HONumber=Jan17_HO2-->


