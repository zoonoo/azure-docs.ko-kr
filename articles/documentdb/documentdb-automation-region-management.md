---
title: "DocumentDB 자동화 - 하위 지역 관리 | Microsoft 문서"
description: "Azure CLI와 Azure Resource Manager를 사용하여 DocumentDB 데이터베이스 계정의 하위 지역을 관리합니다. DocumentDB는 JSON 데이터에 대한 클라우드 기반 NoSQL 데이터베이스입니다."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 7f765c17-8549-4108-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 0782000e87bed0d881be5238c1b91f89a970682c
ms.openlocfilehash: cca2c112924c22846d5a00e0a94181669fb4cbc0


---
# <a name="automate-documentdb-account-region-management-using-azure-cli-and-azure-resource-manager-templates"></a>Azure CLI와 Azure Resource Manager 템플릿을 사용하여 DocumentDB 계정 하위 지역 관리 자동화

이 문서에서는 Azure CLI 명령과 Azure Resource Manager 템플릿을 사용하여 Azure DocumentDB 계정에서 하위 지역을 추가/제거하는 방법을 보여 줍니다. 또한 하위 지역 관리는 [Azure Portal](documentdb-portal-global-replication.md)을 통해 수행할 수도 있습니다. 다음 자습서의 명령을 사용하면 다양한 하위 지역의 장애 조치 우선 순위를 변경할 수 없습니다. 읽기 하위 지역만 추가하거나 제거할 수 있습니다. 데이터베이스 계정의 쓰기 하위 지역(장애 조치 우선 순위 0)은 추가하거나 제거할 수 없습니다.

DocumentDB 데이터베이스 계정은 현재 [Azure Resource Manager 템플릿 및 Azure CLI](documentdb-automation-resource-manager-cli.md)를 사용하여 만들고 수정할 수 있는 유일한 DocumentDB 리소스입니다.

## <a name="getting-ready"></a>준비

Azure 리소스 그룹에서 Azure CLI를 사용하려면 올바른 Azure CLI 버전 및 Azure 계정이 있어야 합니다. Azure CLI가 없으면 [설치](../xplat-cli-install.md)하십시오.

### <a name="update-your-azure-cli-version"></a>Azure CLI 버전 업데이트

명령 프롬프트에서 `azure --version`을 입력하면 0.10.4 이상을 이미 설치했는지 여부를 확인할 수 있습니다. 이 단계에서 Microsoft Azure CLI 데이터 수집에 참여하라는 메시지가 표시될 수 있습니다. 그러면 y를 선택하여 옵트인하거나 n을 선택하여 옵트아웃할 수 있습니다.

    azure --version
    0.10.4 (node: 4.2.4)

버전이 0.10.4 미만인 경우 기본 설치 관리자 중 하나를 사용하여 [Azure CLI를 설치](../xplat-cli-install.md) 또는 업데이트하거나 **npm**을 통해 `npm update -g azure-cli`을 입력하여 업데이트하거나 `npm install -g azure-cli`를 입력하여 설치해야 합니다.

### <a name="set-your-azure-account-and-subscription"></a>Azure 계정 및 구독 설정

Azure 구독이 아직 없지만 Visual Studio 구독이 있는 경우 [Visual Studio 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화할 수 있습니다. 또는 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

Azure 리소스 관리 템플릿을 사용하려면 회사 또는 학교 계정 또는 Microsoft 계정 ID가 있어야 합니다. 이러한 계정 중 하나 있는 경우 다음 명령을 입력합니다.

    azure login

다음과 같은 출력을 생성합니다. 

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Azure 계정이 없는 경우 다른 유형의 계정이 필요하다는 오류 메시지가 표시됩니다. 현재 Azure 계정에서 계정을 만들려면 [Azure Active Directory에서 회사 또는 학교 ID 만들기](../virtual-machines/virtual-machines-windows-create-aad-work-id.md)를 참조하세요.

브라우저에서 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 을 열고 명령 출력에 제공된 코드를 입력합니다.

![Microsoft Azure CLI에 대한 장치 로그인 화면을 보여 주는 스크린 샷](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

코드를 입력한 후 브라우저에서 사용할 ID를 선택하고 사용자 이름 및 필요한 경우 암호를 제공합니다.

![사용하려는 Azure 구독과 연결된 Microsoft ID 계정을 선택하는 위치를 보여 주는 스크린 샷](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

성공적으로 로그인하면 다음과 같은 확인 화면이 표시되며, 이때 브라우저 창을 닫을 수 있습니다.

![Microsoft Azure 플랫폼 간 명령줄 인터페이스에 로그인에 대한 확인을 보여 주는 스크린 샷](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

또한 명령 셸은 다음과 같은 출력을 제공합니다.

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

여기서 설명한 대화형 로그인 방법 외에 사용 가능한 추가 Azure CLI 로그인 방법이 있습니다. 다른 방법에 대한 자세한 내용 및 여러 구독 처리에 대한 자세한 내용은 [Azure 명령줄 인터페이스(Azure CLI)에서 Azure 구독에 연결](../xplat-cli-connect.md)을 참조하세요.

### <a name="switch-to-the-azure-cli-resource-group-mode"></a>Azure CLI 리소스 그룹 모드로 전환

기본적으로 Azure CLI는 서비스 관리 모드(**asm** 모드)로 시작됩니다. 다음을 입력하여 리소스 그룹 모드로 전환합니다.

    azure config mode arm

다음과 같은 출력을 제공합니다.

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

필요한 경우 `azure config mode asm`을 입력하여 기본 명령 집합으로 다시 전환할 수 있습니다.

### <a name="create-or-retrieve-your-resource-group"></a>리소스 그룹 만들기 또는 검색

DocumentDB 계정을 만들려면 먼저 리소스 그룹이 필요합니다. 사용할 리소스 그룹의 이름을 이미 알고 있는 경우 [2단계](#create-documentdb-account-cli)로 건너뜁니다. 

현재 리소스 그룹의 모든 목록을 검토하려면 다음 명령을 실행하고 사용할 리소스 그룹 이름을 메모합니다. 

    azure group list

새 리소스 그룹을 만들려면 다음 명령을 실행하고 만들 새 리소스 그룹 및 리소스 그룹을 만들 지역의 이름을 지정합니다. 

    azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` 은 영숫자, 마침표, 밑줄, '-' 문자 및 괄호를 사용할 수 있고 마침표로 끝날 수 없습니다. 
 - `<resourcegrouplocation>` 은 DocumentDB를 일반적으로 사용할 수 있는 하위 지역 중 하나여야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.

예제 입력:

    azure group create new_res_group westus

다음과 같은 출력을 생성합니다.

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

오류가 발생하면 [문제 해결](#troubleshooting)을 참조하세요. 

## <a name="understanding-azure-resource-manager-templates-and-resource-groups"></a>Azure Resource Manager 템플릿 및 리소스 그룹 이해

대부분의 응용 프로그램은 다양한 리소스 유형(예: 하나 이상의 DocumentDB 계정, 저장소 계정, 네트워크 또는 콘텐츠 배달 네트워크)의 조합으로 구축되었습니다. 기본 Azure 서비스 관리 API 및 Azure 포털에서는 서비스 단위 접근 방식을 사용하여 이러한 항목을 나타냈습니다. 이 접근 방식에서는 하나의 논리적인 배포 단위가 아니라 개별적으로 각 서비스를 배포하고 관리(또는 이러한 작업을 수행하는 다른 도구를 찾아야 함)해야 합니다.

*Azure 리소스 관리자 템플릿* 을 사용하면 선언적 방식으로 이러한 다양한 리소스를 하나의 논리적 배포 단위로 배포하고 관리할 수 있습니다. 명령을 통해 차례로 배포할 항목을 Azure에 지시하는 대신 JSON 파일에서 전체 배포(모든 리소스와 관련된 구성 및 배포 매개 변수)를 설명하고 이러한 리소스를 하나의 그룹으로 배포하도록 Azure에 지시합니다.

Azure 리소스 그룹 및 기능에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)에서 확인할 수 있습니다. 템플릿 작성에 관심이 있다면 [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.


## <a name="a-idadd-region-documentdb-accountatask-add-region-to-a-documentdb-account"></a><a id="add-region-documentdb-account"></a>작업: DocumentDB 계정에 하위 지역 추가

DocumentDB는 여러 [Azure 지역](https://azure.microsoft.com/regions/#services)에 걸쳐 [전역적으로 데이터를 배포][distribute-globally]할 수 있는 기능이 있습니다. 이 섹션의 지침에서는 Azure CLI와 Resource Manager 템플릿을 사용하여 기존 DocumentDB 계정에 읽기 하위 지역을 추가하는 방법에 대해 설명합니다. Resource Manager 템플릿 사용 여부에 상관없이 Azure CLI를 사용하여 이 작업을 수행할 수 있습니다.

### <a name="a-idadd-region-documentdb-account-clia-add-region-to-a-documentdb-account-using-azure-cli-without-resource-manager-templates"></a><a id="add-region-documentdb-account-cli"></a> Resource Manager 템플릿을 사용하지 않고 Azure CLI를 통해 DocumentDB 계정에 하위 지역 추가

명령 프롬프트에서 아래 명령을 입력하여 새 리소스 그룹 또는 기존 리소스 그룹의 DocumentDB 계정에 하위 지역을 추가합니다. "locations" 배열은 추가될 새 하위 지역을 제외하고 DocumentDB 계정 내의 현재 하위 지역 구성을 반영해야 합니다. 아래 예제에서는 두 번째 하위 지역을 계정에 추가하는 명령을 보여 줍니다.

장애 조치 우선 순위 값은 기존 구성과 일치시킵니다. 하위 지역 중 하나에서 failoverPriority 값이 0이어야 하며, 이는 이 하위 지역이 [DocumentDB 계정의 쓰기 하위 지역][scaling-globally]으로 유지됨을 나타냅니다. 장애 조치 우선 순위 값은 위치 간에 고유해야 하며, 가장 큰 값이 총 하위 지역 수보다 작아야 합니다. 새 하위 지역은 "읽기" 하위 지역이며 0보다 큰 장애 조치 우선 순위 값을 가져야 합니다.

> [!TIP]
> Azure PowerShell 또는 Windows PowerShell에서 이 명령을 실행하는 경우 예기치 않은 토큰에 대한 오류가 발생합니다. 그 대신 Windows 명령 프롬프트에서 이 명령을 실행합니다.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<newdatabaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

 - `<resourcegroupname>` 은 영숫자, 마침표, 밑줄, '-' 문자 및 괄호를 사용할 수 있고 마침표로 끝날 수 없습니다.
 - `<resourcegrouplocation>`은 현재 리소스 그룹의 영역입니다.
 - `<ip-range-filter>` 지정된 데이터베이스 계정에 대해 허용된 클라이언트 IP 목록으로 포함할 IP 주소 집합 또는 IP 주소 범위를 CIDR 형식으로 지정합니다. IP 주소/범위는 쉼표로 구분하며 공백을 포함해서는 안 됩니다. 자세한 내용은 [DocumentDB 방화벽 지원](documentdb-firewall-support.md)을 참조하세요.
 - `<databaseaccountname>` 은 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다.
 - `<databaseaccountlocation>` 은 DocumentDB를 일반적으로 사용할 수 있는 하위 지역 중 하나여야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.
 - `<newdatabaseaccountlocation>`은 추가할 새 하위 지역이며. 일반적으로 DocumentDB를 사용할 수 있는 하위 지역 중 하나여야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.


"미국 동부" 하위 지역을 DocumentDB 계정의 읽기 하위 지역으로 추가하기 위한 입력 예제: 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

새 계정이 프로비전될 때 다음과 같은 출력을 생성합니다.

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

오류가 발생하면 [문제 해결](#troubleshooting)을 참조하세요. 

명령이 반환된 후 몇 분 동안 계정이 **만드는 중** 상태에 있다가 사용할 준비가 되면 **온라인**으로 바뀝니다. [Azure 포털](https://portal.azure.com)의 **DocumentDB 계정** 블레이드에서 계정의 상태를 확인할 수 있습니다.

### <a name="a-idadd-region-documentdb-account-cli-arma-add-region-to-a-documentdb-account-using-azure-cli-with-resource-manager-templates"></a><a id="add-region-documentdb-account-cli-arm"></a> Resource Manager 템플릿을 사용하여 Azure CLI를 통해 DocumentDB 계정에 하위 지역 추가

이 섹션의 지침에서는 Azure Resource Manager 템플릿과 선택적 매개 변수 파일(둘 다 JSON 파일임)을 사용하여 기존 DocumentDB 계정에 하위 지역을 추가하는 방법에 대해 설명합니다. 템플릿을 사용하면 원하는 내용을 정확히 기술하고 오류 없이 반복할 수 있습니다.

장애 조치 우선 순위 값은 기존 구성과 일치시킵니다. 하위 지역 중 하나에서 failoverPriority 값이 0이어야 하며, 이는 이 하위 지역이 [DocumentDB 계정의 쓰기 하위 지역][scaling-globally]으로 유지됨을 나타냅니다. 장애 조치 우선 순위 값은 위치 간에 고유해야 하며, 가장 큰 값이 총 하위 지역 수보다 작아야 합니다. 새 하위 지역은 "읽기" 하위 지역이며 0보다 큰 장애 조치 우선 순위 값을 가져야 합니다.

아래와 같이 현재 DocumentDB 하위 지역 구성과 일치하는 것과 유사한 로컬 템플릿 파일을 만듭니다. "locations" 배열은 추가될 새로운 하위 지역과 함께 데이터베이스 계정의 기존 하위 지역 모두를 포함해야 합니다. 파일 이름을 azuredeploy.json으로 지정합니다.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            },
            "newLocationName": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        },
                        {
                            "failoverPriority": 2,
                            "locationName": "[parameters('newLocationName')]"
                        }
                    ]
                }
            }
        ]
    }

위의 템플릿 파일은 이미 2개 하위 지역이 있는 DocumentDB 계정에 새 하위 지역을 추가하는 예제를 보여 줍니다.

명령줄에 매개 변수 값을 입력하거나 매개 변수 파일을 만들어 값을 지정할 수 있습니다.

매개 변수 파일을 만들려면 새 파일에 다음 내용을 복사하고 파일 이름을 azuredeploy.parameters.json으로 지정합니다. 명령 프롬프트에서 데이터베이스 계정 이름을 지정하려는 경우 이 파일을 만들지 않고 계속할 수 있습니다.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            },
            "newLocationName": {
                "value": "northeurope"
            }
        }
    }

azuredeploy.parameters.json 파일에서 `"databaseAccountName"`의 value 필드를 사용할 데이터베이스 이름으로 업데이트한 후 파일을 저장합니다. `"databaseAccountName"` 은 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다. `"locationName1"` 및 `"locationName2"`의 value 필드를 DocumentDB 계정이 있는 하위 지역으로 업데이트합니다. `"newLocationName"`의 value 필드를 추가할 하위 지역으로 업데이트합니다.

리소스 그룹에 DocumentDB 계정을 만들려면 다음 명령을 실행하고 템플릿 파일에 대한 경로, 매개 변수 파일 또는 매개 변수 값에 대한 경로, 배포할 리소스 그룹의 이름 및 배포 이름(-n은 선택 사항)을 제공합니다. 

매개 변수 파일을 사용하려면:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` 은 1단계에서 만든 azuredeploy.json 파일에 대한 경로입니다. 경로 이름에 공백이 있으면 이 매개 변수 앞뒤에 큰따옴표를 배치합니다.
 - `<PathToParameterFile>` 은 1단계에서 만든 azuredeploy.parameters.json 파일에 대한 경로입니다. 경로 이름에 공백이 있으면 이 매개 변수 앞뒤에 큰따옴표를 배치합니다.
 - `<resourcegroupname>` 은 DocumentDB 데이터베이스 계정을 추가할 기존 리소스 그룹의 이름입니다. 
 - `<deploymentname>` 은 배포의 선택적 이름입니다.

예제 입력: 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

또는 매개 변수 파일 없이 데이터베이스 계정 이름 매개 변수를 지정하려면 값에 대한 프롬프트가 대신 표시되며 이때 다음 명령을 입력합니다.

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

프롬프트 및 samplearmacct라는 데이터베이스 계정에 대한 항목을 표시하는 예제 입력:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

계정이 프로비전될 때 다음과 같은 정보가 표시됩니다. 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    data:    newLocationName      String  eastus
    data:    databaseAccountName  String  samplearmacct
    info:    group deployment create command OK

오류가 발생하면 [문제 해결](#troubleshooting)을 참조하세요.  

명령이 반환된 후 몇 분 동안 계정이 **만드는 중** 상태에 있다가 사용할 준비가 되면 **온라인**으로 바뀝니다. [Azure 포털](https://portal.azure.com)의 **DocumentDB 계정** 블레이드에서 계정의 상태를 확인할 수 있습니다.

## <a name="a-idremove-region-documentdb-accountatask-remove-region-from-a-documentdb-account"></a><a id="remove-region-documentdb-account"></a>작업: DocumentDB 계정에서 하위 지역 제거

DocumentDB는 여러 [Azure 지역](https://azure.microsoft.com/regions/#services)에 걸쳐 [전역적으로 데이터를 배포][distribute-globally]할 수 있는 기능이 있습니다. 이 섹션의 지침에서는 Azure CLI와 Resource Manager 템플릿을 사용하여 기존 DocumentDB 계정에서 하위 지역을 제거하는 방법에 대해 설명합니다. Resource Manager 템플릿 사용 여부에 상관없이 Azure CLI를 사용하여 이 작업을 수행할 수 있습니다.

### <a name="a-idremove-region-documentdb-account-clia-remove-region-to-a-documentdb-account-using-azure-cli-without-resource-manager-templates"></a><a id="remove-region-documentdb-account-cli"></a> Resource Manager 템플릿을 사용하지 않고 Azure CLI를 통해 DocumentDB 계정에서 하위 지역 제거

기존 DocumentDB 계정에서 하위 지역을 제거하려면 Azure CLI를 사용하여 아래 명령을 실행합니다. "locations" 배열은 하위 지역을 제거한 후에 남아 있어야 하는 하위 지역만 포함해야 합니다. **생략된 위치는 DocumentDB 계정에서 제거됩니다**. 명령 프롬프트 창에서 다음 명령을 입력합니다.

하위 지역 중 하나에서 failoverPriority 값이 0이어야 하며, 이는 이 하위 지역이 [DocumentDB 계정의 쓰기 하위 지역][scaling-globally]으로 유지됨을 나타냅니다. 장애 조치 우선 순위 값은 위치 간에 고유해야 하며, 가장 큰 값이 총 하위 지역 수보다 작아야 합니다. 

> [!TIP]
> Azure PowerShell 또는 Windows PowerShell에서 이 명령을 실행하는 경우 예기치 않은 토큰에 대한 오류가 발생합니다. 그 대신 Windows 명령 프롬프트에서 이 명령을 실행합니다.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

 - `<resourcegroupname>` 은 영숫자, 마침표, 밑줄, '-' 문자 및 괄호를 사용할 수 있고 마침표로 끝날 수 없습니다.
 - `<resourcegrouplocation>`은 현재 리소스 그룹의 영역입니다.
 - `<ip-range-filter>` 지정된 데이터베이스 계정에 대해 허용된 클라이언트 IP 목록으로 포함할 IP 주소 집합 또는 IP 주소 범위를 CIDR 형식으로 지정합니다. IP 주소/범위는 쉼표로 구분하며 공백을 포함해서는 안 됩니다. 자세한 내용은 [DocumentDB 방화벽 지원](documentdb-firewall-support.md)을 참조하세요.
 - `<databaseaccountname>` 은 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다.
 - `<databaseaccountlocation>` 은 DocumentDB를 일반적으로 사용할 수 있는 하위 지역 중 하나여야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.

예제 입력: 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

새 계정이 프로비전될 때 다음과 같은 출력을 생성합니다.

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

오류가 발생하면 [문제 해결](#troubleshooting)을 참조하세요. 

명령이 반환된 후 몇 분 동안 계정이 **업데이트 중** 상태에 있다가 사용할 준비가 되면 **온라인**으로 바뀝니다. [Azure 포털](https://portal.azure.com)의 **DocumentDB 계정** 블레이드에서 계정의 상태를 확인할 수 있습니다.

### <a name="a-idremove-region-documentdb-account-cli-arma-remove-region-from-a-documentdb-account-using-azure-cli-with-resource-manager-templates"></a><a id="remove-region-documentdb-account-cli-arm"></a> Resource Manager 템플릿을 사용하여 Azure CLI를 통해 DocumentDB 계정에서 하위 지역 제거

이 섹션의 지침에서는 Azure Resource Manager 템플릿과 선택적 매개 변수 파일(둘 다 JSON 파일임)을 사용하여 기존 DocumentDB 계정에서 하위 지역을 제거하는 방법에 대해 설명합니다. 템플릿을 사용하면 원하는 내용을 정확히 기술하고 오류 없이 반복할 수 있습니다.

하위 지역 중 하나에서 failoverPriority 값이 0이어야 하며, 이는 이 하위 지역이 [DocumentDB 계정의 쓰기 하위 지역][scaling-globally]으로 유지됨을 나타냅니다. 장애 조치 우선 순위 값은 위치 간에 고유해야 하며, 가장 큰 값이 총 하위 지역 수보다 작아야 합니다. 

아래와 같이 현재 DocumentDB 하위 지역 구성과 일치하는 것과 유사한 로컬 템플릿 파일을 만듭니다. "locations" 배열은 하위 지역을 제거한 후에 남아 있어야 하는 하위 지역만 포함해야 합니다. **생략된 위치는 DocumentDB 계정에서 제거됩니다**. 파일 이름을 azuredeploy.json으로 지정합니다.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

명령줄에 매개 변수 값을 입력하거나 매개 변수 파일을 만들어 값을 지정할 수 있습니다.

매개 변수 파일을 만들려면 새 파일에 다음 내용을 복사하고 파일 이름을 azuredeploy.parameters.json으로 지정합니다. 명령 프롬프트에서 데이터베이스 계정 이름을 지정하려는 경우 이 파일을 만들지 않고 계속할 수 있습니다. Azure Resource Manager 템플릿에 정의된 필수 매개 변수를 반드시 추가합니다.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

azuredeploy.parameters.json 파일에서 `"databaseAccountName"`의 value 필드를 사용할 데이터베이스 이름으로 업데이트한 후 파일을 저장합니다. `"databaseAccountName"` 은 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다. `"locationName1"`의 value 필드는 하위 지역을 제거한 후 DocumentDB 계정에 존재하게 할 하위 지역으로 업데이트합니다.

리소스 그룹에 DocumentDB 계정을 만들려면 다음 명령을 실행하고 템플릿 파일에 대한 경로, 매개 변수 파일 또는 매개 변수 값에 대한 경로, 배포할 리소스 그룹의 이름 및 배포 이름(-n은 선택 사항)을 제공합니다. 

매개 변수 파일을 사용하려면:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` 은 1단계에서 만든 azuredeploy.json 파일에 대한 경로입니다. 경로 이름에 공백이 있으면 이 매개 변수 앞뒤에 큰따옴표를 배치합니다.
 - `<PathToParameterFile>` 은 1단계에서 만든 azuredeploy.parameters.json 파일에 대한 경로입니다. 경로 이름에 공백이 있으면 이 매개 변수 앞뒤에 큰따옴표를 배치합니다.
 - `<resourcegroupname>` 은 DocumentDB 데이터베이스 계정을 추가할 기존 리소스 그룹의 이름입니다. 
 - `<deploymentname>` 은 배포의 선택적 이름입니다.

예제 입력: 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

또는 매개 변수 파일 없이 데이터베이스 계정 이름 매개 변수를 지정하려면 값에 대한 프롬프트가 대신 표시되며 이때 다음 명령을 입력합니다.

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

프롬프트 및 samplearmacct라는 데이터베이스 계정에 대한 항목을 표시하는 예제 입력:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

계정이 프로비전될 때 다음과 같은 정보가 표시됩니다. 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

오류가 발생하면 [문제 해결](#troubleshooting)을 참조하세요.  

명령이 반환된 후 몇 분 동안 계정이 **업데이트 중** 상태에 있다가 사용할 준비가 되면 **온라인**으로 바뀝니다. [Azure 포털](https://portal.azure.com)의 **DocumentDB 계정** 블레이드에서 계정의 상태를 확인할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

리소스 그룹 또는 데이터베이스 계정을 만드는 동안 `Deployment provisioning state was not successful` 과 같은 오류가 발생하면 몇 가지 문제 해결 옵션이 있습니다. 

> [!NOTE]
> 데이터베이스 계정 이름에 잘못된 문자를 제공하거나 DocumentDB를 사용할 수 없는 위치를 제공하면 배포 오류가 발생합니다. 데이터베이스 계정 이름은 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다. 모든 유효한 데이터베이스 계정 위치는 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 나열됩니다.

- 출력이 다음 `Error information has been recorded to C:\Users\wendy\.azure\azure.err`을 포함하는 경우 azure.err 파일에서 오류 정보를 검토합니다.

- 리소스 그룹에 대한 로그 파일에서 유용한 정보를 찾을 수 있습니다. 로그 파일을 보려면 다음 명령을 입력합니다.

        azure group log show <resourcegroupname> --last-deployment

    예제 입력:       azure group log show new_res_group --last-deployment

    그런 다음 [Azure에서 리소스 그룹 배포 문제 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md) 을 참조하세요.

- 오류 정보는 다음 스크린샷에 표시된 대로 Azure 포털에서도 사용할 수 있습니다. 오류 정보를 탐색하려면: Jumpbar에서 리소스 그룹을 클릭하고 오류가 있는 리소스 그룹을 선택한 다음 리소스 그룹 블레이드의 필수 항목 영역에서 마지막 배포 날짜를 클릭한 후 배포 기록 블레이드에서 실패한 배포를 선택한 다음 배포 블레이드에서 빨간색 느낌표가 있는 작업 세부 정보를 클릭합니다. 작업 세부 정보 블레이드에서 실패한 배포에 대한 상태 메시지가 표시됩니다. 

    ![배포 오류 메시지를 탐색하는 방법을 보여 주는 Azure 포털의 스크린샷](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png) 

## <a name="next-steps"></a>다음 단계

이제 DocumentDB 계정을 만들었으므로 다음 단계에서는 DocumentDB 데이터베이스를 만들게 됩니다. 다음 중 하나를 사용하여 데이터베이스를 만들 수 있습니다.

- [Azure Portal을 사용하여 DocumentDB 컬렉션 및 데이터베이스 만들기](documentdb-create-collection.md)에 설명된 Azure Portal
- GitHub에서 [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 리포지토리의 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 프로젝트에 있는 C# .NET 샘플.
- [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다. 

데이터베이스를 만든 후에 데이터베이스에 [하나 이상의 컬렉션을 추가](documentdb-create-collection.md)한 다음, 이 컬렉션에 [문서를 추가](documentdb-view-json-document-explorer.md)해야 합니다. 

컬렉션에 문서를 추가한 후에 포털의 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 중 하나를 사용하여 문서에 [쿼리를 실행](documentdb-sql-query.md#executing-sql-queries)하기 위해 [DocumentDB SQL](documentdb-sql-query.md)을 사용할 수 있습니다.

DocumentDB에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

-   [DocumentDB 학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-   [DocumentDB 리소스 모델 및 개념](documentdb-resources.md)

사용할 수 있는 더 많은 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet



<!--HONumber=Jan17_HO2-->


