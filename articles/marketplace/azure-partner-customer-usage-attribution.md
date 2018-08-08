---
title: Azure 파트너 및 고객 사용량 특성
description: Azure Marketplace 솔루션에 대한 고객 사용량을 추적하는 방법의 개요
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: ce862758d97737d16ef26ca7172cad39f8d8336a
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359535"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure 파트너 고객 사용량 특성

Azure 소프트웨어 파트너의 솔루션은 Azure 구성 요소가 필요하거나 Azure 인프라에 직접 배포해야 합니다.  오늘날, 고객이 파트너 솔루션을 배포하고 자체 Azure 리소스를 프로비전하는 경우 파트너가 배포 상태에 대한 가시성을 확보하고 Azure 증가에 미치는 영향을 파악하기가 어렵습니다. 더 높은 수준의 가시성을 추가하면 파트너가 Microsoft 영업 팀과 보조를 맞추고 Microsoft 파트너 프로그램에 대한 크레딧을 얻는 데 도움이 됩니다.   

Microsoft는 고객이 Azure에 파트너 소프트웨어를 배포한 결과인 Azure 사용량을 파트너가 더 정확하게 추적하는 데 도움이 되는 새로운 방법을 만들고 있습니다. 이 새로운 방법은 Azure Resource Manager를 사용하여 Azure 서비스 배포를 오케스트레이션하는 작업을 기반으로 합니다.

Microsoft 파트너는 고객을 대신하여 프로비전하는 Azure 리소스와 Azure 사용량을 연결할 수 있습니다.  이 연결 작업은 Azure Marketplace, 빠른 시작 리포지토리, 개인 github 리포지토리 및 일 대 일 고객 참여를 통해 수행할 수 있습니다.  추적을 설정하는 두 가지 방법이 있습니다.

- Azure Resource Manager 템플릿: 파트너 소프트웨어를 실행하기 위해 Azure 서비스를 배포할 Azure Resource Manager 템플릿 또는 솔루션 템플릿입니다. 파트너는 Azure 솔루션의 인프라와 구성을 정의하는 Azure Resource Manager 템플릿을 만들 수 있습니다. Azure Resource Manager 템플릿을 만들면 파트너와 고객이 수명 주기 내내 솔루션을 배포할 수 있으며, 리소스가 일관된 상태로 배포될 것을 확신할 수 있습니다. 

- Azure Resource Manager API: 파트너는 Azure Resource Manager API를 직접 호출하여 Azure Resource Manager 템플릿을 배포하거나, Azure 서비스를 직접 프로비전할 API 호출을 생성할 수 있습니다. 

## <a name="method-1-azure-resource-manager-templates"></a>방법 1: Azure Resource Manager 템플릿 

현재, 많은 파트너 솔루션은 Azure Resource Manager 템플릿을 사용하여 고객의 구독에 배포됩니다.  Azure Marketplace, GitHub 또는 빠른 시작에서 Azure Resource Manager 템플릿을 이미 사용할 수 있는 경우, 이 새로운 추적 방법을 사용하도록 템플릿을 수정하는 프로세스는 간단합니다.  현재 Azure Resource Manager 템플릿을 사용하지 않는 경우, Azure Resource Manager 템플릿 및 템플릿 생성 방법을 더 잘 이해하는 데 도움이 되는 몇 가지 링크는 다음과 같습니다. 

*   [첫 번째 Azure Resource Manager 템플릿을 만들고 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Azure Marketplace용 솔루션 템플릿 만들기 가이드](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>지침: 기존 Azure Resource Manager 템플릿에 GUID 추가

GUID 추가는 기본 템플릿 파일의 단일 수정 사항입니다.
 1. GUID를 만듭니다. 생성된 값이 eb7927c8-dd66-43e1-b0cf-c346a422063이라고 가정합니다.
 2. Azure Resource Manager 템플릿 열기
 3. 기본 템플릿 파일에 새 리소스를 추가합니다. 리소스는 mainTemplate.json 또는 azuredeploy.json에만 있으면 되고, 중첩된 템플릿이나 연결된 템플릿에는 없어도 됩니다.
 4. 위에 표시된 대로 “pid-” 뒤에 GUID를 입력합니다.

   다음 예제와 같이 표시됩니다. `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. 템플릿에서 오류 확인
 6. 적절한 리포지토리에 템플릿 다시 게시

## <a name="sample-template-code"></a>샘플 템플릿 코드

```

{ // add this resource to the mainTemplate.json (do not add the entire file)
      "apiVersion": "2018-02-01",
      "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your GUID here
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": []
        }
      }
    } // remove all comments from the file when done

```

## <a name="method-2-azure-resource-manager-apis"></a>방법 2: Azure Resource Manager API

파트너가 Azure Resource Manager REST API를 직접 호출하여 Azure 서비스를 배포하는 방법을 선호하는 경우도 있습니다. Azure는 이 기능을 사용하도록 설정하는 [여러 SDK를 지원](https://docs.microsoft.com/azure/#pivot=sdkstools)합니다.  SDK 중 하나를 사용하거나, REST API를 직접 호출하여 리소스를 배포할 수 있습니다.

Azure Resource Manager 템플릿을 사용하는 경우, 위의 지침에 따라 솔루션에 태그를 지정해야 합니다.  Azure Resource Manager 템플릿을 사용하지 않고 직접 API 호출을 수행하는 경우에도 배포에 태그를 지정하여 Azure 리소스 사용량을 연결할 수 있습니다. 

**Azure Resource Manager API를 사용하여 배포에 태그를 지정하는 방법:** 이 방법의 경우, API 호출을 디자인할 때 요청의 사용자 에이전트 헤더에 GUID를 포함합니다. 각 제품 또는 SKU에 대해 GUID를 추가해야 합니다.  문자열 형식은 접두사 pid-로 시작하고 파트너 생성 GUID를 포함해야 합니다.   

>[!Note] 
>사용자 에이전트에 삽입할 GUID 형식: pid-eb7927c8-dd66-43e1-b0cf-c346a422063     // “pid-” 뒤에 GUID를 입력합니다.

문자열의 형식이 중요합니다. “pid-” 접두사를 포함하지 않으면 데이터를 쿼리할 수 없습니다. 이 작업은 SDK마다 다르게 수행됩니다.  이 방법을 구현하려면 선호하는 Azure SDK에 대한 지원 및 접근 방법을 검토해야 합니다. 

**Python SDK를 사용한 예제:** Python의 경우, “config” 특성을 사용해야 합니다. UserAgent에만 추가할 수 있습니다. 다음은 예제입니다.

```python

client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
        client.config.add_user_agent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

>이 작업은 각 클라이언트에 대해 수행해야 하며, 전역 정적 구성이 없습니다. 클라이언트 팩터리 실행을 선택하여 모든 클라이언트가 이 작업을 수행하도록 할 수도 있습니다. 
>[추가 참조 정보](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Azure PowerShell 또는 Azure CLI를 사용하여 배포에 태그를 지정하는 방법: AzurePowerShell을 통해 리소스를 배포하는 경우, 다음 방법을 사용하여 GUID를 추가할 수 있습니다.

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Azure CLI를 사용할 때 GUID를 추가하려면 AZURE_HTTP_USER_AGENT 환경 변수를 설정합니다.  스크립트의 범위 내에서 이 변수를 설정할 수 있습니다. 또는 전체적으로 설정하려면 셸 범위에 대해 다음을 사용합니다.

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>GUID/제품 등록

추적에 GUID를 포함하려면 GUID를 등록해야 합니다.  

템플릿 GUID의 모든 등록은 Azure Marketplace CPP(Cloud 파트너 포털)를 통해 수행됩니다. 

지금 [Azure Marketplace](http://aka.ms/listonazuremarketplace)에 신청하여 Cloud 파트너 포털을 액세스하세요.

*   파트너는 [CPP에 프로필이 있어야](https://docs.microsoft.com/azure/marketplace/become-publisher) 하며, Azure Marketplace 또는 AppSource에 제품을 나열하는 것이 좋습니다. 
*   한 파트너가 여러 GUID를 등록할 수 있습니다. 
*   또한 파트너는 Marketplace 이외의 솔루션 템플릿/제품의 GUID를 등록할 수도 있습니다.

템플릿이나 사용자 에이전트에 GUID를 추가하고 CPP에 GUID를 등록하고 나면 모든 배포가 추적됩니다. 

## <a name="verification-of-guid-deployment"></a>GUID 배포 확인 

템플릿을 수정하고 테스트 배포를 수행한 후, 다음 PowerShell 스크립트를 사용하여 배포하고 태그를 지정한 리소스를 검색할 수 있습니다. 

Azure Resource Manager 템플릿에 GUID가 추가되었는지 확인하는 데 이 스크립트를 사용할 수 있습니다. Azure Resource Manager API 배포에는 적용되지 않습니다.

Azure에 로그인하고, 스크립트를 실행하기 전에 확인할 배포가 포함된 구독을 선택합니다. 배포의 구독 컨텍스트 내에서 실행해야 합니다.

배포의 GUID 및 resourceGroup 이름은 필수 매개 변수입니다.

[여기](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)서 원래 스크립트를 확인할 수 있습니다.

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>GUID 만들기 지침

GUID(Globally Unique Identifier)는 32개의 16진수로 이루어진 고유 참조 번호입니다. GUID를 만들려면 GUID 생성기를 사용하여 추적할 GUID를 만들어야 합니다.  사용할 수 있는 여러 [온라인 GUID 생성기](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E)가 있습니다.

모든 제품 및 배포 채널에 대해 고유 GUID를 만드는 것이 좋습니다.  예를 들어 두 개의 솔루션이 있고, 둘 다 템플릿을 통해 배포되며, Azure Marketplace 및 GitHub 둘 다에서 사용할 수 있습니다.  4개의 GUID를 만듭니다.

*   Azure Marketplace의 제품 A 
*   GitHub의 제품 A
*   Azure Marketplace의 제품 B 
*   GitHub의 제품 B

보고는 파트너(Microsoft 파트너 ID) 및 GUID별로 수행됩니다. 

SKU(SKU는 제품의 변형임) 등의 더 세분화된 수준에서 GUID를 추적하도록 선택할 수도 있습니다.

## <a name="guidance-on-privacy-and-data-collection"></a>개인 정보 보호 및 데이터 수집 지침

파트너는 Azure Resource Manager GUID 추적을 포함하는 배포에서 Microsoft가 해당 배포와 연결된 Azure 사용량을 해당 파트너에게 보고할 수 있도록 허용함을 고객에게 알리는 메시지를 제공해야 합니다.  몇 가지 예제 언어는 다음과 같습니다. "파트너"를 의미하는 부분에는 파트너사의 이름을 입력해야 합니다. 또한 파트너는 고객을 추적에서 제외할 수 있는 옵션을 포함하여 고유한 데이터 개인 정보 보호 및 수집 정책에 맞게 언어를 조정해야 합니다. 

**Azure Resource Manager 템플릿 배포의 경우**

이 템플릿을 배포할 때 Microsoft는 Azure 리소스가 배포된 파트너 소프트웨어 설치를 식별할 수 있습니다.  Microsoft는 소프트웨어를 지원하는 데 사용되는 Azure 리소스를 상호 연결할 수 있습니다.  Microsoft는 제품에 최상의 환경을 제공하고 비즈니스를 운영하기 위해 이 정보를 수집합니다. 이 데이터는 https://www.microsoft.com/trustcenter에서 확인할 수 있는 Microsoft 개인 정보 보호 정책에 따라 수집 및 규제됩니다. 

**SDK 또는 API 배포의 경우**

파트너 소프트웨어를 배포할 때 Microsoft는 Azure 리소스가 배포된 파트너 소프트웨어 설치를 식별할 수 있습니다.  Microsoft는 소프트웨어를 지원하는 데 사용되는 Azure 리소스를 상호 연결할 수 있습니다.  Microsoft는 제품에 최상의 환경을 제공하고 비즈니스를 운영하기 위해 이 정보를 수집합니다. 이 데이터는 https://www.microsoft.com/trustcenter에서 확인할 수 있는 Microsoft 개인 정보 보호 정책에 따라 수집 및 규제됩니다.

## <a name="support"></a>지원

지원을 받으려면 아래 단계를 따르세요.
 1. [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)에 있는 지원 페이지를 방문합니다.
 2. 사용량 연결 문제의 경우 - 문제 유형으로 **마켓플레이스 온보딩**, 범주로 **기타**를 선택하고 **요청 시작**을 클릭합니다. 
>[!Note]
>Azure Marketplace Cloud 파트너 포털 액세스 문제의 경우 - 문제 유형으로 **마켓플레이스 온보딩**, 범주로 **액세스 문제**를 선택하고 **요청 시작**을 클릭합니다.
 3. 다음 페이지에서 필수 필드를 작성하고 **계속**을 클릭합니다.
 4. 다음 페이지에서 자유 텍스트 필드를 작성합니다.  
 

 
>[!Important] 
>인시던트 제목에 **“ISV 사용량 추적”** 을 입력하고, 그 뒤에 있는 큰 자유 텍스트 필드에서 문제를 자세히 설명합니다.  양식의 나머지 부분을 작성하고 **제출**을 클릭합니다.

## <a name="faqs"></a>FAQ

**템플릿에 GUID를 추가하면 어떤 이점이 있나요?**

Microsoft는 고객의 템플릿 배포 및 영향을 받는 사용량에 대한 인사이트 보기를 파트너에게 제공합니다.  또한 Microsoft와 파트너는 이 정보를 사용하여 영업 팀 간에 긴밀한 관계를 구축할 수 있습니다. Microsoft와 파트너는 이 정보를 사용하여 개별 파트너가 Azure 성장에 미치는 영향에 대한 일관적인 보기를 얻을 수도 있습니다. 

**템플릿에 GUID를 추가할 수 있는 사람은 누구인가요?**

추적 리소스는 파트너 솔루션을 고객의 Azure 사용량에 연결하기 위한 것입니다.  사용량 데이터는 파트너의 MPN ID(Microsoft 파트너 네트워크 ID)에 연결되며, CPP(Cloud 파트너 포털)를 통해 파트너에게 보고가 제공될 예정입니다.  

**GUID가 추가된 후 변경할 수 있나요?**
 
예, 고객 또는 구현 파트너는 템플릿을 사용자 지정할 수 있으며 GUID를 변경하거나 제거할 수 있습니다. 파트너가 리소스 및 GUID의 역할을 사전에 고객과 파트너에게 설명하여 추적 GUID를 제거하거나 편집하지 못하게 하는 것이 좋습니다.  GUID를 변경할 경우 새 배포 및 리소스에만 영향을 주고, 기존 배포 및 리소스에는 영향을 주지 않습니다.

**언제 보고를 사용할 수 있나요?**

보고의 베타 버전이 곧 제공될 예정입니다.  보고는 CPP(Cloud 파트너 포털)에 통합됩니다.

**GitHub 등의 타사 리포지토리에서 배포된 템플릿을 추적할 수 있나요?**

예, 템플릿을 배포될 때 GUID가 있기만 하면 사용량이 추적됩니다.  
파트너가 Azure Marketplace 외부에서 게시된 관련 템플릿을 등록하려면 Cloud 파트너 포털에 프로필이 있어야 합니다. 

**템플릿이 Azure Marketplace에서 배포된 경우와 GitHub 등의 다른 리포지토리에서 배포된 경우 간에 차이가 있나요?**

예, Azure Marketplace에 제품을 게시하는 파트너는 Azure Marketplace의 배포에 대한 자세한 데이터를 받을 수 있습니다.  파트너는 Azure Marketplace 포털 및 Azure 관리 포털에서 고객에게 제품을 노출하는 기회를 활용할 수 있습니다. Azure Marketplace의 제품은 파트너의 잠재 고객도 생성합니다.

**개별 고객 참여를 위해 사용자 지정 템플릿을 만드는 경우에는 어떻게 해야 하나요?**

이 경우에도 템플릿에 GUID를 추가할 수 있습니다.  등록된 기존 GUID를 사용하면 보고에 포함됩니다.  새 GUID를 만드는 경우 추적에 포함되도록 새 GUID를 등록해야 합니다.

**고객도 보고를 받나요?**

고객은 현재 Azure 관리 포털 내에서 개별 리소스 또는 고객 정의 리소스 그룹의 사용량을 추적할 수 있습니다.   

**추적 방법론은 DPOR(디지털 공식 파트너)과 유사한가요?**

배포 및 사용량을 파트너 솔루션에 연결하는 이 새로운 방법은 파트너 솔루션을 Azure 사용량에 연결하는 메커니즘을 제공하기 위한 것입니다.  DPOR는 컨설팅(시스템 통합자) 또는 관리(관리 서비스 공급자) 파트너를 고객의 Azure 구독과 연결하기 위한 것입니다.   
