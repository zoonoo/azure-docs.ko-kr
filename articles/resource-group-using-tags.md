<properties
	pageTitle="태그를 사용하여 Azure 리소스 구성"
	description="태그를 적용하여 대금 청구 및 관리를 위해 리소스를 구성하는 방법을 보여 줍니다."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="tomfitz"/>


# 태그를 사용하여 Azure 리소스 구성

리소스 관리자를 사용하면 태그를 적용하여 리소스를 논리적으로 구성할 수 있습니다. 태그는 정의하는 속성을 가진 리소스를 식별하는 키/값 쌍으로 구성됩니다. 동일한 범주에 속하는 것으로 리소스를 표시하려면 이러한 리소스에 동일한 태그를 적용합니다.

특정 태그를 사용하여 리소스를 보는 경우 모든 리소스 그룹에서 리소스를 참조합니다. 배포 관계와 무관하게 연결되는 방식으로 리소스를 구성할 수 있도록 하는 동일한 리소스 그룹의 리소스에 제한되지는 않습니다. 태그는 청구 또는 관리에 대한 리소스를 구성해야 하는 경우 특히 유용할 수 있습니다.

리소스 또는 리소스 그룹에 추가한 각 태그는 구독 전체의 분류에 자동으로 추가됩니다. 나중에 리소스에 태그를 지정할 때 사용하려는 태그 이름 및 값으로 구독을 위한 분류를 미리 채울 수도 있습니다.

> [AZURE.NOTE]리소스 관리자 작업을 지원하는 리소스에만 태그를 적용할 수 있습니다. 기존 배포 모델을 통해(예: Azure 포털 또는[서비스 관리 API](../services/api-management/)를 통해) 가상 컴퓨터, 가상 네트워크 또는 저장소를 만든 경우, 해당 리소스에 태그를 적용할 수 없습니다. 태그 지정을 지원하기 위해서는 리소스 관리자를 통해 이러한 리소스를 다시 배포해야 합니다. 다른 모든 리소스는 태그 지정을 지원합니다.


## 미리 보기 포털의 태그

미리 보기 포털에서 리소스 및 리소스 그룹에 쉽게 태그를 지정할 수 있습니다. 찾아보기 허브를 사용하여 태그를 지정할 리소스 또는 리소스 그룹으로 이동한 후 블레이드 위쪽의 개요 섹션에서 태그 부분을 클릭합니다.

![리소스 및 리소스 그룹 블레이드에서 태그 파트](./media/resource-group-using-tags/tag-icon.png)

이렇게 하면 이미 적용된 태그의 목록이 있는 블레이드가 열립니다. 첫 번째 태그이면 목록은 비어 있습니다. 태그를 추가하려면 이름 및 값을 지정하고 Enter 키를 누르기만 하면 됩니다. 몇 개의 태그를 추가하면 기존 태그 이름 및 값에 따라 자동 완성 옵션이 제공되므로 리소스 간에 일관된 분류를 적용하기 쉬우며 맞춤법 오류와 같은 일반적인 실수를 피할 수 있습니다.

![이름/값 쌍이 있는 태그 리소스](./media/resource-group-using-tags/tag-resources.png)

포털에서 태그 분류를 확인하려면 찾아보기 허브를 사용하여 모든 항목을 확인하고 태그를 선택합니다.

![찾아보기 허브를 통해 태그 찾기](./media/resource-group-using-tags/browse-tags.png)

빠른 액세스를 위해 시작 보드에 가장 중요 한 태그를 고정하고 진행 준비를 완료합니다. 즐겁게 작업하세요!

![시작 보드에 태그 고정](./media/resource-group-using-tags/pin-tags.png)

## PowerShell을 사용한 태그 지정

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

태그는 리소스 및 리소스 그룹에 직접 존재하므로 이미 적용된 태그를 확인하기 위해서는 **Get-AzureRmResource** 또는 **Get-AzureRmResourceGroup**이 있는 리소스나 리소스 그룹을 파악하기만 하면 됩니다. 리소스 그룹부터 살펴보겠습니다.

    PS C:\> Get-AzureRmResourceGroup tag-demo

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    Resources         :
                    Name                             Type                                  Location
                    ===============================  ====================================  ==============
                    CPUHigh ExamplePlan              microsoft.insights/alertrules         eastus
                    ForbiddenRequests tag-demo-site  microsoft.insights/alertrules         eastus
                    LongHttpQueue ExamplePlan        microsoft.insights/alertrules         eastus
                    ServerErrors tag-demo-site       microsoft.insights/alertrules         eastus
                    ExamplePlan-tag-demo             microsoft.insights/autoscalesettings  eastus
                    tag-demo-site                    microsoft.insights/components         centralus
                    ExamplePlan                      Microsoft.Web/serverFarms             southcentralus
                    tag-demo-site                    Microsoft.Web/sites                   southcentralus


이 cmdlet은 적용된 태그를 포함하여 리소스 그룹의 일부 메타데이터를 반환합니다. 리소스 그룹에 태그를 지정하기 위해 **Set-AzureRmResourceGroup** 명령을 사용하고 태그 이름과 값을 지정합니다.

    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag @( @{ Name="project"; Value="tags" }, @{ Name="env"; Value="demo"} )

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  =====
                    project  tags
                    env      demo

태그는 전체적으로 업데이트되므로 이미 태그가 지정된 리소스에 태그를 추가하려는 경우 유지하려는 모든 태그가 있는 배열을 사용해야 합니다. 이렇게 하려면 먼저 기존 태그를 선택하고 새 태그를 추가하면 됩니다.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag $tags

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  ========
                    project  tags
                    env      demo
                    status   approved


태그를 하나 이상 제거하려는 경우에는 제거하려는 태그가 없는 배열을 저장하면 됩니다.

프로세스는 **Get-AzureRmResource** 및 **Set-AzureRmResource** cmdlet을 사용한다는 점을 제외하고 리소스에 대한 프로세스와 동일합니다.

특정 태그가 있는 리소스 그룹을 가져오려면 **-Tag** 매개 변수와 함께 **Find-AzureRmResourceGroup** cmdlet을 사용합니다.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo

Azure PowerShell 1.0 Preview 이전 버전의 경우 다음 명령을 사용하여 특정 태그가 있는 리소스를 가져옵니다.

    PS C:\> Get-AzureResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo
    PS C:\> Get-AzureResource -Tag @{ Name="env"; Value="demo" } | %{ $_.Name }
    rbacdemo-web
    rbacdemo-docdb
    ...    

PowerShell을 사용하여 구독 내의 모든 태그 목록을 가져오려면 **Get-AzureRmTag** cmdlet을 사용합니다.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

"hidden-" 및 "link:"로 시작하는 태그도 볼 수 있습니다. 이러한 태그는 내부 태그이므로 무시해도 되며 변경하지 않도록 하세요.

**New-AzureRmTag** cmdlet을 사용하여 분류에 새 태그를 추가합니다. 이러한 태그는 아직 리소스 또는 리소스 그룹에 적용되지 않았더라도 자동 완성에 포함됩니다. 태그 이름/값을 제거하려면 먼저 함께 사용될 수 있는 모든 리소스에서 태그를 제거하고 **Remove-AzureRmTag** cmdlet을 사용하여 분류에서 제거합니다.

## REST API를 사용하여 태그 지정

포털 및 PowerShell 둘 다 백그라운드에서 [리소스 관리자 REST API](http://msdn.microsoft.com/library/azure/dn790568.aspx)를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 GET을 사용하여 태그를 얻고 PATCH 호출을 통해 태그 집합을 업데이트할 수 있습니다.


## 태그 지정 및 대금 청구

지원 되는 서비스에 대한 청구 데이터를 그룹화하는 데 태그를 사용할 수 있습니다. 예를 들어, [Azure 리소스 관리자와 통합된 가상 컴퓨터](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md)를 사용하면 가상 컴퓨터에 대한 청구 사용을 구성하는 태그를 정의하고 적용할 수 있습니다. 서로 다른 조직에 여러 VM을 실행하는 경우 비용 센터에 따라 그룹 사용에 대한 태그를 사용할 수 있습니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용과 같이 런타임 환경으로 비용을 분류하는 데 태그를 사용할 수 있습니다.

[Azure 계정 포털](https://account.windowsazure.com/) 또는 [EA 포털](https://ea.azure.com)에서 다운로드할 수 있는 [Azure 리소스 사용 및 RateCard API](billing-usage-rate-card-overview.md) 또는 사용 쉼표로 구분된 값(CSV) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다. 대금 청구 정보에 프로그래밍 방식으로 액세스하는 방법은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](billing-usage-rate-card-overview.md)를 참조하세요. REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 참조하세요.

대금 청구에 태그를 지원하는 서비스용 사용 CSV를 다운로드하면 **태그** 열에 태그가 나타납니다 더 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하십시오.

![요금 청구에 대한 태그를 참조하십시오.](./media/resource-group-using-tags/billing_csv.png)

## 다음 단계

- 리소스 배포 시 Azure PowerShell 사용에 대한 소개는 [Azure 리소스 관리자와 함께 Azure PowerShell 사용](./powershell-azure-resource-manager.md)을 참조하세요.
- 리소스 배포 시 Azure CLI 사용에 대한 소개는 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](./xplat-cli-azure-resource-manager.md)을 참조하세요.
- Preview 포털 사용에 대한 소개는 [Azure Preview 포털을 사용하여 Azure 리소스 관리](./resource-group-portal.md)를 참조하세요.  

<!---HONumber=Oct15_HO3-->