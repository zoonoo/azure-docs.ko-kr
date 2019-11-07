---
title: Azure Cosmos 계정에 대 한 Azure 개인 링크 구성
description: 가상 네트워크에서 개인 IP 주소를 사용 하 여 azure Cosmos 계정에 액세스 하도록 Azure 개인 링크를 설정 하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 254c2645d842a6f6a2eaaeca2369b93a81e1a8cd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681673"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Azure Cosmos 계정에 대 한 Azure 개인 링크 구성 (미리 보기)

Azure 개인 링크를 사용 하 여 개인 끝점을 통해 Azure Cosmos 계정에 연결할 수 있습니다. 개인 끝점은 가상 네트워크 내의 서브넷에 있는 개인 IP 주소의 집합입니다. 개인 링크를 사용 하 여 개인 IP 주소를 통해 지정 된 Azure Cosmos 계정에 대 한 액세스를 제한할 수 있습니다. 제한 된 NSG 정책과 결합 하면 개인 링크를 통해 데이터 exfiltration의 위험을 줄일 수 있습니다. 개인 끝점에 대 한 자세한 내용은 [Azure 개인 링크](../private-link/private-link-overview.md) 문서를 참조 하세요.

또한 개인 링크를 사용 하면 가상 네트워크 또는 피어 링 가상 네트워크 내에서 Azure Cosmos 계정에 액세스할 수 있습니다. 개인 링크에 매핑된 리소스는 VPN 또는 Express 경로를 통해 개인 피어 링을 통해 온-프레미스에서 액세스할 수도 있습니다. 

"자동" 또는 "수동" 승인 방법을 사용 하 여 개인 링크로 구성 된 Azure Cosmos 계정에 연결할 수 있습니다. 자세히 알아보려면 개인 링크 설명서의 [승인 워크플로](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) 섹션을 참조 하세요. 이 문서에서는 자동 승인 방법을 사용 한다고 가정 하는 개인 링크를 만드는 단계를 설명 합니다.

## <a name="create-a-private-link-using-the-azure-portal"></a>Azure Portal를 사용 하 여 개인 링크 만들기

Azure Portal를 사용 하 여 기존 Azure Cosmos 계정에 대 한 개인 링크를 만들려면 다음 단계를 사용 합니다.

1. **모든 리소스** 창에서 보안을 설정할 Azure Cosmos DB 계정을 찾습니다.

1. 설정 메뉴에서 **개인 끝점 연결** 을 선택 하 고 **개인 끝점** 옵션을 선택 합니다.

   ![Azure Portal를 사용 하 여 개인 끝점 만들기](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. **개인 끝점 만들기 (미리 보기) – 기본 사항**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | 구독 | 사용 중인 구독을 선택합니다. |
    | 리소스 그룹 | 리소스 그룹을 선택합니다.|
    | **인스턴스 세부 정보** |  |
    | Name | 개인 끝점의 이름을 입력 합니다. 이 이름을 사용 하는 경우 고유한 이름을 만듭니다. |
    |지역| 개인 링크를 배포할 지역을 선택 합니다. 가상 네트워크가 있는 위치와 동일한 위치에 개인 끝점을 만들어야 합니다.|
    |||
1. **다음: 리소스**를 선택 합니다.
1. **개인 끝점 만들기-리소스**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | 내 디렉터리의 Azure 리소스에 연결하도록 선택합니다. <br/><br/> 이 옵션을 사용 하면 개인 링크를 설정 하는 리소스 중 하나를 선택 하거나, 공유 된 리소스 ID 또는 별칭을 사용 하 여 다른 사용자의 리소스에 연결할 수 있습니다.|
    | 구독| 사용 중인 구독을 선택합니다. |
    | 리소스 종류 | **Microsoft.azurecosmosdb/databaseAccounts**를 선택 합니다. |
    | 리소스 |Azure Cosmos 계정 선택 |
    |대상 하위 리소스 |매핑할 Cosmos DB API 유형을 선택 합니다. 이 기본값은 SQL, MongoDB 및 Cassandra Api에 대해 선택 하는 옵션 중 하나입니다. Gremlin 및 Table Api의 경우 sql API와 상호 운용할 수 있는 Api를 사용 하 여 *sql* 을 선택할 수도 있습니다. |
    |||

1. **다음: 구성**을 선택 합니다.
1. **프라이빗 엔드포인트 만들기(미리 보기) - 구성**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| 가상 네트워크를 선택 합니다. |
    | 서브넷 | 서브넷을 선택 합니다. |
    |**사설 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. <br><br/> 개인 끝점과 개인적으로 연결 하려면 DNS 레코드가 필요 합니다. 개인 끝점을 개인 DNS 영역에 통합 하는 것이 좋습니다. 사용자의 DNS 서버를 활용 하거나 가상 컴퓨터의 호스트 파일을 사용 하 여 DNS 레코드를 만들 수도 있습니다. |
    |프라이빗 DNS 영역 |*Privatelink.documents.azure.com* 선택 <br><br/> 사설 DNS 영역은 자동으로 결정 되며 현재 Azure Portal를 사용 하 여 변경할 수 없습니다.|
    |||

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.
1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

Azure Cosmos 계정에 대 한 개인 링크 Azure Portal를 승인한 경우 **방화벽 및 가상 네트워크** 창의 **모든 네트워크** 옵션은 회색으로 표시 됩니다.

다음 표에서는 서로 다른 Azure Cosmos 계정 API 형식, 지원 되는 하위 리소스 및 해당 개인 영역 이름 간의 매핑을 보여 줍니다. Gremlin 및 Table API 계정은 SQL API를 통해서도 액세스할 수 있으므로 이러한 Api에 대 한 두 개의 항목이 있습니다.

|Azure Cosmos 계정 API 유형  |지원 되는 하위 리소스 (또는 groupIds) |개인 영역 이름  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|테이블    |    테이블     |   privatelink.table.cosmos.azure.com    |
|테이블     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>개인 IP 주소 가져오기

개인 끝점이 프로 비전 되 면 IP 주소를 쿼리할 수 있습니다. Azure Portal에서 IP 주소를 확인 합니다. **모든 리소스**를 선택 하 고,이 경우 앞에서 만든 개인 끝점을 검색 하 여 "dbPrivateEndpoint3"를 선택 하 고 개요 탭을 선택 하 여 DNS 설정 및 IP 주소를 확인 합니다.

![Azure Portal의 개인 IP 주소](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

개인 끝점 마다 여러 IP 주소가 생성 됩니다.

* Azure Cosmos 계정의 전역 (지역에 관계 없음) 끝점에 대 한 하나입니다.
* Azure Cosmos 계정이 배포 되는 각 지역에 대해 하나씩입니다.

## <a name="create-a-private-link-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 개인 링크 만들기

다음 PowerSehll 스크립트를 실행 하 여 기존 Azure Cosmos 계정에 대해 이름이 "MyPrivateEndpoint" 인 개인 링크를 만듭니다. 변수 값을 사용자 환경에 맞는 세부 정보로 바꾸어야 합니다.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="fetch-the-private-ip-addresses"></a>개인 IP 주소 가져오기

개인 끝점이 프로 비전 된 후 다음 PowerShell 스크립트를 사용 하 여 IP 주소 및 FQDN 매핑을 쿼리할 수 있습니다.

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 개인 링크 만들기

가상 네트워크 서브넷에 개인 끝점을 만들어 개인 링크를 설정할 수 있습니다. 이는 Azure Resource Manager 템플릿을 사용 하 여 수행 됩니다. 다음 코드를 사용 하 여 "PrivateEndpoint_template" 라는 리소스 관리자 템플릿을 만듭니다. 이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos SQL API 계정에 대 한 개인 끝점을 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

### <a name="define-the-template-parameters-file"></a>템플릿 매개 변수 파일 정의

템플릿에 대 한 매개 변수 파일을 만들고 이름을 "PrivateEndpoint_parameters"로 이름을로 합니다. 매개 변수 파일에 다음 코드를 추가 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

### <a name="deploy-the-template-by-using-a-powershell-script"></a>PowerShell 스크립트를 사용 하 여 템플릿 배포

다음 코드를 사용 하 여 PowerShell 스크립트를 만듭니다. 스크립트를 실행 하기 전에 구독 ID, 리소스 그룹 이름 및 기타 변수 값을 사용자 환경에 특정 한 세부 정보로 바꾸어야 합니다.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

PowerShell 스크립트에서 "GroupId" 변수는 계정의 API 유형인 값을 하나만 포함할 수 있습니다. 허용 되는 값은 SQL, MongoDB, Cassandra, Gremlin 및 Table입니다. 일부 Azure Cosmos 계정 유형은 여러 Api를 통해 액세스할 수 있습니다. 예:

* Gremlin API 계정은 Gremlin 및 SQL API 계정 모두에서 액세스할 수 있습니다.
* Table API 계정은 테이블 및 SQL API 계정 모두에서 액세스할 수 있습니다.

이러한 계정에 대해 "GroupId" 배열에 지정 된 해당 API 유형을 사용 하 여 각 API 유형에 대해 하나의 개인 끝점을 만들어야 합니다.

템플릿이 성공적으로 배포 된 후 다음 이미지에 표시 된 것과 유사한 출력을 볼 수 있습니다. 개인 끝점이 올바르게 설정 된 경우 provisioningState 값은 "Succeeded"입니다.

![리소스 관리자 템플릿 배포 출력](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

템플릿을 배포한 후에는 개인 IP 주소가 서브넷 내에서 예약 됩니다. Azure Cosmos 계정의 방화벽 규칙은 개인 끝점의 연결만 허용 하도록 구성 됩니다.

## <a name="configure-custom-dns"></a>사용자 지정 DNS 구성

개인 연결 미리 보기 중 개인 끝점이 생성 된 서브넷 내에서 개인 DNS를 사용 해야 합니다. 각 개인 IP 주소가 DNS 항목에 매핑되도록 끝점을 구성 합니다 (위에 표시 된 응답의 "fqdn" 속성 참조).

개인 끝점을 만들 때 Azure의 개인 DNS 영역과 통합할 수 있습니다. 개인 끝점을 Azure의 개인 DNS 영역과 통합 하지 않고 대신 사용자 지정 DNS를 사용 하도록 선택한 경우 새 지역에 해당 하는 개인 IP에 대 한 새 DNS 레코드를 추가 하도록 DNS를 구성 해야 합니다.

## <a name="firewall-configuration-with-private-link"></a>개인 링크를 사용 하는 방화벽 구성

다음은 방화벽 규칙과 함께 개인 링크를 사용 하는 경우의 다양 한 상황 및 결과입니다.

* 구성 된 방화벽 규칙이 없는 경우 기본적으로 Azure Cosmos 계정은 모든 트래픽에 액세스할 수 있습니다.

* 공용 트래픽 또는 서비스 끝점을 구성 하 고 개인 끝점을 만든 경우에는 해당 유형의 방화벽 규칙에 따라 들어오는 트래픽 유형이 서로 다릅니다.

* 공용 트래픽 또는 서비스 끝점이 구성 되지 않고 개인 끝점이 생성 된 경우 Azure Cosmos 계정은 개인 끝점을 통해서만 액세스할 수 있습니다. 공용 트래픽 또는 서비스 끝점이 구성 되지 않은 경우 승인 된 모든 개인 끝점을 거부 하거나 삭제 한 후 계정이 모든 네트워크에 열립니다.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>영역을 추가 하거나 제거 하는 경우 개인 끝점 업데이트

Azure Cosmos 계정에 지역을 추가 하거나 제거 하려면 해당 계정에 대 한 DNS 항목을 추가 하거나 제거 해야 합니다. 이러한 변경 내용은 개인 끝점에서 적절 하 게 업데이트 해야 합니다. 현재 다음 단계를 수행 하 여 수동으로 변경 해야 합니다.

1. Azure Cosmos DB 관리자가 지역을 추가 하거나 제거 합니다. 그러면 네트워크 관리자에 게 보류 중인 변경 내용에 대 한 알림이 표시 됩니다. Azure Cosmos 계정에 매핑된 개인 끝점은 "작업이 필요 합니다." 속성이 "None"에서 "다시 만들기"로 변경 된 것을 볼 수 있습니다. 그런 다음 네트워크 관리자는이를 만드는 데 사용 되는 것과 동일한 리소스 관리자 페이로드를 사용 하 여 PUT 요청을 실행 하 여 개인 끝점을 업데이트 합니다.

1. 이 작업 후에는 추가 되거나 제거 된 DNS 항목과 해당 개인 IP 주소를 반영 하도록 서브넷의 개인 DNS도 업데이트 해야 합니다.

예를 들어, "미국 서 부", "미국 중부" 및 "유럽 서부"의 3 개 지역에서 Azure Cosmos 계정을 배포 하는 경우입니다. 계정에 대 한 개인 끝점을 만들 때 4 개의 개인 Ip가 서브넷에 예약 되어 있습니다. 각 지역에 대해 하나씩 (총 3 개, 전역/지역에 상관 없는 끝점의 경우)

나중에 Azure Cosmos 계정에 새 지역 (예: "미국 동부")을 추가 합니다. 기본적으로 새 지역은 기존 개인 끝점에서 액세스할 수 없습니다. Azure Cosmos 계정 관리자는 새 지역에 액세스 하기 전에 개인 끝점 연결을 새로 고쳐야 합니다. 

` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` 명령을 실행 하면 명령의 출력에 "다시 만들기"로 설정 된 `actionsRequired` 매개 변수가 포함 됩니다. 이 값은 개인 끝점을 새로 고쳐야 함을 나타냅니다. 그런 다음 Azure Cosmos 계정 관리자는 `Set-AzPrivateEndpoint` 명령을 실행 하 여 개인 끝점 새로 고침을 트리거합니다.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

새 개인 IP는이 개인 끝점의 서브넷에서 자동으로 예약 되며 `actionsRequired` 값이 `None`됩니다. 개인 DNZ 영역 통합이 없는 경우 (즉, 사용자 지정 개인 DNS를 사용 하는 경우) 새 지역에 해당 하는 개인 IP에 대 한 새 DNS 레코드를 추가 하도록 개인 DNS를 구성 해야 합니다.

지역을 제거할 때와 동일한 단계를 사용할 수 있습니다. 제거 된 영역의 개인 IP가 자동으로 회수 되 고 `actionsRequired` 플래그가 `None`됩니다. 개인 DNZ 영역 통합이 없는 경우 제거 된 지역에 대 한 DNS 레코드를 제거 하도록 개인 DNS를 구성 해야 합니다.

개인 끝점을 삭제 하거나 Azure Cosmos 계정의 지역이 제거 되 면 개인 DNS 영역의 DNS 레코드는 자동으로 제거 되지 않습니다. DNS 레코드를 수동으로 제거 해야 합니다.

## <a name="current-limitations"></a>현재 제한 사항

Azure Cosmos 계정에 개인 링크를 사용 하는 경우 다음 제한 사항이 적용 됩니다.

* 직접 모드 연결을 사용 하 여 Azure Cosmos 계정에 개인 링크를 사용 하는 경우 TCP 프로토콜만 사용할 수 있습니다. HTTP 프로토콜은 아직 지원 되지 않습니다.

* MongoDB 계정에 대해 Azure Cosmos DB의 API를 사용 하는 경우 전용 끝점은 서버 버전 3.6의 계정에 대해서만 지원 됩니다. 즉, `*.mongo.cosmos.azure.com`형식의 끝점을 사용 하는 계정입니다. 서버 버전 3.2 (`*.documents.azure.com`형식의 끝점을 사용 하는 계정인 경우)의 계정에 대해서는 개인 링크가 지원 되지 않습니다. 개인 링크를 사용 하려면 이전 계정을 새 버전으로 마이그레이션해야 합니다.

* 개인 링크가 있는 MongoDB 계정에 대해 Azure Cosmos DB의 API를 사용 하는 경우 Robo 3T, 스튜디오 3T, Mongoose 등의 도구를 사용할 수 없습니다. AppName =<account name> 매개 변수가 지정 된 경우에만 끝점이 개인 링크를 지원할 수 있습니다. 예: replicaSet = globaldb & appName = mydbaccountname 이러한 도구는 연결 문자열의 앱 이름을 서비스에 전달 하지 않으므로 개인 링크를 사용할 수 없습니다. 그러나 3.6 버전의 SDK 드라이버를 사용 하 여 이러한 계정에 계속 액세스할 수 있습니다.

* Azure Cosmos 계정 및 Vnet에 대 한 개인 링크 지원은 특정 지역 에서만 사용할 수 있습니다. 지원 되는 지역 목록은 개인 링크 문서의 [사용 가능한 지역](../private-link/private-link-overview.md#availability) 섹션을 참조 하세요. **VNET 및 Azure Cosmos 계정은 모두 개인 끝점을 만들 수 있도록 지원 되는 지역에**있어야 합니다.

* 개인 링크를 포함 하는 경우 가상 네트워크를 이동 하거나 삭제할 수 없습니다.

* Azure Cosmos 계정은 개인 끝점에 연결 된 경우 삭제할 수 없습니다.

* Azure Cosmos 계정은 연결 된 모든 개인 끝점에 매핑되지 않은 지역으로 장애 조치 (failover) 할 수 없습니다. 자세한 내용은 이전 섹션에서 지역 추가 또는 제거를 참조 하세요.

* 관리자는 Azure Cosmos 계정 범위에서 "*/PrivateEndpointConnectionsApproval" 이상의 권한을 부여 받아야 자동으로 승인 된 개인 끝점을 만들 수 있습니다.

### <a name="private-dns-zone-integration-limitations"></a>사설 DNS 영역 통합 제한 사항

개인 끝점을 삭제 하거나 Azure Cosmos 계정의 지역이 제거 되 면 개인 DNS 영역의 DNS 레코드는 자동으로 제거 되지 않습니다. 이전에 DNS 레코드를 수동으로 제거 해야 합니다.

* 이 개인 DNS 영역에 연결 된 새 개인 끝점을 추가 합니다.
* 이 개인 DNS 영역에 연결 된 개인 끝점이 있는 모든 데이터베이스 계정에 새 영역을 추가 합니다.

DNS 레코드를 정리 하지 않으면 개인 끝점 제거 또는 지역 제거 후에 추가 된 지역에 대 한 데이터 중단 등 예기치 않은 데이터 평면 문제가 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다른 Azure Cosmos DB 보안 기능에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* Azure Cosmos DB에 대 한 방화벽을 구성 하려면 [방화벽 지원](firewall-support.md)을 참조 하세요.

* [Azure Cosmos 계정에 대 한 가상 네트워크 서비스 끝점을 구성 하는 방법입니다.](how-to-configure-vnet-service-endpoint.md)

* 개인 링크에 대 한 자세한 내용은 [Azure 개인 링크](../private-link/private-link-overview.md) 설명서를 참조 하세요.
