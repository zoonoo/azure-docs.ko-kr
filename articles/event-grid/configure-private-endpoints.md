---
title: Azure Event Grid 토픽 또는 도메인의 프라이빗 엔드포인트 구성
description: 이 문서에서는 Azure Event Grid 토픽 또는 도메인의 프라이빗 엔드포인트를 구성하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: df0c855a50bdb8f617513e635a021032a1ebf7da
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110706057"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Azure Event Grid 토픽 또는 도메인의 프라이빗 엔드포인트 구성
[프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 퍼블릭 인터넷을 통해 이동하지 않고도 [프라이빗 링크](../private-link/private-link-overview.md)를 통해 안전하게 가상 네트워크에서 토픽 및 도메인으로 직접 이벤트를 수신할 수 있습니다. 프라이빗 엔드포인트는 토픽 또는 도메인에 대해 VNet 주소 공간의 IP 주소를 사용합니다. 자세한 개념 정보는 [네트워크 보안](network-security.md)을 참조하세요.

이 문서에서는 토픽 또는 도메인에 대해 프라이빗 엔드포인트를 구성하는 방법을 설명합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용 
이 섹션에서는 Azure Portal을 사용하여 토픽 또는 도메인의 프라이빗 엔드포인트를 만드는 방법을 보여 줍니다.

> [!NOTE]
> 이 섹션에 표시된 단계는 대부분 토픽에 해당합니다. 비슷한 단계를 사용하여 **도메인** 의 프라이빗 엔드포인트를 만들 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 토픽 또는 도메인으로 이동합니다.
2. 토픽 페이지의 **네트워킹** 탭으로 전환합니다. 도구 모음에서 **+ 프라이빗 엔드포인트** 를 선택합니다.

    ![프라이빗 엔드포인트 추가](./media/configure-private-endpoints/add-button.png)
2. **기본** 페이지에서 다음 단계를 수행합니다. 
    1. 프라이빗 엔드포인트를 만들려는 **Azure 구독** 을 선택합니다. 
    2. 프라이빗 엔드포인트의 **Azure 리소스 그룹** 을 선택합니다. 
    3. 엔드포인트의 **이름** 을 입력합니다. 
    4. 엔드포인트의 **지역** 을 선택합니다. 프라이빗 엔드포인트는 가상 네트워크와 동일한 지역에 있어야 하지만 프라이빗 링크 리소스와는 다른 지역에 있을 수 있습니다(이 예제에서는 이벤트 그리드 토픽). 
    5. 그런 다음, 페이지 아래쪽에서 **다음: 리소스 >** 단추를 선택합니다. 

      ![프라이빗 엔드포인트 - 기본 페이지](./media/configure-private-endpoints/basics-page.png)
3. **리소스** 페이지에서 다음 단계를 수행합니다. 
    1. 연결 방법으로 **내 디렉터리에서 Azure 리소스에 연결** 을 선택하는 경우 다음 단계를 수행합니다. 이 예제에서는 디렉터리의 Azure 리소스에 연결하는 방법을 보여 줍니다. 
        1. **토픽/도메인** 이 있는 **Azure 구독** 을 선택합니다. 
        1. **리소스 종류** 의 경우 **리소스 종류** 에 대한 **Microsoft.EventGrid/topics** 또는 **Microsoft.EventGrid/domains** 를 선택합니다.
        2. **리소스** 의 경우 드롭다운 목록에서 토픽/도메인을 선택합니다. 
        3. 선택한 리소스 종류에 따라 **대상 하위 리소스** 가 **토픽** 또는 **도메인** 으로 설정되어 있는지 확인합니다.    
        4. 완료되면 **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다. 

            ![“프라이빗 엔드포인트 만들기 - 리소스” 페이지를 보여 주는 스크린샷](./media/configure-private-endpoints/resource-page.png)
    2. **리소스 ID 또는 별칭을 사용하여 리소스에 연결** 을 선택하는 경우 다음 단계를 수행합니다.
        1. 리소스의 ID를 입력합니다. 예를 들면 `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`와 같습니다.  
        2. **리소스** 에 대해 **토픽** 또는 **도메인** 을 입력합니다. 
        3. (선택 사항) 요청 메시지를 추가합니다. 
        4. 완료되면 **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다. 

            ![프라이빗 엔드포인트 - 리소스 페이지](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. **구성** 페이지에서 프라이빗 엔드포인트를 배포하려는 가상 네트워크의 서브넷을 선택합니다. 
    1. **가상 네트워크** 를 선택합니다. 드롭다운 목록에 현재 선택한 구독 및 위치의 가상 네트워크만 나열됩니다. 
    2. 선택한 가상 네트워크의 **서브넷** 을 선택합니다. 
    3. 완료되면 **다음: 태그 >** 단추를 페이지의 아래쪽에서 선택합니다. 

    ![프라이빗 엔드포인트 - 구성 페이지](./media/configure-private-endpoints/configuration-page.png)
5. **태그** 페이지에서 프라이빗 엔드포인트 리소스와 연결하려는 태그(이름 및 값)를 만듭니다. 그런 다음 페이지 아래쪽에서 **검토 + 만들기** 단추를 선택합니다. 
6. **검토 + 만들기** 에서 모든 설정을 검토하고 **만들기** 를 선택하여 프라이빗 엔드포인트를 만듭니다. 

    ![프라이빗 엔드포인트 - 검토 및 만들기 페이지](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>프라이빗 링크 연결 관리

프라이빗 엔드포인트를 만들 때 연결이 승인되어야 합니다. 프라이빗 엔드포인트를 만드는 리소스가 디렉터리에 있는 경우 충분한 사용 권한이 있다면 연결 요청을 승인할 수 있습니다. 다른 디렉터리의 Azure 리소스에 연결하는 경우 해당 리소스의 소유자가 연결 요청을 승인할 때까지 기다려야 합니다.

다음 네 가지 프로비저닝 상태가 있습니다.

| 서비스 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | Description |
|--|--|--|
| None | Pending | 연결이 수동으로 만들어지고, 프라이빗 링크 리소스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Private Link 리소스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | Private Link 리소스 소유자가 연결을 제거했습니다. 프라이빗 엔드포인트는 정보를 제공하므로 정리를 위해 삭제해야 합니다. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결을 관리하는 방법
다음 섹션에서는 프라이빗 엔드포인트 연결을 승인하거나 거부하는 방법을 보여 줍니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 표시줄에 **Event Grid 토픽** 또는 **Event Grid 도메인** 을 입력합니다.
1. 관리하려는 **토픽** 또는 **도메인** 을 선택합니다.
1. **네트워킹** 탭을 선택합니다.
1. 보류 중인 연결이 있으면 프로비저닝 상태가 **보류 중** 인 연결이 나열됩니다. 

### <a name="to-approve-a-private-endpoint"></a>프라이빗 엔드포인트를 승인하려면
보류 중 상태인 프라이빗 엔드포인트를 승인할 수 있습니다. 승인하려면 다음 단계를 수행합니다. 

> [!NOTE]
> 이 섹션에 표시된 단계는 대부분 토픽에 해당합니다. 비슷한 단계를 사용하여 **도메인** 의 프라이빗 엔드포인트를 승인할 수 있습니다. 

1. 승인하려는 **프라이빗 엔드포인트** 를 선택하고 도구 모음에서 **승인** 을 선택합니다.

    ![프라이빗 엔드포인트 - 보류 중 상태](./media/configure-private-endpoints/pending.png)
1. **연결 승인** 대화 상자에서 설명(선택 사항)을 입력하고 **예** 를 선택합니다. 

    ![프라이빗 엔드포인트 - 승인](./media/configure-private-endpoints/approve.png)
1. 엔드포인트 상태가 **승인됨** 으로 표시되는지 확인합니다. 

    ![프라이빗 엔드포인트 - 승인됨 상태](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>프라이빗 엔드포인트를 거부하려면
보류 중 상태 또는 승인됨 상태인 프라이빗 엔드포인트를 거부할 수 있습니다. 거부하려면 다음 단계를 수행합니다. 

> [!NOTE]
> 이 섹션에 표시된 단계는 토픽과 관련됩니다. 비슷한 단계를 사용하여 **도메인** 의 프라이빗 엔드포인트를 거부할 수 있습니다. 

1. 거부하려는 **프라이빗 엔드포인트** 를 선택하고 도구 모음에서 **거부** 를 선택합니다.

    ![“거부”가 선택된 “네트워킹 - 프라이빗 엔드포인트 연결”을 보여 주는 스크린샷](./media/configure-private-endpoints/reject-button.png)
1. **연결 거부** 대화 상자에서 설명(선택 사항)을 입력하고 **예** 를 선택합니다. 

    ![프라이빗 엔드포인트 - 거부](./media/configure-private-endpoints/reject.png)
1. 엔드포인트 상태가 **거부됨** 으로 표시되는지 확인합니다. 

    ![프라이빗 엔드포인트 - 거부됨 상태](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > 프라이빗 엔드포인트가 거부된 후에는 Azure Portal에서 프라이빗 엔드포인트를 승인할 수 없습니다. 


## <a name="use-azure-cli"></a>Azure CLI 사용
프라이빗 엔드포인트를 만들려면 다음 예제와 같이 [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) 메서드를 사용합니다.

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

예제에 사용된 매개 변수의 설명은 [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create)에 관한 설명서를 참조하세요. 이 예제에서는 주의해야 할 몇 가지 사항은 다음과 같습니다. 

- `private-connection-resource-id`의 경우 **토픽** 또는 **도메인** 의 리소스 ID를 지정합니다. 이전 예제에서는 토픽 형식을 사용합니다.
- `group-ids`의 경우 `topic` 또는 `domain`을 지정합니다. 앞의 예에서는 `topic`가 사용됩니다. 

프라이빗 엔드포인트를 삭제하려면 다음 예제와 같이 [az network private-endpoint delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) 메서드를 사용합니다.

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 이 섹션에 표시된 단계는 토픽과 관련됩니다. 비슷한 단계를 사용하여 **도메인** 의 프라이빗 엔드포인트를 만들 수 있습니다. 



### <a name="prerequisites"></a>사전 요구 사항
다음 명령을 실행하여 CLI의 Azure Event Grid 확장을 업데이트합니다. 

```azurecli-interactive
az extension update -n eventgrid
```

확장이 설치되지 않은 경우 다음 명령을 실행하여 확장을 설치합니다. 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기
프라이빗 엔드포인트를 만들려면 다음 예제와 같이 [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) 메서드를 사용합니다.

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

예제에 사용된 매개 변수의 설명은 [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create)에 관한 설명서를 참조하세요. 이 예제에서는 주의해야 할 몇 가지 사항은 다음과 같습니다. 

- `private-connection-resource-id`의 경우 **토픽** 또는 **도메인** 의 리소스 ID를 지정합니다. 이전 예제에서는 토픽 형식을 사용합니다.
- `group-ids`의 경우 `topic` 또는 `domain`을 지정합니다. 앞의 예에서는 `topic`가 사용됩니다. 

프라이빗 엔드포인트를 삭제하려면 다음 예제와 같이 [az network private-endpoint delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) 메서드를 사용합니다.

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 이 섹션에 표시된 단계는 토픽과 관련됩니다. 비슷한 단계를 사용하여 **도메인** 의 프라이빗 엔드포인트를 만들 수 있습니다. 

#### <a name="sample-script"></a>샘플 스크립트
여기에 다음 Azure 리소스를 만드는 샘플 스크립트가 있습니다.

- Resource group
- 가상 네트워크
- 가상 네트워크의 서브넷
- Azure Event Grid 토픽
- 토픽의 프라이빗 엔드포인트

> [!NOTE]
> 이 섹션에 표시된 단계는 토픽과 관련됩니다. 비슷한 단계를 사용하여 도메인의 프라이빗 엔드포인트를 만들 수 있습니다.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

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
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

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
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>프라이빗 엔드포인트 승인
다음 샘플 CLI 코드 조각은 프라이빗 엔드포인트 연결을 승인하는 방법을 보여 줍니다. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>프라이빗 엔드포인트 거부
다음 샘플 CLI 코드 조각은 프라이빗 엔드포인트 연결을 거부하는 방법을 보여 줍니다. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>공용 네트워크 액세스 사용 안 함
기본적으로 공용 네트워크 액세스는 Event Grid 토픽 또는 도메인에 대해 사용하도록 설정됩니다. 프라이빗 엔드포인트를 통해 액세스를 허용하려면 다음 명령을 실행하여 공용 네트워크 액세스를 사용하지 않도록 설정합니다.  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>PowerShell 사용
이 섹션에서는 PowerShell을 사용하여 토픽 또는 도메인의 프라이빗 엔드포인트를 만드는 방법을 보여 줍니다. 

### <a name="prerequisite"></a>필수 조건
[방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)의 지침에 따라 Azure Active Directory 애플리케이션을 만들고 **디렉터리(테넌트) ID**, **애플리케이션(클라이언트) ID** 및 **애플리케이션(클라이언트) 비밀** 의 값을 적어 둡니다. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 호출에 대한 토큰 및 헤더 준비 
다음 필수 조건 명령을 실행하여 REST API 호출 및 권한 부여에서 사용할 애플리케이션 토큰과 기타 헤더 정보를 가져옵니다. 

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

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>엔드포인트 네트워크 정책을 사용하지 않고 서브넷 만들기

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

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하여 이벤트 그리드 토픽 만들기

> [!NOTE]
> 이 섹션에 표시된 단계는 토픽과 관련됩니다. 비슷한 단계를 사용하여 **도메인** 의 프라이빗 엔드포인트를 만들 수 있습니다. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
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
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

엔드포인트가 생성되었는지 확인하는 경우 다음과 유사한 결과가 표시됩니다.

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

### <a name="approve-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인
다음 샘플 PowerShell 코드 조각은 프라이빗 엔드포인트를 승인하는 방법을 보여 줍니다. 

> [!NOTE]
> 이 섹션에 표시된 단계는 토픽과 관련됩니다. 비슷한 단계를 사용하여 **도메인** 의 프라이빗 엔드포인트를 승인할 수 있습니다. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 거부
다음 예제에서는 PowerShell을 사용하여 프라이빗 엔드포인트를 거부하는 방법을 보여 줍니다. 이전 GET 명령의 결과에서 프라이빗 엔드포인트의 GUID를 가져올 수 있습니다. 

> [!NOTE]
> 이 섹션에 표시된 단계는 토픽과 관련됩니다. 비슷한 단계를 사용하여 **도메인** 의 프라이빗 엔드포인트를 거부할 수 있습니다. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

API를 통해 거부된 후에도 연결을 승인할 수 있습니다. Azure Portal을 사용하는 경우에는 거부된 엔드포인트를 승인할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
* IP 방화벽 설정을 구성하는 방법에 관해 자세히 알아보려면 [Azure Event Grid 토픽 또는 도메인의 IP 방화벽 구성](configure-firewall.md)을 참조하세요.
* 네트워크 연결 문제를 해결하려면 [네트워크 연결 문제 해결](troubleshoot-network-connectivity.md)을 참조하세요.
