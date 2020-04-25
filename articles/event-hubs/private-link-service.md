---
title: Azure 개인 링크 서비스와 Azure Event Hubs 통합
description: Azure 개인 링크 서비스와 Azure Event Hubs를 통합 하는 방법 알아보기
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: fb8fc93174345d0bdb09e4308a4206a65ed2270a
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148196"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Azure 개인 링크를 사용 하 여 Azure Event Hubs 통합 (미리 보기)
Azure 개인 링크 서비스를 사용 하면 가상 네트워크의 **개인 끝점** 을 통해 azure 서비스 (예: azure Event Hubs, Azure Storage 및 Azure Cosmos DB)와 azure에서 호스트 되는 고객/파트너 서비스에 액세스할 수 있습니다.

개인 끝점은 Azure 개인 링크를 통해 제공 되는 서비스에 비공개로 안전 하 게 연결 하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

자세한 내용은 [Azure Private Link란?](../private-link/private-link-overview.md)을 참조하세요.

> [!IMPORTANT]
> 이 기능은 **전용** 계층 에서만 지원 됩니다. 전용 계층에 대 한 자세한 내용은 [Event Hubs Dedicated 개요](event-hubs-dedicated-overview.md)를 참조 하세요. 
>
> 이 기능은 현재 **미리 보기**상태입니다. 

>[!WARNING]
> 개인 끝점을 사용 하도록 설정 하면 다른 Azure 서비스가 Event Hubs와 상호 작용 하지 않을 수 있습니다.
>
> 가상 네트워크를 사용 하는 경우 신뢰할 수 있는 Microsoft 서비스가 지원 되지 않습니다.
>
> Virtual Networks가 작동하지 않는 일반적인 Azure 시나리오(목록은 전체 목록이 **아님**) -
> - Azure Monitor (진단 설정)
> - Azure Stream Analytics
> - Azure Event Grid와 통합
> - Azure IoT Hub 경로
> - Azure IoT Device Explorer
>
> 다음 Microsoft 서비스는 가상 네트워크에 있어야 합니다.
> - Azure Web Apps
> - Azure 기능

## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure Portal를 사용 하 여 개인 끝점 추가

### <a name="prerequisites"></a>전제 조건

Event Hubs 네임 스페이스를 Azure 개인 링크와 통합 하려면 다음 엔터티 또는 권한이 필요 합니다.

- Event Hubs 네임스페이스
- Azure 가상 네트워크
- 가상 네트워크의 서브넷
- 네임 스페이스 및 가상 네트워크 둘 다에 대 한 소유자 또는 참가자 권한입니다.

프라이빗 엔드포인트와 가상 네트워크는 동일한 지역에 있어야 합니다. 포털을 사용하여 프라이빗 엔드포인트에 대한 영역을 선택하면 해당 지역에 있는 가상 네트워크만 자동으로 필터링됩니다. 네임 스페이스는 다른 지역에 있을 수 있습니다.

프라이빗 엔드포인트는 가상 네트워크에서 개인 IP 주소를 사용합니다.

### <a name="steps"></a>단계
Event Hubs 네임 스페이스가 이미 있는 경우 다음 단계에 따라 개인 링크 연결을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 검색 창에서 **event hubs**를 입력 합니다.
3. 개인 끝점을 추가 하려는 목록에서 **네임 스페이스** 를 선택 합니다.
4. **설정**아래에서 **네트워킹** 탭을 선택 합니다.
5. 페이지 맨 위에 있는 **개인 끝점 연결 (미리 보기)** 탭을 선택 합니다. Event Hubs 전용 계층을 사용 하지 않는 경우 **Event Hubs의 개인 끝점 연결은 전용 클러스터에서 만든 네임 스페이스 에서만 지원 됩니다**.
6. 페이지 맨 위에 있는 **+ 개인 끝점** 단추를 선택 합니다.

    ![이미지](./media/private-link-service/private-link-service-3.png)
7. **기본 사항** 페이지에서 다음 단계를 수행 합니다. 
    1. 개인 엔드포인트를 만들려는 **Azure 구독** 을 선택 합니다. 
    2. 개인 끝점 리소스에 대 한 **리소스 그룹** 을 선택 합니다.
    3. 개인 끝점의 **이름을** 입력 합니다. 
    5. 개인 끝점의 **지역을** 선택 합니다. 개인 끝점은 가상 네트워크와 동일한 지역에 있어야 하지만, 연결 하려는 개인 링크 리소스와 다른 지역에 있을 수 있습니다. 
    6. 페이지 맨 아래에 있는 **다음: 리소스 >** 단추를 선택 합니다.

        ![개인 끝점 만들기-기본 페이지](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **리소스** 페이지에서 다음 단계를 수행 합니다.
    1. 연결 방법의 경우 **내 디렉터리에서 Azure 리소스에 연결**을 선택 하는 경우 다음 단계를 수행 합니다. 
        1. **Event Hubs 네임 스페이스가** 있는 **Azure 구독** 을 선택 합니다. 
        2. **리소스 종류**에 대해 **리소스 종류**에 대해 **Microsoft EventHub/네임 스페이스** 를 선택 합니다.
        3. **리소스**의 경우 드롭다운 목록에서 Event Hubs 네임 스페이스를 선택 합니다. 
        4. **대상 하위 리소스가** **namespace**로 설정 되었는지 확인 합니다.
        5. 페이지 맨 아래에 있는 **다음: 구성 >** 단추를 선택 합니다. 
        
            ![개인 끝점 만들기-리소스 페이지](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. **리소스 ID 또는 별칭으로 Azure 리소스에 연결**을 선택 하는 경우 다음 단계를 수행 합니다.
        1. **리소스 ID** 또는 **별칭**을 입력 합니다. 다른 사람이 공유 하는 리소스 ID 또는 별칭 일 수 있습니다. 리소스 ID를 가져오는 가장 쉬운 방법은 Azure Portal에서 Event Hubs 네임 스페이스로 이동 하 여에서 `/subscriptions/`시작 하는 URI의 부분을 복사 하는 것입니다. 예제는 다음 이미지를 참조 하세요. 
        2. **대상 하위 리소스**에 대해 **네임 스페이스**를 입력 합니다. 개인 끝점에서 액세스할 수 있는 하위 리소스의 형식입니다.
        3. 필드 **요청 메시지**를 입력 하십시오. 리소스 소유자는 개인 끝점 연결을 관리 하는 동안이 메시지를 확인 합니다.
        4. 그런 다음 페이지 맨 아래에 있는 **다음: 구성 >** 단추를 선택 합니다.

            ![개인 끝점 만들기-리소스 ID를 사용 하 여 연결](./media/private-link-service/connect-resource-id.png)
9. **구성** 페이지에서 개인 끝점을 배포 하려는 가상 네트워크의 서브넷을 선택 합니다. 
    1. **가상 네트워크**를 선택 합니다. 현재 선택한 구독 및 위치에 있는 가상 네트워크만 드롭다운 목록에 나열 됩니다. 
    2. 선택한 가상 네트워크에서 **서브넷** 을 선택 합니다. 
    3. 페이지 맨 아래에 있는 **다음: 태그 >** 단추를 선택 합니다. 

        ![개인 끝점 만들기-구성 페이지](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **태그** 페이지에서 개인 끝점 리소스와 연결 하려는 태그 (이름 및 값)를 만듭니다. 그런 다음 페이지 아래쪽에서 **검토 + 만들기** 단추를 선택 합니다. 
11. **검토 + 만들기**에서 모든 설정을 검토 하 고 **만들기** 를 선택 하 여 개인 끝점을 만듭니다.
    
    ![개인 끝점 만들기-검토 및 만들기 페이지](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 만든 개인 끝점 연결이 끝점 목록에 표시 되는지 확인 합니다. 이 예제에서는 디렉터리의 Azure 리소스에 연결 하 고 충분 한 권한이 있으므로 개인 끝점이 자동으로 승인 됩니다. 

    ![만든 개인 끝점](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell을 사용 하 여 개인 끝점 추가
다음 예제에서는 Azure PowerShell를 사용 하 여 개인 끝점 연결을 만드는 방법을 보여 줍니다. 전용 클러스터를 만들지 않습니다. [이 문서의](event-hubs-dedicated-cluster-create-portal.md) 단계를 수행 하 여 전용 Event Hubs 클러스터를 만듭니다. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>개인 DNS 영역 구성
Event Hubs 도메인에 대 한 개인 DNS 영역을 만들고 가상 네트워크를 사용 하 여 연결 링크를 만듭니다.

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Azure Portal를 사용 하 여 개인 끝점 관리

프라이빗 엔드포인트를 만들 때 연결이 승인되어야 합니다. 개인 끝점을 만드는 리소스가 디렉터리에 있는 경우 충분 한 권한이 있는 경우 연결 요청을 승인할 수 있습니다. 다른 디렉터리의 Azure 리소스에 연결 하는 경우 해당 리소스의 소유자가 연결 요청을 승인할 때까지 기다려야 합니다.

다음 네 가지 프로비저닝 상태가 있습니다.

| 서비스 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | 설명 |
|--|--|--|
| 없음 | Pending | 연결이 수동으로 만들어지고, Private Link 리소스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Private Link 리소스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | Private Link 리소스 소유자가 연결을 제거했습니다. 프라이빗 엔드포인트는 정보를 제공하므로 정리를 위해 삭제해야 합니다. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>개인 끝점 연결 승인, 거부 또는 제거

1. Azure Portal에 로그인합니다.
2. 검색 창에서 **event hubs**를 입력 합니다.
3. 관리 하려는 **네임 스페이스** 를 선택 합니다.
4. **네트워킹** 탭을 선택 합니다.
5. 승인, 거부 또는 제거 하려는 작업에 따라 아래의 해당 섹션으로 이동 합니다.

### <a name="approve-a-private-endpoint-connection"></a>개인 끝점 연결 승인
1. 보류 중인 연결이 있는 경우 프로 비전 상태에 **보류 중** 으로 표시 된 연결이 표시 됩니다. 
2. 승인 하려는 **개인 끝점** 을 선택 합니다.
3. **승인** 단추를 선택 합니다.

    ![이미지](./media/private-link-service/approve-private-endpoint.png)
4. **연결 승인** 페이지에서 주석 (선택 사항)을 추가 하 고 **예**를 선택 합니다. 아니요를 선택 하면 아무 일도 발생 **하지**않습니다. 
5. 목록에서 개인 끝점 연결 상태가 **승인 됨**으로 변경 된 것을 볼 수 있습니다. 

### <a name="reject-a-private-endpoint-connection"></a>개인 끝점 연결 거부

1. 거부 하려는 개인 끝점 연결이 있는 경우 보류 중인 요청 또는 기존 연결 인지 여부에 관계 없이 연결을 선택 하 고 **거부** 단추를 클릭 합니다.

    ![이미지](./media/private-link-service/private-endpoint-reject-button.png)
2. **연결 거부** 페이지에서 설명 (선택 사항)을 입력 하 고 **예**를 선택 합니다. 아니요를 선택 하면 아무 일도 발생 **하지**않습니다. 
3. 목록에서 개인 끝점 연결의 상태가 **거부 됨으로**변경 된 것을 볼 수 있습니다. 

### <a name="remove-a-private-endpoint-connection"></a>개인 끝점 연결 제거

1. 개인 끝점 연결을 제거 하려면 목록에서 해당 연결을 선택 하 고 도구 모음에서 **제거** 를 선택 합니다.
2. **연결 삭제** 페이지에서 **예** 를 선택 하 여 개인 끝점의 삭제를 확인 합니다. 아니요를 선택 하면 아무 일도 발생 **하지**않습니다.
3. 상태가 **Disconnected**로 변경 된 것을 볼 수 있습니다. 그러면 목록에서 끝점이 사라지게 됩니다.

## <a name="validate-that-the-private-link-connection-works"></a>프라이빗 링크 연결이 작동하는지 확인

개인 끝점 리소스의 동일한 서브넷에 있는 리소스가 개인 IP 주소를 통해 Event Hubs 네임 스페이스에 연결 되어 있는지, 그리고 올바른 개인 DNS 영역 통합이 있는지 확인 해야 합니다.

먼저 [Azure Portal에서 Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)의 단계에 따라 가상 머신을 만듭니다.

**네트워킹** 탭에서 다음을 수행 합니다.

1. **가상 네트워크** 및 **서브넷**을 지정 합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 선택할 수 있습니다. 기존 가상 네트워크를 선택하는 경우 지역이 일치하는지 확인합니다.
1. **공용 IP** 리소스를 지정 합니다.
1. **NIC 네트워크 보안 그룹**에서 **없음**을 선택 합니다.
1. **부하 분산**에서 **아니요**를 선택 합니다.

명령줄을 열고 다음 명령을 실행합니다.

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Ns lookup 명령을 실행 하 여 공용 끝점에서 Event Hubs 네임 스페이스의 IP 주소를 확인 하는 경우 다음과 같은 결과가 표시 됩니다.

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Ns lookup 명령을 실행 하 여 개인 끝점에서 Event Hubs 네임 스페이스의 IP 주소를 확인 하는 경우 다음과 같은 결과가 표시 됩니다.

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>제한 사항 및 디자인 고려 사항

**가격**책정에 대 한 자세한 내용은 [Azure 개인 링크 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조 하세요.

**제한 사항**: Azure Event Hubs에 대 한 개인 끝점은 공개 미리 보기로 제공 됩니다. 이 기능은 모든 Azure 퍼블릭 지역에서 사용할 수 있습니다.

**Event Hubs 네임 스페이스 당 최대 개인 끝점 수**: 120.

자세한 내용은 [Azure 개인 링크 서비스: 제한 사항](../private-link/private-link-service-overview.md#limitations) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Private Link](../private-link/private-link-service-overview.md)에 대해 자세히 알아봅니다.
- [Azure Event Hubs](event-hubs-about.md) 에 대 한 자세한 정보
