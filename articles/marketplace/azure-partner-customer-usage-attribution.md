---
title: Azure 파트너 및 고객 사용량 특성
description: Azure Marketplace 솔루션에 대한 고객 사용량을 추적하는 방법의 개요
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: yijenj
ms.openlocfilehash: a0b3c220a1cd857bc8bea0eb5ab41625845fcc5d
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365628"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure 파트너 고객 사용량 특성

Azure 소프트웨어 파트너의 솔루션은 Azure 구성 요소가 필요하거나 Azure 인프라에 직접 배포해야 합니다. 파트너 솔루션을 배포하고 자체 Azure 리소스를 프로비전하는 고객은 배포 상태를 파악하기 어렵고, 이에 따라 Azure 성장에 미치는 영향에 광학을 사용할 수 있습니다. 높은 수준의 가시성을 추가하면 Microsoft 판매 팀과 제휴하여 Microsoft 파트너 프로그램에 대한 크레딧을 얻게 됩니다.   

Microsoft는 이제 파트너가 Azure에 자사 소프트웨어를 배포한 고객의 Azure 사용량을 더 효율적으로 추적할 수 있는 유용한 방법을 제공합니다. 이 새 방법에서는 Azure Resource Manager를 사용하여 Azure 서비스의 배포를 오케스트레이션합니다.

Microsoft 파트너는 고객을 대신하여 프로비전하는 Azure 리소스와 Azure 사용량을 연결할 수 있습니다. Azure Marketplace, 빠른 시작 리포지토리, 개인 GitHub 리포지토리 및 일대일 고객 참여를 통해 연결을 구성할 수 있습니다. 추적을 사용하도록 설정하려면 다음 두 가지 방법을 사용할 수 있습니다.

- Azure Resource Manager 템플릿: 파트너 소프트웨어를 실행하기 위해 Azure 서비스를 배포할 Resource Manager 템플릿 또는 솔루션 템플릿입니다. 파트너는 Azure 솔루션의 인프라와 구성을 정의하는 Resource Manager 템플릿을 만들 수 있습니다. Resource Manager 템플릿을 사용하면 개발자와 고객이 수명 주기 전반에 걸쳐 솔루션을 배포할 수 있습니다. 리소스가 일관된 상태로 배포된다는 것을 확신할 수 있습니다. 

- Azure Resource Manager API: 파트너는 Resource Manager API를 직접 호출하여 Resource Manager 템플릿을 배포하거나 Azure 서비스를 직접 프로비전할 API 호출을 생성할 수 있습니다. 

## <a name="use-resource-manager-templates"></a>Resource Manager 템플릿 사용

많은 파트너 솔루션은 Resource Manager 템플릿을 사용하여 고객의 구독에 배포됩니다. Azure Marketplace, GitHub 또는 빠른 시작에서 사용할 수 있는 Resource Manager 템플릿이 있는 경우, 새 추적 메서드를 사용하도록 템플릿을 수정하는 프로세스가 간단해야 합니다. Azure Resource Manager 템플릿을 사용하지 않는 경우, Resource Manager 템플릿 및 이러한 템플릿을 만드는 방법을 더 잘 이해하는 데 도움이 되는 몇 가지 링크는 다음과 같습니다. 

*   [첫 번째 Resource Manager 템플릿 만들기 및 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Azure Marketplace용 솔루션 템플릿 만들기](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="add-a-guid-to-your-template"></a>템플릿에 GUID 추가

GUID(Globally Unique Identifier)를 추가하려면 주 템플릿 파일을 한 번만 수정하면 됩니다.

1. [GUID](#create-guids)(예: eb7927c8-dd66-43e1-b0cf-c346a422063)를 만들고 [GUID를 등록](#register-guids-and-offers)합니다.

1. Resource Manager 템플릿을 엽니다.

1. 기본 템플릿 파일에 새 리소스를 추가합니다. 리소스는 **mainTemplate.json** 또는 **azuredeploy.json** 파일에만 있어야 하며, 중첩되거나 연결된 템플릿에 있으면 안됩니다.

1. **pid-** 접두사 뒤에 GUID 값을 입력합니다(예: pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. 템플릿에서 오류를 확인합니다.

1. 적절한 리포지토리에 템플릿을 다시 게시합니다.

1. [템플릿 배포에서 GUID 성공을 확인](#verify-the-guid-deployment)합니다.

### <a name="sample-template-code"></a>샘플 템플릿 코드

![샘플 템플릿 코드](media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG)

## <a name="use-the-resource-manager-apis"></a>Resource Manager API 사용

파트너가 Resource Manager REST API를 직접 호출하여 Azure 서비스를 배포하는 방법을 선호하는 경우도 있습니다. Azure는 이러한 호출을 사용하도록 설정하는 [여러 SDK를 지원](https://docs.microsoft.com/azure/#pivot=sdkstools)합니다. SDK 중 하나를 사용하거나, REST API를 직접 호출하여 리소스를 배포할 수 있습니다.

Resource Manager 템플릿을 사용하는 경우 앞에서 설명한 지침에 따라 솔루션에 태그를 지정해야 합니다. Resource Manager 템플릿을 사용하지 않고 API를 직접 호출하는 경우에도 Azure 리소스 사용량에 연결하도록 배포에 태그를 지정할 수 있습니다. 

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Resource Manager API를 사용하여 배포에 태그 지정

이 추적 방법의 경우 API 호출을 디자인할 때 요청의 사용자 에이전트 헤더에 GUID를 포함시킵니다. 각 제품 또는 SKU에 대한 GUID를 추가합니다. **pid-** 접두사를 사용하여 문자열의 형식을 지정하고 파트너가 생성한 GUID를 포함시킵니다. 사용자 에이전트에 삽입하기 위한 GUID 형식의 예제는 다음과 같습니다. 

![GUID 형식 예제](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> 문자열의 형식이 중요합니다. **pid-** 접두사가 포함되지 않으면 데이터를 쿼리할 수 없습니다. SDK마다 다른 방법으로 추적합니다. 이 방법을 구현하려면 기본 설정 Azure SDK에 대한 지원 및 추적 방법을 검토하세요. 

### <a name="example-the-python-sdk"></a>예제: Python SDK

Python의 경우 **config** 특성을 사용합니다. 이 특성은 UserAgent에만 추가할 수 있습니다. 예를 들면 다음과 같습니다.

![사용자 에이전트에 특성 추가](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> 각 클라이언트에 대한 특성을 추가합니다. 글로벌 정적 구성이 없습니다. 모든 클라이언트에서 추적할 수 있도록 클라이언트 팩터리에 태그를 지정할 수 있습니다. 자세한 내용은 [GitHub의 클라이언트 팩터리 샘플](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)을 참조하세요.

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Azure PowerShell을 사용하여 배포에 태그 지정

Azure PowerShell을 통해 리소스를 배포하는 경우 다음 방법을 사용하여 GUID를 추가합니다.

```
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Azure CLI를 사용하여 배포에 태그 지정

Azure CLI를 사용하여 GUID를 추가하는 경우 **AZURE_HTTP_USER_AGENT** 환경 변수를 설정합니다. 이 변수는 스크립트의 범위 내에서 설정할 수 있습니다. 셸 범위에 대한 글로벌 변수를 설정할 수도 있습니다.

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

## <a name="create-guids"></a>GUID 만들기

GUID는 32자리의 16진수가 있는 고유 참조 번호입니다. 추적을 위한 GUID를 만들려면 GUID 생성기를 사용해야 합니다. [Azure Storage의 GUID 생성기 양식](https://aka.ms/StoragePartners)을 활용하는 것이 좋습니다. 그러나 Azure Storage의 GUID 생성기를 사용하지 않으려는 경우 사용할 수 있는 [여러 온라인 GUID 생성기](https://www.bing.com/search?q=guid%20generator)가 있습니다.

> [!Note]
> 그렇지만 [Azure Storage의 GUID 생성기 양식](https://aka.ms/StoragePartners)을 사용하여 GUID를 만드는 것이 좋습니다. 자세한 내용은 [FAQ](#faq)를 참조하세요.

모든 제품 및 배포 채널에 대한 고유 GUID를 만듭니다. 템플릿을 사용하여 두 개의 솔루션을 배포하고 각각을 Azure Marketplace와 GitHub에서 사용할 수 있는 경우 4개의 GUID를 만들어야 합니다.

*   Azure Marketplace의 제품 A 
*   GitHub의 제품 A
*   Azure Marketplace의 제품 B 
*   GitHub의 제품 B

보고는 파트너 값(Microsoft 파트너 ID) 및 GUID를 통해 수행됩니다. 

SKU는 제품의 변형인 SKU와 같이 더 세부적인 수준에서 GUID를 추적할 수도 있습니다.

## <a name="register-guids-and-offers"></a>GUID 및 제품 등록

추적에 GUID를 포함하려면 GUID를 등록해야 합니다.  

템플릿 GUID에 대한 모든 등록은 Azure Marketplace CPP(Cloud 파트너 포털)를 통해 수행됩니다. 

GUID가 템플릿이나 사용자 에이전트에 추가되고 해당 GUID가 CPP에 등록되면 모든 배포가 추적됩니다. 

1. [Azure Marketplace](http://aka.ms/listonazuremarketplace)에 신청하여 CPP에 액세스합니다.

   * 파트너는 [CPP에 프로필을 가지고 있어야 합니다](https://docs.microsoft.com/azure/marketplace/become-publisher). Azure Marketplace 또는 AppSource에 제품을 나열하는 것이 좋습니다.
   * 파트너는 여러 GUID를 등록할 수 있습니다.
   * 파트너는 Marketplace 이외의 솔루션 템플릿 및 제품에 대한 GUID를 등록할 수 있습니다.
 
1. [클라우드 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

1. 오른쪽 위 모서리에서 계정 아이콘을 선택한 다음, **게시자 프로필**을 선택합니다.

   ![게시자 프로필 선택](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. **프로필 페이지**에서 **추적 GUID 추가**를 선택합니다.

   ![추적 GUID 추가 선택](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. **추적 GUID** 상자에서 추적 GUID를 입력합니다. **pid-** 접두사 없이 GUID만 입력합니다. **사용자 지정 설명** 상자에서 제품 이름 또는 설명을 입력합니다.

   ![프로필 페이지](media/marketplace-publishers-guide/guid-dev-center-login.png)
   
   ![GUID 및 제품 설명 입력](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. 여러 GUID를 등록하려면 **추적 GUID 추가**를 다시 선택합니다. 추가 상자가 페이지에 표시됩니다.

   ![추적 GUID 추가 다시 선택](media/marketplace-publishers-guide/guid-dev-center-example-add.png)
   
   ![다른 GUID를 입력하고 설명을 제공합니다.](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. **저장**을 선택합니다.

   ![저장 선택](media/marketplace-publishers-guide/guid-dev-center-save.png)

GUID가 템플릿이나 사용자 에이전트에 추가되고 해당 GUID가 CPP에 등록되면 모든 배포가 추적됩니다. 

## <a name="verify-the-guid-deployment"></a>GUID 배포 확인 

템플릿이 수정되고 테스트 배포가 실행되면 다음 PowerShell 스크립트를 사용하여 배포하고 태그를 지정한 리소스를 검색합니다. 

이 스크립트를 사용하여 GUID가 Resource Manager 템플릿에 성공적으로 추가되었는지 확인할 수 있습니다. Resource Manager API 배포에는 이 스크립트가 적용되지 않습니다.

Azure에 로그인합니다. 스크립트를 실행하기 전에 확인하려는 배포가 있는 구독을 선택합니다. 배포의 구독 컨텍스트 내에서 스크립트를 실행합니다.

배포의 **GUID** 및 **resourceGroup** 이름은 필수 매개 변수입니다.

GitHub에서 [원래의 스크립트](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)를 가져올 수 있습니다.

```
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="notify-your-customers"></a>고객에게 알림

파트너는 Resource Manager GUID 추적을 사용하는 배포에 대해 고객에게 알려야 합니다. Microsoft는 이러한 배포와 연결된 Azure 사용량을 파트너에게 보고합니다. 다음 예제에는 이러한 배포에 대해 고객에게 알리는 데 사용할 수 있는 콘텐츠가 포함되어 있습니다. 이 예제에서 \<PARTNER>는 회사 이름으로 바꿉니다. 파트너는 추적에서 고객을 제외할 수 있는 옵션을 포함하여 알림이 해당 데이터 개인 정보 및 수집 정책과 일치하는지 확인해야 합니다. 

### <a name="notification-for-resource-manager-template-deployments"></a>Resource Manager 템플릿 배포에 대한 알림

이 템플릿을 배포하면 Microsoft에서 배포된 Azure 리소스를 사용하여 \<PARTNER> 소프트웨어의 설치를 식별할 수 있습니다. Microsoft는 소프트웨어를 지원하는 데 사용되는 Azure 리소스를 상호 연결할 수 있습니다. Microsoft는 제품에 최상의 환경을 제공하고 비즈니스를 운영하기 위해 이 정보를 수집합니다. 데이터는 Microsoft 개인 정보 보호 정책에 따라 수집되고 관리되며, https://www.microsoft.com/trustcenter에 있습니다. 

### <a name="notification-for-sdk-or-api-deployments"></a>SDK 또는 API 배포 알림

\<PARTNER> 소프트웨어를 배포하면 Microsoft에서 배포된 Azure 리소스를 사용하여 \<PARTNER> 소프트웨어의 설치를 식별할 수 있습니다. Microsoft는 소프트웨어를 지원하는 데 사용되는 Azure 리소스를 상호 연결할 수 있습니다. Microsoft는 제품에 최상의 환경을 제공하고 비즈니스를 운영하기 위해 이 정보를 수집합니다. 데이터는 Microsoft 개인 정보 보호 정책에 따라 수집되고 관리되며, https://www.microsoft.com/trustcenter에 있습니다.

## <a name="get-support"></a>지원 받기

도움이 필요하면 다음 단계를 수행합니다.

1. [지원 페이지](https://go.microsoft.com/fwlink/?linkid=844975)로 이동합니다. 

1. **문제 유형** 아래에서 **Marketplace 온보딩**을 선택합니다.

1. 다음과 같이 문제에 대한 **범주**를 선택합니다.

   - 사용량 관련 문제의 경우 **기타**를 선택합니다.
   - Azure Marketplace CPP에 대한 액세스 문제의 경우 **액세스 문제**를 선택합니다.
   
    ![문제 범주 선택](media/marketplace-publishers-guide/lu-article-incident.png)

1. **요청 시작**을 선택합니다.

1. 다음 페이지에서 필요한 값을 입력합니다. **계속**을 선택합니다.

1. 다음 페이지에서 필요한 값을 입력합니다.

   > [!Important] 
   > **인시던트 제목** 상자에서 **ISV 사용량 추적**을 입력합니다. 문제를 자세히 설명하세요.
   
   ![인시던트 제목에 대한 ISV 사용량 추적 입력](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. 양식을 완성한 다음, **제출**을 선택합니다.

## <a name="faq"></a>FAQ

**템플릿에 GUID를 추가하면 어떤 이점이 있나요?**

Microsoft는 고객의 템플릿 배포 및 영향을 받는 사용량에 대한 인사이트 보기를 파트너에게 제공합니다. Microsoft와 파트너는 모두 이 정보를 사용하여 영업 팀 간에 긴밀한 관계를 구축할 수 있습니다. Microsoft와 파트너는 이 정보를 사용하여 개별 파트너가 Azure 성장에 미치는 영향에 대한 일관된 보기를 얻을 수 있습니다. 

**템플릿에 GUID를 추가할 수 있는 사람은 누구인가요?**

추적 리소스는 파트너 솔루션을 고객의 Azure 사용량에 연결하기 위한 것입니다. 사용량 데이터는 파트너의 MPN ID(Microsoft 파트너 네트워크 ID)와 연결됩니다. 보고는 CPP의 파트너가 사용할 수 있습니다.

**GUID는 추가된 후에 변경할 수 있나요?**
 
예, 고객 또는 구현 파트너는 템플릿을 사용자 지정할 수 있으며 GUID를 변경하거나 제거할 수 있습니다. 파트너가 리소스 및 GUID의 역할을 사전에 고객과 파트너에게 설명하여 추적 GUID를 제거하거나 편집하지 못하게 하는 것이 좋습니다. GUID가 변경되면 기존이 아닌 새 배포 및 리소스에만 영향을 줍니다.

**언제 보고를 사용할 수 있나요?**

보고의 베타 버전이 곧 제공될 예정입니다. 보고는 CPP에 통합됩니다.

**GitHub 등의 타사 리포지토리에서 배포된 템플릿을 추적할 수 있나요?**

예, 템플릿을 배포할 때 GUID가 있는 한 사용량이 추적됩니다. 파트너가 Azure Marketplace 외부에서 게시된 관련 템플릿을 등록하려면 CPP에 프로필이 있어야 합니다. 

**템플릿이 Azure Marketplace에서 배포된 경우와 GitHub 등의 다른 리포지토리에서 배포된 경우 간에 차이가 있나요?**

예, Azure Marketplace에 제품을 게시하는 파트너는 Azure Marketplace의 배포에 대한 자세한 데이터를 받을 수 있습니다. 파트너는 Azure Marketplace 포털 및 Azure Portal에서 고객에게 제품을 공개하는 기회를 활용할 수 있습니다. 또한 Azure Marketplace의 제품은 파트너에 대한 잠재 고객도 창출합니다.

**개별 고객 참여를 위해 사용자 지정 템플릿을 만드는 경우에는 어떻게 해야 하나요?**

이 경우에도 GUID를 템플릿에 추가할 수 있습니다. 기존 등록된 GUID를 사용하는 경우 해당 GUID가 보고에 포함됩니다. 새 GUID를 만드는 경우 새 GUID를 등록해야 추적에 포함됩니다.

**고객도 보고를 받나요?**

고객은 Azure Portal 내에서 개별 리소스 또는 고객 정의 리소스 그룹의 사용량을 추적할 수 있습니다.   

**추적 방법론은 DPOR(디지털 공식 파트너)과 유사한가요?**

배포 및 사용량을 파트너 솔루션에 연결하는 이 새로운 방법은 파트너 솔루션을 Azure 사용량에 연결하는 메커니즘을 제공합니다. DPOR는 컨설팅(시스템 통합자) 또는 관리(관리 서비스 공급자) 파트너를 고객의 Azure 구독과 연결하기 위한 것입니다.   

**Azure Storage의 GUID 생성기 양식을 사용하면 어떤 이점이 있나요?**

Azure Storage의 GUID 생성기 양식은 필요한 형식의 GUID를 생성하도록 보장합니다. 또한, Azure Storage의 데이터 평면 추적 방법을 사용하는 경우 Marketplace 제어 평면 추적에 동일한 GUID를 활용할 수 있습니다. 이렇게 하면 별도의 GUIDS를 유지하지 않고도 파트너 특성으로 단일 통합 GUID를 활용할 수 있습니다.
