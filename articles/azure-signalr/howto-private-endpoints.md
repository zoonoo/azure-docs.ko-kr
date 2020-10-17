---
title: 프라이빗 엔드포인트 사용
titleSuffix: Azure SignalR Service
description: 가상 네트워크에서 Azure SignalR Service에 안전 하 게 액세스 하기 위한 개인 끝점의 개요입니다.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 80369883b84ca30cae475235d41addcfba7e52e1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152337"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Azure SignalR Service에 대 한 개인 끝점 사용

Azure SignalR 서비스에 대 한 [개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 VNet (가상 네트워크)의 클라이언트가 [개인 링크](../private-link/private-link-overview.md)를 통해 안전 하 게 데이터에 액세스할 수 있도록 할 수 있습니다. 개인 끝점은 Azure SignalR 서비스에 대 한 VNet 주소 공간의 IP 주소를 사용 합니다. VNet 및 Azure SignalR 서비스의 클라이언트 간의 네트워크 트래픽은 Microsoft 백본 네트워크의 개인 링크를 통해 이동 하 여 공용 인터넷에서의 노출을 제거 합니다.

Azure SignalR 서비스에 대 한 개인 끝점을 사용 하면 다음을 수행할 수 있습니다.

- 네트워크 액세스 제어를 사용 하 여 azure SignalR 서비스를 보호 하 여 Azure SignalR Service에 대 한 공용 끝점의 모든 연결을 차단 합니다.
- Vnet에서 데이터의 반출을 차단할 수 있도록 하 여 vnet (가상 네트워크)에 대 한 보안을 강화 합니다.
- 개인 피어 링을 사용 하 여 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [연결할 expressroutes](../expressroute/expressroute-locations.md) 를 사용 하 여 VNet에 연결 하는 온-프레미스 네트워크에서 Azure SignalR 서비스에 안전 하 게 연결 합니다.

## <a name="conceptual-overview"></a>개념적 개요

![Azure SignalR Service에 대 한 개인 끝점 개요](media/howto-private-endpoints/private-endpoint-overview.png)

개인 끝점은 VNet ( [Virtual Network](../virtual-network/virtual-networks-overview.md) )의 Azure 서비스에 대 한 특별 한 네트워크 인터페이스입니다. Azure SignalR 서비스에 대 한 개인 끝점을 만들면 VNet과 서비스의 클라이언트 간에 보안 연결을 제공 합니다. 개인 끝점에는 VNet의 IP 주소 범위에서 IP 주소가 할당 됩니다. 개인 끝점과 Azure SignalR Service 간의 연결은 보안 개인 링크를 사용 합니다.

VNet의 응용 프로그램은 **다른 방법으로 사용 하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용 하 여**개인 끝점을 통해 Azure SignalR Service에 원활 하 게 연결할 수 있습니다. 개인 끝점은 REST API를 포함 하 여 Azure SignalR Service에서 지 원하는 모든 프로토콜과 함께 사용할 수 있습니다.

VNet에서 Azure SignalR 서비스에 대 한 개인 끝점을 만드는 경우 승인 요청이 Azure SignalR 서비스 소유자에 게 전송 됩니다. 개인 끝점의 생성을 요청 하는 사용자가 Azure SignalR 서비스의 소유자 이기도 한 경우이 동의 요청은 자동으로 승인 됩니다.

Azure SignalR 서비스 소유자는 [Azure Portal](https://portal.azure.com)에서 Azure SignalR 서비스에 대 한 '*개인 끝점*' 탭을 통해 동의 요청 및 개인 끝점을 관리할 수 있습니다.

> [!TIP]
> 개인 끝점을 통해서만 Azure SignalR 서비스에 대 한 액세스를 제한 하려는 경우 공용 끝점을 통해 액세스를 거부 하거나 제어 하도록 [네트워크 Access Control를 구성](howto-network-access-control.md#managing-network-access-control) 합니다.

### <a name="connecting-to-private-endpoints"></a>전용 끝점에 연결

개인 끝점을 사용 하는 VNet의 클라이언트는 공용 끝점에 연결 하는 클라이언트와 동일한 Azure SignalR 서비스에 대 한 연결 문자열을 사용 해야 합니다. DNS 확인을 사용 하 여 개인 링크를 통해 VNet에서 Azure SignalR 서비스로의 연결을 자동으로 라우팅합니다.

> [!IMPORTANT]
> 다른 방법으로는 동일한 연결 문자열을 사용 하 여 개인 끝점을 사용 하 여 Azure SignalR Service에 연결 합니다. 하위 도메인 URL을 사용 하 여 Azure SignalR Service에 연결 하지 마세요 `privatelink` .

기본적으로 개인 끝점에 대 한 필수 업데이트를 사용 하 여 VNet에 연결 된 [개인 DNS 영역](../dns/private-dns-overview.md) 을 만듭니다. 그러나 사용자 고유의 DNS 서버를 사용 하는 경우 DNS 구성을 추가로 변경 해야 할 수 있습니다. 아래 [DNS 변경](#dns-changes-for-private-endpoints) 에 대 한 섹션에서는 개인 끝점에 필요한 업데이트에 대해 설명 합니다.

## <a name="dns-changes-for-private-endpoints"></a>전용 끝점에 대 한 DNS 변경

개인 끝점을 만들 때 Azure SignalR 서비스에 대 한 DNS CNAME 리소스 레코드는 접두사가 있는 하위 도메인의 별칭으로 업데이트 됩니다 `privatelink` . 또한 기본적으로 개인 끝점에 대 한 DNS A 리소스 레코드를 사용 하 여 하위 도메인에 해당 하는 [개인 DNS 영역](../dns/private-dns-overview.md)을 만듭니다 `privatelink` .

개인 끝점을 사용 하 여 VNet 외부에서 Azure SignalR 서비스 도메인 이름을 확인 하면 Azure SignalR 서비스의 공용 끝점으로 확인 됩니다. 개인 끝점을 호스트 하는 VNet에서 확인 되 면 도메인 이름이 개인 끝점의 IP 주소로 확인 됩니다.

위의 예에서는 개인 끝점을 호스트 하는 VNet 외부에서 확인 되는 Azure SignalR 서비스 ' foobar '에 대 한 DNS 리소스 레코드는 다음과 같습니다.

| Name                                                  | Type  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

앞에서 설명한 것 처럼 네트워크 액세스 제어를 사용 하 여 공용 끝점을 통해 VNet 외부의 클라이언트에 대 한 액세스를 거부 하거나 제어할 수 있습니다.

' Foobar '에 대 한 DNS 리소스 레코드는 개인 끝점을 호스트 하는 VNet의 클라이언트에서 확인 되는 경우 다음과 같습니다.

| Name                                                  | Type  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

이 접근 방식을 사용 하면 개인 끝점을 호스트 하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 **동일한 연결 문자열을 사용 하 여** Azure SignalR Service에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용 하는 경우 클라이언트는 개인 끝점 IP 주소에 대 한 Azure SignalR Service 끝점의 FQDN을 확인할 수 있어야 합니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임 하도록 DNS 서버를 구성 하거나 `foobar.privatelink.service.signalr.net` 개인 끝점 IP 주소를 사용 하 여에 대 한 A 레코드를 구성 해야 합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용 하는 경우 하위 도메인의 Azure SignalR 서비스 이름을 `privatelink` 개인 끝점 IP 주소로 확인 하도록 DNS 서버를 구성 해야 합니다. 이렇게 하려면 하위 `privatelink` 도메인을 VNet의 개인 DNS 영역에 위임 하거나 dns 서버에서 dns 영역을 구성 하 고 Dns A 레코드를 추가 합니다.

Azure SignalR Service에 대 한 개인 끝점의 권장 DNS 영역 이름은 `privatelink.service.signalr.net` 입니다.

전용 끝점을 지원 하기 위해 자체 DNS 서버를 구성 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure 가상 네트워크의 리소스 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [전용 끝점에 대 한 DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Azure Portal에서 새 Azure SignalR 서비스와 함께 개인 끝점을 만듭니다.

1. 새 Azure SignalR 서비스를 만들 때 **네트워킹** 탭을 선택 합니다. 연결 방법으로 **개인 끝점** 을 선택 합니다.

    ![Azure SignalR 서비스 만들기-네트워킹 탭](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. **추가**를 클릭합니다. 새 개인 끝점에 대 한 구독, 리소스 그룹, 위치, 이름을 입력 합니다. 가상 네트워크 및 서브넷을 선택 합니다.

    ![Azure SignalR Service 만들기-개인 끝점 추가](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. **검토 + 만들기**를 클릭합니다.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Azure Portal에서 기존 Azure SignalR 서비스에 대 한 개인 끝점을 만듭니다.

1. Azure SignalR 서비스로 이동 합니다.

1. **개인 끝점 연결**이라고 하는 설정 메뉴를 클릭 합니다.

1. 위쪽의 단추 **+ 개인 끝점** 을 클릭 합니다.

    ![개인 끝점 연결 블레이드](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. 새 개인 끝점에 대 한 구독, 리소스 그룹, 리소스 이름 및 지역을 입력 합니다.
    
    ![개인 끝점 만들기-기본 사항](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. 대상 Azure SignalR Service 리소스를 선택 합니다.

    ![개인 끝점 만들기-리소스](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. 대상 가상 네트워크 선택

    ![개인 끝점 만들기-구성](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. **검토 + 만들기**를 클릭합니다.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기

1. Azure CLI에 로그인
    ```console
    az login
    ```
1. Azure 구독 선택
    ```console
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. 새 리소스 그룹 만들기
    ```console
    az group create -n {RG} -l {AZURE REGION}
    ```
1. SignalRService를 공급자로 등록
    ```console
    az provider register -n Microsoft.SignalRService
    ```
1. 새 Azure SignalR Service 만들기
    ```console
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Virtual Network 만들기
    ```console
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. 서브넷 추가
    ```console
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Virtual Network 정책 사용 안 함
    ```console
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. 프라이빗 DNS 영역 추가
    ```console
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Virtual Network에 프라이빗 DNS 영역 연결
    ```console
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. 프라이빗 엔드포인트 만들기(자동으로 승인)
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. 프라이빗 엔드포인트 만들기(수동으로 승인 요청)
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. 연결 상태 표시
    ```console
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

Azure SignalR Service에 대 한 개인 끝점에 대 한 다음과 같은 알려진 문제를 염두에 두어야 합니다.

### <a name="free-tier"></a>무료 계층

무료 계층 Azure SignalR 서비스에 대 한 개인 끝점을 만들 수 없습니다.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>전용 끝점을 사용 하 여 Vnet의 클라이언트에 대 한 액세스 제약 조건

기존 개인 끝점을 사용 하는 Vnet의 클라이언트는 개인 끝점이 있는 다른 Azure SignalR 서비스 인스턴스에 액세스할 때 제약 조건을 사용 합니다. 예를 들어 VNet N1에는 Azure SignalR Service 인스턴스 s 1에 대 한 개인 끝점이 있다고 가정 합니다. Azure SignalR Service S2에서 VNet N2에 개인 끝점이 있는 경우 VNet N1의 클라이언트는 개인 끝점을 사용 하 여 Azure SignalR Service S2에도 액세스 해야 합니다. Azure SignalR Service S2에 개인 끝점이 없는 경우 VNet N1의 클라이언트는 개인 끝점 없이 해당 계정에서 Azure SignalR Service에 액세스할 수 있습니다.

이 제약 조건은 Azure SignalR Service S2가 개인 끝점을 만들 때 적용 되는 DNS 변경의 결과입니다.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 서브넷의 네트워크 보안 그룹 규칙

현재 개인 끝점에 대 한 nsg ( [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md) ) 규칙 및 사용자 정의 경로를 구성할 수 없습니다. 개인 끝점을 호스트 하는 서브넷에 적용 된 NSG 규칙은 개인 끝점에 적용 됩니다. 이 문제에 대 한 제한 된 해결 방법은 원본 서브넷의 개인 끝점에 대 한 액세스 규칙을 구현 하는 것입니다. 단,이 방법에는 더 높은 관리 오버 헤드가 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [네트워크 Access Control 구성](howto-network-access-control.md)