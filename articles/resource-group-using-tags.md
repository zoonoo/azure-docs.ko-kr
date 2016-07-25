<properties
	pageTitle="태그를 사용하여 Azure 리소스 구성 | Microsoft Azure"
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
	ms.date="07/12/2016"
	ms.author="tomfitz"/>


# 태그를 사용하여 Azure 리소스 구성

리소스 관리자를 사용하면 태그를 적용하여 리소스를 논리적으로 구성할 수 있습니다. 태그는 정의하는 속성을 가진 리소스를 식별하는 키/값 쌍으로 구성됩니다. 동일한 범주에 속하는 것으로 리소스를 표시하려면 이러한 리소스에 동일한 태그를 적용합니다.

특정 태그를 사용하여 리소스를 보는 경우 모든 리소스 그룹에서 리소스를 참조합니다. 배포 관계와 무관하게 연결되는 방식으로 리소스를 구성할 수 있도록 하는 동일한 리소스 그룹의 리소스에 제한되지는 않습니다. 태그는 청구 또는 관리에 대한 리소스를 구성해야 하는 경우 특히 유용할 수 있습니다.

리소스 또는 리소스 그룹에 추가한 각 태그는 구독 전체의 분류에 자동으로 추가됩니다. 나중에 리소스에 태그를 지정할 때 사용하려는 태그 이름 및 값으로 구독을 위한 분류를 미리 채울 수도 있습니다.

각 리소스 또는 리소스 그룹에는 최대 15개의 태그가 포함될 수 있습니다. 태그 이름은 512자로 제한되며 태그 값은 256자로 제한됩니다.

> [AZURE.NOTE] 리소스 관리자 작업을 지원하는 리소스에만 태그를 적용할 수 있습니다. 클래식 배포 모델을 통해(예: 클래식 포털 또는 서비스 관리 API를 통해) 가상 컴퓨터, 가상 네트워크 또는 저장소를 만든 경우 해당 리소스에 태그를 적용할 수 없습니다. 태그 지정을 지원하기 위해서는 리소스 관리자를 통해 이러한 리소스를 다시 배포해야 합니다. 다른 모든 리소스는 태그 지정을 지원합니다.

## 템플릿의 태그

배포 중 리소스에 태그를 지정하려면 배포할 리소스에 **tags** 요소를 추가하고 태그 이름 및 값을 제공하기만 하면 됩니다. 태그 이름 및 값은 구독에 미리 존재할 필요가 없습니다. 각 리소스에 최대 15개의 태그를 제공할 수 있습니다.

다음 예제에서는 태그가 있는 저장소 계정을 보여 줍니다.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

현재 리소스 관리자는 태그 이름 및 값에 대한 개체를 처리하도록 지원하지 않습니다. 대신, 태그 값에 대한 개체를 전달할 수 있지만 아래와 같이 태그 이름을 지정해야 합니다.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## 포털의 태그

포털을 통해 기존 리소스 및 리소스 그룹에 태그를 추가할 수 있습니다. 찾아보기 허브를 사용하여 태그를 지정할 리소스 또는 리소스 그룹으로 이동한 후 블레이드 위쪽의 개요 섹션에서 태그 부분을 클릭합니다.

![리소스 및 리소스 그룹 블레이드에서 태그 파트](./media/resource-group-using-tags/tag-icon.png)

이렇게 하면 이미 적용된 태그의 목록이 있는 블레이드가 열립니다. 첫 번째 태그이면 목록은 비어 있습니다. 태그를 추가하려면 이름 및 값을 지정하고 Enter 키를 누르기만 하면 됩니다. 몇 개의 태그를 추가하면 기존 태그 이름 및 값에 따라 자동 완성 옵션이 제공되므로 리소스 간에 일관된 분류를 적용하기 쉬우며 맞춤법 오류와 같은 일반적인 실수를 피할 수 있습니다.

![이름/값 쌍이 있는 태그 리소스](./media/resource-group-using-tags/tag-resources.png)

포털에서 태그 분류를 보려면 **찾아보기** 및 **태그**를 선택합니다.

![찾아보기 허브를 통해 태그 찾기](./media/resource-group-using-tags/select-tags.png)

구독에 태그 요약이 표시됩니다.

![모든 태그 표시](./media/resource-group-using-tags/show-tag-summary.png)

이러한 태그 중에서 선택하면 해당 태그와 함께 리소스 및 리소스 그룹이 표시됩니다.

![태그가 지정된 리소스 표시](./media/resource-group-using-tags/show-tagged-resources.png)

빠른 액세스를 위해 대시보드에 가장 중요한 태그를 고정합니다.

![시작 보드에 태그 고정](./media/resource-group-using-tags/show-pinned-tag.png)

## 태그 및 PowerShell

태그는 리소스 및 리소스 그룹에 직접 존재하므로 이미 적용된 태그를 확인하기 위해서는 **Get-AzureRmResource** 또는 **Get-AzureRmResourceGroup**이 있는 리소스나 리소스 그룹을 파악하기만 하면 됩니다. 리소스 그룹부터 살펴보겠습니다.

    Get-AzureRmResourceGroup -Name tag-demo-group

이 cmdlet은 적용된 태그를 포함하여 리소스 그룹의 일부 메타데이터를 반환합니다.

    ResourceGroupName : tag-demo-group
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

리소스에 대한 메타데이터를 가져올 때 태그가 직접 표시되지 않습니다.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group

태그는 결과와 같이 Hashtable 개체로만 표시됩니다.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

실제 태그를 보려면 **Tags** 속성을 검색합니다.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
   
서식이 지정된 결과가 반환됩니다.
    
    Dept: Finance
    Environment: Production

특정 리소스 그룹 또는 리소스에 대한 태그를 보는 대신 특정 태그 및 값이 있는 모든 리소스 또는 리소스 그룹을 검색하려고 할 수 있습니다. 특정 태그가 있는 리소스 그룹을 가져오려면 **-Tag** 매개 변수와 함께 **Find-AzureRmResourceGroup** cmdlet을 사용합니다.

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    
해당 태그 값을 포함하는 리소스 그룹의 이름이 반환됩니다.
   
    tag-demo-group
    web-demo-group

특정 태그 및 값이 있는 모든 리소스를 가져오려면 **Find-AzureRmResource** cmdlet을 사용합니다.

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
    
해당 태그 값을 포함하는 리소스의 이름이 반환됩니다.
    
    tfsqlserver
    tfsqldatabase

기존 태그가 없는 리소스 그룹에 태그를 추가하려면 **Set-AzureRmResourceGroup** 명령을 사용하여 태그 개체를 지정합니다.

    Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

새로운 태그 값이 있는 리소스 그룹이 반환됩니다.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
**Set-AzureRmResource** 명령을 사용하여 기존 태그가 없는 리소스에 태그를 추가할 수 있습니다.

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

태그는 전체적으로 업데이트되므로 이미 태그가 지정된 리소스에 태그를 추가하려는 경우 유지하려는 모든 태그가 있는 배열을 사용해야 합니다. 이렇게 하려면 먼저 기존 태그를 선택하고 해당 집합에 새 태그를 추가한 다음 모든 태그를 다시 적용합니다.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

태그를 하나 이상 제거하려는 경우에는 제거하려는 태그가 없는 배열을 저장하면 됩니다.

프로세스는 **Get-AzureRmResource** 및 **Set-AzureRmResource** cmdlet을 사용한다는 점을 제외하고 리소스에 대한 프로세스와 동일합니다.

PowerShell을 사용하여 구독 내의 모든 태그 목록을 가져오려면 **Get-AzureRmTag** cmdlet을 사용합니다.

    Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

"hidden-" 및 "link:"로 시작하는 태그도 볼 수 있습니다. 이러한 태그는 내부 태그이므로 무시해도 되며 변경하지 않도록 하세요.

**New-AzureRmTag** cmdlet을 사용하여 분류에 새 태그를 추가합니다. 이러한 태그는 아직 리소스 또는 리소스 그룹에 적용되지 않았더라도 자동 완성에 포함됩니다. 태그 이름/값을 제거하려면 먼저 함께 사용될 수 있는 모든 리소스에서 태그를 제거하고 **Remove-AzureRmTag** cmdlet을 사용하여 분류에서 제거합니다.

## 태그 및 Azure CLI

태그는 리소스 및 리소스 그룹에 직접 존재하므로 이미 적용된 태그를 확인하기 위해서는 **azure group show**가 있는 리소스나 리소스 그룹을 파악하기만 하면 됩니다.

    azure group show -n tag-demo-group
    
적용할 태그를 포함하여 리소스 그룹에 대한 메타데이터가 반환됩니다.
    
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

리소스 그룹에 대한 태그만 가져오려면 [jq](http://stedolan.github.io/jq/download/)와 같은 JSON 유틸리티를 사용합니다.

    azure group show -n tag-demo-group --json | jq ".tags"
    
해당 리소스 그룹에 대한 태그가 반환됩니다.
    
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

**azure resource show**를 사용하여 특정 리소스에 대한 태그를 볼 수 있습니다.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    
해당 리소스에 대한 태그가 반환됩니다.
    
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
아래와 같이 특정 태그와 값을 가진 리소스를 모두 검색할 수 있습니다.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    
해당 태그를 포함하는 리소스의 이름이 반환됩니다.
    
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

태그는 전체적으로 업데이트되므로 이미 태그가 지정된 리소스에 하나의 태그를 추가하려는 경우 유지하려는 모든 기존 태그를 검색해야 합니다. 리소스 그룹에 대한 태그 값을 설정하려면 **azure group set**를 사용하여 리소스 그룹에 모든 태그를 제공합니다.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    
새 태그가 있는 리소스 그룹에 대한 요약이 반환됩니다.
    
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
**azure tag list**를 사용하여 구독에 있는 기존 태그를 나열하고 **azure tag create**를 사용하여 새 태그를 추가할 수 있습니다. 구독에 대한 분류에서 태그를 제거하려면 먼저 함께 사용될 수 있는 모든 리소스에서 태그를 제거하고 **azure tag delete**를 사용하여 태그를 제거합니다.

## 태그 및 REST API

포털 및 PowerShell 둘 다 백그라운드에서 [리소스 관리자 REST API](https://msdn.microsoft.com/library/azure/dn848368.aspx)를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 GET을 사용하여 태그를 얻고 PATCH 호출을 통해 태그 집합을 업데이트할 수 있습니다.


## 태그 및 청구

지원 되는 서비스에 대한 청구 데이터를 그룹화하는 데 태그를 사용할 수 있습니다. 예를 들어, [Azure 리소스 관리자와 통합된 가상 컴퓨터](./virtual-machines/virtual-machines-windows-compare-deployment-models.md)를 사용하면 가상 컴퓨터에 대한 청구 사용을 구성하는 태그를 정의하고 적용할 수 있습니다. 서로 다른 조직에 여러 VM을 실행하는 경우 비용 센터에 따라 그룹 사용에 대한 태그를 사용할 수 있습니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용과 같이 런타임 환경으로 비용을 분류하는 데 태그를 사용할 수 있습니다.

[Azure 계정 포털](https://account.windowsazure.com/) 또는 [EA 포털](https://ea.azure.com)에서 다운로드할 수 있는 [Azure 리소스 사용 및 RateCard API](billing-usage-rate-card-overview.md) 또는 사용 CSV(쉼표로 구분된 값) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다. 대금 청구 정보에 프로그래밍 방식으로 액세스하는 방법은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](billing-usage-rate-card-overview.md)를 참조하세요. REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 참조하세요.

대금 청구에 태그를 지원하는 서비스용 사용 CSV를 다운로드하면 **태그** 열에 태그가 나타납니다 더 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하십시오.

![요금 청구에 대한 태그를 참조하십시오.](./media/resource-group-using-tags/billing_csv.png)

## 다음 단계

- 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 정의한 정책을 사용하려면 모든 리소스에 대해 특정 태그를 설정해야 할 수 있습니다. 자세한 내용은 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)를 참조하세요.
- 리소스 배포 시 Azure PowerShell 사용에 대한 소개는 [Azure 리소스 관리자에서 Azure PowerShell 사용](./powershell-azure-resource-manager.md)을 참조하세요.
- 리소스 배포 시 Azure CLI 사용에 대한 소개는 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](./xplat-cli-azure-resource-manager.md)을 참조하세요.
- 포털 사용에 대한 소개는 [Azure 포털을 사용하여 Azure 리소스 관리](./azure-portal/resource-group-portal.md)를 참조하세요.

<!---HONumber=AcomDC_0713_2016-->