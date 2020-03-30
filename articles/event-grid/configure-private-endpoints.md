---
title: Azure 이벤트 그리드 토픽 또는 도메인에 대한 개인 끝점 구성
description: 이 문서에서는 Azure Event Grid 항목 또는 도메인에 대한 개인 끝점을 구성하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299908"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Azure 이벤트 그리드 토픽 또는 도메인에 대한 개인 끝점 구성(미리 보기)
[비공개 끝점을](../private-link/private-endpoint-overview.md) 사용하여 공용 인터넷을 [거치지](../private-link/private-link-overview.md) 않고도 가상 네트워크에서 주제 및 도메인으로 직접 이벤트를 안전하게 진행할 수 있습니다. 개인 끝점은 주제 또는 도메인에 대한 VNet 주소 공간의 IP 주소를 사용합니다. 자세한 개념 정보는 네트워크 [보안을](network-security.md)참조하십시오.

이 문서에서는 토픽 또는 도메인에 대한 개인 끝점을 구성하는 방법을 설명합니다.

> [!IMPORTANT]
> 비공개 엔드포인트 기능은 프리미엄 계층에서만 토픽 및 도메인에 사용할 수 있습니다. 기본 계층에서 프리미엄 계층으로 업그레이드하려면 [가격 책정 계층 업데이트](update-tier.md) 문서를 참조하세요. 

## <a name="use-azure-portal"></a>Azure Portal 사용 
이 섹션에서는 Azure Portal을 사용하여 토픽 또는 도메인에 대한 개인 끝점을 만드는 방법을 보여 주며 있습니다.

> [!NOTE]
> 이 섹션에 표시된 단계는 주로 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 개인 끝점을 만들 **수 있습니다.** 

1. [Azure 포털에](https://portal.azure.com) 로그인하고 토픽 또는 도메인으로 이동합니다.
2. 토픽 페이지의 **네트워킹** 탭으로 전환합니다. 도구 모음에서 **+ 비공개 끝점을** 선택합니다.

    ![개인 끝점 추가](./media/configure-private-endpoints/add-button.png)
2. 기본 페이지 중 하나는 다음 단계를 **따릅니다.** 
    1. 개인 끝점을 만들 려는 **Azure 구독을** 선택합니다. 
    2. 개인 끝점에 대한 **Azure 리소스 그룹을** 선택합니다. 
    3. 끝점에 대한 **이름을** 입력합니다. 
    4. 끝점의 **영역을** 선택합니다. 개인 끝점은 가상 네트워크와 동일한 지역에 있어야 하지만 개인 링크 리소스(이 예에서는 이벤트 그리드 항목)와 다른 지역에 있을 수 있습니다. 
    5. 그런 다음 다음: 페이지 하단의 리소스 >단추를 **선택합니다.** 

      ![개인 끝점 - 기본 페이지](./media/configure-private-endpoints/basics-page.png)
3. **리소스** 페이지에서 다음 단계를 수행합니다. 
    1. 연결 방법의 경우 **내 디렉터리에서 Azure 리소스에 연결을**선택하면 다음 단계를 따르십시오. 이 예제에서는 디렉터리에서 Azure 리소스에 연결하는 방법을 보여 줍니다. 
        1. **토픽/도메인이** 있는 **Azure 구독을** 선택합니다. 
        1. **리소스 유형의**경우 **리소스 유형에**대해 **Microsoft.EventGrid/항목** 또는 **Microsoft.EventGrid/도메인을** 선택합니다.
        2. **리소스의**경우 드롭다운 목록에서 토픽/도메인을 선택합니다. 
        3. **대상 하위 리소스가** 선택한 리소스 유형에 따라 **주제** 또는 **도메인으로** 설정되어 있는지 확인합니다.    
        4. 다음: 페이지 하단의 구성 >단추를 **선택합니다.** 

            ![개인 끝점 - 리소스 페이지](./media/configure-private-endpoints/resource-page.png)
    2. **리소스 ID 또는 별칭을 사용하여 리소스에 연결(연결)을**선택한 경우 다음 단계를 따르십시오.
        1. 리소스의 ID를 입력합니다. 예: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`  
        2. **리소스의**경우 **토픽** 또는 **도메인을**입력합니다. 
        3. (선택 사항) 요청 메시지를 추가합니다. 
        4. 다음: 페이지 하단의 구성 >단추를 **선택합니다.** 

            ![개인 끝점 - 리소스 페이지](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. **구성** 페이지에서 가상 네트워크에서 개인 끝점을 배포할 서브넷을 선택합니다. 
    1. 가상 **네트워크를**선택합니다. 현재 선택한 구독 및 위치의 가상 네트워크만 드롭다운 목록에 나열됩니다. 
    2. 선택한 가상 네트워크에서 **서브넷을** 선택합니다. 
    3. **다음:** 페이지 하단에 >버튼에 태그를 붙입니다. 

    ![개인 끝점 - 구성 페이지](./media/configure-private-endpoints/configuration-page.png)
5. **태그** 페이지에서 개인 끝점 리소스와 연결할 태그(이름 및 값)를 만듭니다. 그런 다음 페이지 하단에 **있는 검토 + 만들기** 버튼을 선택합니다. 
6. 검토 **+ 만들기**, 모든 설정을 검토하고 **만들기를** 선택하여 비공개 끝점을 만듭니다. 

    ![비공개 끝점 - 검토 & 페이지 만들기](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>프라이빗 링크 연결 관리

프라이빗 엔드포인트를 만들 때 연결이 승인되어야 합니다. 개인 끝점을 만드는 리소스가 디렉터리에 있는 경우 충분한 권한이 있는 경우 연결 요청을 승인할 수 있습니다. 다른 디렉터리의 Azure 리소스에 연결하는 경우 해당 리소스의 소유자가 연결 요청을 승인할 때까지 기다려야 합니다.

다음 네 가지 프로비저닝 상태가 있습니다.

| 서비스 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | 설명 |
|--|--|--|
| None | Pending | 연결은 수동으로 만들어지며 개인 링크 리소스 소유자의 승인을 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Private Link 리소스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | Private Link 리소스 소유자가 연결을 제거했습니다. 프라이빗 엔드포인트는 정보를 제공하므로 정리를 위해 삭제해야 합니다. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>개인 엔드포인트 연결을 관리하는 방법
다음 섹션에서는 개인 끝점 연결을 승인하거나 거부하는 방법을 보여 주며, 이 섹션에서는 개인 끝점 연결을 승인하거나 거부하는 방법을 보여 주며, 이 섹션에서는 

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 검색 표시줄에서 **이벤트 그리드 항목** 또는 이벤트 그리드 **도메인을 입력합니다.**
1. 관리할 **주제** 또는 **도메인을** 선택합니다.
1. 네트워킹 탭을 **선택합니다.**
1. 보류 중인 연결이 있는 경우 프로비저닝 상태에서 **보류 중으로** 나열된 연결이 표시됩니다. 

### <a name="to-approve-a-private-endpoint"></a>개인 끝점을 승인하려면
보류 중인 상태에 있는 개인 끝점을 승인할 수 있습니다. 승인하려면 다음 단계를 따르십시오. 

> [!NOTE]
> 이 섹션에 표시된 단계는 주로 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인의 개인 끝점을 승인할 수 **있습니다.** 

1. 승인할 **전용 끝점을** 선택하고 도구 모음에서 **승인을** 선택합니다.

    ![개인 끝점 - 보류 상태](./media/configure-private-endpoints/pending.png)
1. 연결 **승인** 대화 상자에서 주석(선택 사항)을 입력하고 **예**를 선택합니다. 

    ![비공개 끝점 - 승인](./media/configure-private-endpoints/approve.png)
1. 끝점의 상태가 **승인됨으로**표시되는지 확인합니다. 

    ![개인 끝점 - 승인된 상태](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>개인 끝점을 거부하려면
보류 상태 또는 승인된 상태에 있는 개인 끝점을 거부할 수 있습니다. 거부하려면 다음 단계를 따르십시오. 

> [!NOTE]
> 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 개인 끝점을 거부할 수 **있습니다.** 

1. 거부할 **전용 끝점을** 선택하고 도구 모음에서 **거부를** 선택합니다.

    ![프라이빗 엔드포인트 - 거부](./media/configure-private-endpoints/reject-button.png)
1. 연결 **거부** 대화 상자에서 주석(선택 사항)을 입력하고 **예**를 선택합니다. 

    ![프라이빗 엔드포인트 - 거부](./media/configure-private-endpoints/reject.png)
1. 끝점의 상태가 **거부됨으로**표시되는지 확인합니다. 

    ![개인 끝점 - 거부된 상태](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Azure 포털이 거부되면 개인 끝점을 승인할 수 없습니다. 


## <a name="use-azure-cli"></a>Azure CLI 사용
개인 끝점을 만들려면 다음 예제와 같이 [az 네트워크 개인 끝점 만들기](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 메서드를 사용합니다.

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

예제에서 사용된 매개 변수에 대한 설명은 [az 네트워크 개인 끝점 만들기에](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)대한 설명서를 참조하십시오. 이 예제에서 주목해야 할 몇 가지 사항은 다음과 같습니다. 

- `private-connection-resource-id`에 대 한 **항목** 또는 **도메인의**리소스 ID를 지정 합니다. 앞의 예제에서는 형식: 항목을 사용합니다.
- 을 `group-ids` `topic` 지정하거나 `domain`을 지정합니다. 앞의 예제에서는 `topic` 사용 됩니다. 

개인 끝점을 삭제하려면 다음 예제와 같이 [az 네트워크 개인 끝점 삭제](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) 방법을 사용합니다.

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 개인 끝점을 만들 **수 있습니다.** 

### <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기
다음은 다음과 같은 Azure 리소스를 만드는 샘플 스크립트입니다.

- Resource group
- 가상 네트워크
- 가상 네트워크의 서브넷
- Azure 이벤트 그리드 토픽(프리미엄 계층)
- 주제에 대한 개인 끝점

> [!NOTE]
> 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 개인 끝점을 만들 수 있습니다.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>개인 끝점 연결 승인
다음 샘플 CLI 코드 조각은 개인 끝점 연결을 승인하는 방법을 보여 주며 있습니다. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>개인 끝점 연결 거부
다음 샘플 CLI 코드 조각은 개인 끝점 연결을 거부하는 방법을 보여 주며 있습니다. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>PowerShell 사용
이 섹션에서는 PowerShell을 사용하여 토픽 또는 도메인에 대한 개인 끝점을 만드는 방법을 보여 주며, 이 섹션에서는 

### <a name="prerequisite"></a>필수 요소
[방법: 포털을 사용하여 리소스에 액세스하여](../active-directory/develop/howto-create-service-principal-portal.md) Azure Active Directory 응용 프로그램을 만들고 **디렉터리(테넌트) ID,** **응용 프로그램(클라이언트) ID**및 응용 **프로그램(클라이언트) 보안**에 대한 값을 기록할 수 있는 Azure AD 응용 프로그램 및 서비스 주체를 만듭니다. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 호출에 대한 토큰 및 헤더 준비 
REST API 호출 및 권한 부여 및 기타 헤더 정보와 함께 사용할 인증 토큰을 얻으려면 다음 필수 구성 조건 명령을 실행합니다. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>끝점 네트워크 정책을 사용하지 않도록 설정한 서브넷 만들기

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>비공개 끝점으로 이벤트 그리드 토픽 만들기

> [!NOTE]
> 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 개인 끝점을 만들 **수 있습니다.** 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

끝점이 만들어졌는지 확인하면 다음과 유사한 결과가 표시됩니다.

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>개인 끝점 연결 승인
다음 샘플 PowerShell 코드 조각은 개인 끝점을 승인하는 방법을 보여 주며 있습니다. 

> [!NOTE]
> 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인의 개인 끝점을 승인할 수 **있습니다.** 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>개인 끝점 연결 거부
다음 예제에서는 PowerShell을 사용하여 개인 끝점을 거부하는 방법을 보여 주며 있습니다. 이전 GET 명령의 결과에서 개인 끝점에 대한 GUID를 얻을 수 있습니다. 

> [!NOTE]
> 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 개인 끝점을 거부할 수 **있습니다.** 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

API를 통해 연결이 거부된 후에도 연결을 승인할 수 있습니다. Azure 포털을 사용하는 경우 거부된 끝점을 승인할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
IP 방화벽 설정을 구성하는 방법에 대해 알아보려면 [Azure Event Grid 항목 또는 도메인에 대한 IP 방화벽 구성을](configure-firewall.md)참조하십시오.