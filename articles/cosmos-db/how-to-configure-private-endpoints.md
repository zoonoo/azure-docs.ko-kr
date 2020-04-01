---
title: Azure 코스모스 계정에 대한 Azure 개인 링크 구성
description: 가상 네트워크에서 개인 IP 주소를 사용하여 Azure Cosmos 계정에 액세스하도록 Azure 개인 링크를 설정하는 방법에 대해 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 9a6a1560e169c51256c198868dc7293a020189f4
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421429"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Azure 코스모스 계정에 대한 Azure 개인 링크 구성

Azure 개인 링크를 사용하여 개인 끝점을 통해 Azure Cosmos 계정에 연결할 수 있습니다. 개인 끝점은 가상 네트워크 내의 서브넷에 있는 개인 IP 주소 집합입니다. 그런 다음 개인 IP 주소를 통해 Azure Cosmos 계정에 대한 액세스를 제한할 수 있습니다. 개인 링크가 제한된 NSG 정책과 결합되면 데이터 유출 위험을 줄이는 데 도움이 됩니다. 개인 끝점에 대한 자세한 내용은 [Azure 개인 링크](../private-link/private-link-overview.md) 문서를 참조하세요.

개인 링크를 사용하면 사용자가 가상 네트워크 내에서 또는 피어링된 가상 네트워크에서 Azure Cosmos 계정에 액세스할 수 있습니다. 프라이빗 링크에 매핑된 리소스는 VPN 또는 Azure ExpressRoute를 통해 개인 피어링을 통해 온-프레미스에 액세스할 수도 있습니다. 

자동 또는 수동 승인 방법을 사용하여 개인 링크로 구성된 Azure Cosmos 계정에 연결할 수 있습니다. 자세한 내용은 개인 링크 설명서의 [승인 워크플로](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) 섹션을 참조하십시오. 

이 문서에서는 개인 끝점을 만드는 단계를 설명합니다. 자동 승인 방법을 사용하고 있다고 가정합니다.

> [!NOTE]
> 개인 엔드포인트 지원은 현재 게이트웨이 연결 모드에서만 일반적으로 사용할 수 있습니다. 직접 모드의 경우 미리 보기 기능으로 사용할 수 있습니다.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Azure 포털을 사용하여 개인 끝점 만들기

다음 단계를 사용하여 Azure 포털을 사용하여 기존 Azure Cosmos 계정에 대한 개인 끝점을 만듭니다.

1. 모든 **리소스** 창에서 Azure 코스모스 계정을 선택합니다.

1. 설정 목록에서 **비공개 끝점 연결을** 선택한 다음 **비공개 끝점을**선택합니다.

   ![Azure 포털에서 개인 끝점을 만들기 위한 선택](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. 전용 **끝점 만들기 - 기본** 창에서 다음 세부 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | 리소스 그룹을 선택합니다.|
    | **인스턴스 세부 정보** |  |
    | 속성 | 개인 끝점에 대한 이름을 입력합니다. 이 이름을 가져온 경우 고유한 이름을 만듭니다. |
    |지역| 비공개 링크를 배포할 지역을 선택합니다. 가상 네트워크가 있는 동일한 위치에 개인 끝점을 만듭니다.|
    |||
1. **다음 을 선택합니다.**
1. **프라이빗 엔드포인트 만들기 - 리소스**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | **내 디렉터리에서 Azure 리소스에 연결 선택**합니다. <br/><br/> 그런 다음 리소스 중 하나를 선택하여 비공개 링크를 설정할 수 있습니다. 또는 사용자가 공유한 리소스 ID 또는 별칭을 사용하여 다른 사람의 리소스에 연결할 수 있습니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **마이크로소프트.AzureCosmosDB/데이터베이스 계정을 선택합니다.** |
    | 리소스 |Azure 코스모스 계정을 선택합니다. |
    |대상 하위 리소스 |매핑할 Azure 코스모스 DB API 유형을 선택합니다. 기본값은 SQL, MongoDB 및 Cassandra API에 대해 하나의 선택 항목만 선택합니다. 그렘린 및 테이블 API의 경우 이러한 API가 SQL API와 상호 운용가능하기 때문에 **Sql을** 선택할 수도 있습니다. |
    |||

1. **다음 을 선택합니다: 구성.**
1. **개인 끝점 만들기 - 구성에서**다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| 가상 네트워크를 선택합니다. |
    | 서브넷 | 서브넷을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. <br><br/> 개인 끝점에 비공개로 연결하려면 DNS 레코드가 필요합니다. 개인 끝점을 개인 DNS 영역과 통합하는 것이 좋습니다. 가상 컴퓨터에서 호스트 파일을 사용하여 사용자 고유의 DNS 서버를 사용하거나 DNS 레코드를 만들 수도 있습니다. |
    |프라이빗 DNS 영역 |**privatelink.documents.azure.com**선택합니다. <br><br/> 개인 DNS 영역은 자동으로 결정됩니다. Azure 포털을 사용 하 여 변경할 수 없습니다.|
    |||

1. **검토 + 만들기를**선택합니다. 검토 **+ 만들기** 페이지에서 Azure는 구성의 유효성을 검사합니다.
1. 유효성 검사 전달 메시지가 표시되면 **에서 만들기를** **선택합니다.**

Azure Cosmos 계정에 대한 개인 링크를 승인한 경우 Azure 포털에서 **방화벽 및 가상 네트워크** 창의 모든 **네트워크** 옵션을 사용할 수 없습니다.

다음 표에서는 서로 다른 Azure Cosmos 계정 API 형식, 지원되는 하위 리소스 및 해당 개인 영역 이름 간의 매핑을 보여 둡니다. SQL API를 통해 Gremlin 및 Table API 계정에 액세스할 수도 있으므로 이러한 API에 대한 두 가지 항목이 있습니다.

|Azure 코스모스 계정 API 유형  |지원되는 하위 리소스(또는 그룹 아이디) |개인 영역 이름  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|테이블    |    테이블     |   privatelink.table.cosmos.azure.com    |
|테이블     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>개인 IP 주소 가져오기

개인 끝점을 프로비전한 후 IP 주소를 쿼리할 수 있습니다. Azure 포털에서 IP 주소를 보려면 다음을 수행하십시오.

1. **모든 리소스**를 선택합니다.
1. 이전에 만든 개인 끝점을 검색합니다. 이 경우 **cdbPrivateEndpoint3**입니다.
1. **개요** 탭을 선택하여 DNS 설정 및 IP 주소를 확인합니다.

![Azure 포털의 개인 IP 주소](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

개인 끝점당 여러 IP 주소가 만들어집니다.

* Azure Cosmos 계정의 전역(지역과 불가지론) 끝점에 대한 하나
* Azure 코스모스 계정이 배포되는 각 지역에 대해 하나씩

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 개인 끝점 만들기

다음 PowerShell 스크립트를 실행하여 기존 Azure Cosmos 계정에 대해 "MyPrivateEndpoint"라는 개인 끝점을 만듭니다. 변수 값을 환경에 대한 세부 정보로 바꿉습니다.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>프라이빗 엔드포인트와 프라이빗 DNS 영역 통합

개인 끝점을 만든 후 다음 PowerShell 스크립트를 사용하여 개인 DNS 영역과 통합할 수 있습니다.

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>개인 IP 주소 가져오기

개인 끝점을 프로비전한 후 다음 PowerShell 스크립트를 사용하여 IP 주소와 FQDN 매핑을 쿼리할 수 있습니다.

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Azure CLI를 사용하여 개인 끝점 만들기

다음 Azure CLI 스크립트를 실행하여 기존 Azure Cosmos 계정에 대해 "myPrivateEndpoint"라는 개인 끝점을 만듭니다. 변수 값을 환경에 대한 세부 정보로 바꿉습니다.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>프라이빗 엔드포인트와 프라이빗 DNS 영역 통합

개인 끝점을 만든 후 다음 Azure CLI 스크립트를 사용하여 개인 DNS 영역과 통합할 수 있습니다.

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 개인 끝점 만들기

가상 네트워크 서브넷에서 개인 끝점을 만들어 비공개 링크를 설정할 수 있습니다. Azure 리소스 관리자 템플릿을 사용하여 이 작업을 수행할 수 있습니다.

다음 코드를 사용하여 "PrivateEndpoint_template.json"이라는 리소스 관리자 템플릿을 만듭니다. 이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos SQL API 계정에 대한 개인 끝점을 만듭니다.

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

**템플릿에 대한 매개 변수 파일 정의**

템플릿에 대한 매개 변수 파일을 만들고 "PrivateEndpoint_parameters.json"이라고 지정합니다. 매개 변수 파일에 다음 코드를 추가합니다.

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

**PowerShell 스크립트를 사용하여 템플릿 배포**

다음 코드를 사용하여 PowerShell 스크립트를 만듭니다. 스크립트를 실행하기 전에 구독 ID, 리소스 그룹 이름 및 기타 변수 값을 환경에 대한 세부 정보로 바꿉니다.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
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

PowerShell 스크립트에서 변수에는 `GroupId` 하나의 값만 포함될 수 있습니다. 이 값은 계정의 API 유형입니다. 허용된 값은 `MongoDB` `Cassandra`" `Gremlin` `Sql`, `Table`" 및 . 일부 Azure Cosmos 계정 유형은 여러 API를 통해 액세스할 수 있습니다. 다음은 그 예입니다.

* 그렘린 API 계정은 그렘린 및 SQL API 계정 모두에서 액세스할 수 있습니다.
* 테이블 API 계정은 테이블 및 SQL API 계정 모두에서 액세스할 수 있습니다.

이러한 계정의 경우 각 API 유형에 대해 하나의 개인 끝점을 만들어야 합니다. 해당 API 형식은 배열에 지정됩니다. `GroupId`

템플릿을 성공적으로 배포한 후 다음 이미지와 유사한 출력을 볼 수 있습니다. 이 `provisioningState` 값은 `Succeeded` 전용 끝점이 올바르게 설정된 경우입니다.

![리소스 관리자 템플릿에 대한 배포 출력](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

템플릿을 배포한 후 개인 IP 주소는 서브넷 내에서 예약됩니다. Azure Cosmos 계정의 방화벽 규칙은 개인 끝점에서만 연결을 허용하도록 구성됩니다.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>프라이빗 엔드포인트와 프라이빗 DNS 존 통합

다음 코드를 사용하여 "PrivateZone_template.json"이라는 리소스 관리자 템플릿을 만듭니다. 이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos SQL API 계정에 대한 개인 DNS 영역을 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

다음 코드를 사용하여 "PrivateZoneRecords_template.json"이라는 리소스 관리자 템플릿을 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**템플릿에 대한 매개 변수 파일 정의**

템플릿에 대해 다음 두 매개 변수 파일을 만듭니다. "PrivateZone_parameters.json"을 만듭니다. 다음 코드와 바꿉니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

"PrivateZoneRecords_parameters.json"을 만듭니다. 다음 코드와 바꿉니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**PowerShell 스크립트를 사용하여 템플릿 배포**

다음 코드를 사용하여 PowerShell 스크립트를 만듭니다. 스크립트를 실행하기 전에 구독 ID, 리소스 그룹 이름 및 기타 변수 값을 환경에 대한 세부 정보로 바꿉니다.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
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

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>사용자 지정 DNS 구성

개인 끝점을 만든 서브넷 내에서 개인 DNS 영역을 사용해야 합니다. 각 개인 IP 주소가 DNS 항목에 매핑되도록 끝점을 구성합니다. (앞에서 `fqdns` 보여 준 응답에서 속성을 참조하십시오.)

개인 끝점을 만들 때 Azure의 개인 DNS 영역과 통합할 수 있습니다. 대신 사용자 지정 DNS 영역을 사용하도록 선택한 경우 개인 끝점에 예약된 모든 개인 IP 주소에 대한 DNS 레코드를 추가하도록 구성해야 합니다.

## <a name="private-link-combined-with-firewall-rules"></a>방화벽 규칙과 결합된 비공개 링크

방화벽 규칙과 함께 비공개 링크를 사용할 때 다음과 같은 상황과 결과가 발생할 수 있습니다.

* 방화벽 규칙을 구성하지 않으면 기본적으로 모든 트래픽이 Azure Cosmos 계정에 액세스할 수 있습니다.

* 공용 트래픽 또는 서비스 끝점을 구성하고 개인 끝점을 만드는 경우 다른 유형의 들어오는 트래픽은 해당 유형의 방화벽 규칙에 의해 승인됩니다.

* 공용 트래픽 또는 서비스 끝점을 구성하지 않고 개인 끝점을 만드는 경우 Azure Cosmos 계정은 개인 끝점을 통해서만 액세스할 수 있습니다. 공용 트래픽 또는 서비스 끝점을 구성하지 않으면 승인된 모든 비공개 끝점이 거부되거나 삭제된 후 계정이 전체 네트워크에 열려 있습니다.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>영역을 추가하거나 제거할 때 비공개 끝점 업데이트

Azure Cosmos 계정에 영역을 추가하거나 제거하려면 해당 계정에 대한 DNS 항목을 추가하거나 제거해야 합니다. 영역이 추가되거나 제거된 후 추가되거나 제거된 DNS 항목과 해당 개인 IP 주소를 반영하도록 서브넷의 개인 DNS 영역을 업데이트할 수 있습니다.

예를 들어 Azure Cosmos 계정을 "미국 서부", "미국 중부" 및 "유럽 서부"의 세 지역에 배포한다고 가정해 보겠습니다. 계정에 대한 개인 끝점을 만들 면 4개의 개인 IP가 서브넷에 예약됩니다. 세 지역 각각에 대해 하나의 IP가 있으며 전역/지역별 끝점에 대해 하나의 IP가 있습니다.

나중에 Azure Cosmos 계정에 새 지역(예: "미국 동부")을 추가할 수 있습니다. 새 지역을 추가한 후 개인 DNS 영역 또는 사용자 지정 DNS에 해당 DNS 레코드를 추가해야 합니다.

지역을 제거할 때도 동일한 단계를 사용할 수 있습니다. 영역을 제거한 후 개인 DNS 영역 또는 사용자 지정 DNS에서 해당 DNS 레코드를 제거해야 합니다.

## <a name="current-limitations"></a>현재 제한 사항

Azure Cosmos 계정으로 개인 링크를 사용하는 경우 다음과 같은 제한 사항이 적용됩니다.

* 직접 모드 연결을 사용하여 Azure Cosmos 계정과 개인 링크를 사용하는 경우 TCP 프로토콜만 사용할 수 있습니다. HTTP 프로토콜은 아직 지원되지 않습니다.

* 개인 엔드포인트 지원은 현재 게이트웨이 연결 모드에서만 일반적으로 사용할 수 있습니다. 직접 모드의 경우 미리 보기 기능으로 사용할 수 있습니다.

* MongoDB 계정에 Azure Cosmos DB의 API를 사용하는 경우 서버 버전 3.6의 계정(즉, 형식의 `*.mongo.cosmos.azure.com`끝점을 사용하는 계정)에 대해서만 개인 끝점이 지원됩니다. 개인 링크는 서버 버전 3.2의 계정에 대해 지원되지 않습니다(즉, `*.documents.azure.com`형식의 끝점을 사용하는 계정). 비공개 링크를 사용하려면 이전 계정을 새 버전으로 마이그레이션해야 합니다.

* 프라이빗 링크가 있는 MongoDB 계정에 Azure Cosmos DB의 API를 사용하는 경우 로보 3T, 스튜디오 3T 및 몽구스와 같은 도구를 사용할 수 없습니다. 매개 변수가 지정된 경우에만 끝점에 `appName=<account name>` 개인 링크가 지원될 수 있습니다. 예제는 `replicaSet=globaldb&appName=mydbaccountname`입니다. 이러한 도구는 연결 문자열의 앱 이름을 서비스에 전달하지 않으므로 비공개 링크를 사용할 수 없습니다. 그러나 3.6 버전의 SDK 드라이버를 사용하여 이러한 계정에 계속 액세스할 수 있습니다.

* 비공개 링크가 포함된 경우 가상 네트워크를 이동하거나 삭제할 수 없습니다.

* Azure Cosmos 계정이 개인 끝점에 연결된 경우 삭제할 수 없습니다.

* Azure Cosmos 계정을 계정에 연결된 모든 개인 끝점에 매핑되지 않은 리전에 장애 조치할 수 없습니다.

* 네트워크 관리자는 Azure Cosmos 계정 범위에서 최소한 "*/PrivateEndpointConnections승인" 권한을 부여하여 자동으로 승인된 개인 끝점을 만들어야 합니다.

### <a name="limitations-to-private-dns-zone-integration"></a>개인 DNS 영역 통합에 대한 제한 사항

개인 DNS 영역의 DNS 레코드는 개인 끝점을 삭제하거나 Azure Cosmos 계정에서 영역을 제거할 때 자동으로 제거되지 않습니다. 먼저 DNS 레코드를 수동으로 제거해야 합니다.

* 이 개인 DNS 영역에 연결된 새 개인 끝점을 추가합니다.
* 이 개인 DNS 영역에 연결된 개인 끝점이 있는 데이터베이스 계정에 새 영역을 추가합니다.

DNS 레코드를 정리하지 않으면 예기치 않은 데이터 평면 문제가 발생할 수 있습니다. 이러한 문제에는 개인 끝점 제거 또는 지역 제거 후 추가된 지역에 대한 데이터 중단이 포함됩니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB 보안 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

* Azure Cosmos DB에 대한 방화벽을 구성하려면 [방화벽 지원을](firewall-support.md)참조하십시오.

* Azure Cosmos 계정에 대한 가상 네트워크 서비스 끝점을 구성하는 방법을 알아보려면 [가상 네트워크에서 액세스 구성 을](how-to-configure-vnet-service-endpoint.md)참조하십시오.

* 개인 링크에 대한 자세한 내용은 [Azure 개인 링크](../private-link/private-link-overview.md) 설명서를 참조하십시오.
