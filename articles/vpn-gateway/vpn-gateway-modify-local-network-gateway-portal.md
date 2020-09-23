---
title: 'VPN Gateway: 게이트웨이 IP 주소 설정 수정: Azure Portal'
description: 이 문서는 Azure Portal을 사용하여 로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 변경하는 방법을 안내합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983172"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure Portal을 사용하여 로컬 네트워크 게이트웨이 설정 수정

때로는 로컬 네트워크 게이트웨이 AddressPrefix 또는 GatewayIPAddress에 대한 설정을 변경합니다. 이 문서는 로컬 네트워크 게이트웨이 설정을 수정하는 방법을 안내합니다. 다음 목록에서 다른 옵션을 선택해 다른 방법으로 이러한 설정을 수정할 수도 있습니다.

연결을 삭제하기 전에 정의된 PSK를 가져오기 위해 연결 디바이스에 대한 구성을 다운로드할 수도 있습니다. 이런 방식으로 다른 쪽에서는 재정의할 필요가 없습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>로컬 네트워크 게이트웨이 구성

아래 스크린샷에서는 공용 IP 주소 끝점을 사용 하 여 로컬 네트워크 게이트웨이 리소스의 **구성** 페이지를 보여 줍니다.

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="로컬 네트워크 게이트웨이-IP 주소 구성":::

FQDN 끝점이 있는 동일한 구성 페이지입니다.

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="로컬 네트워크 게이트웨이 구성-FQDN":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>게이트웨이 IP 주소 수정

연결하려는 VPN 디바이스의 공용 IP 주소가 변경된 경우 해당 변경 내용을 반영하도록 로컬 네트워크 게이트웨이를 수정해야 합니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. **IP 주소** 상자에서 IP 주소를 수정합니다.
3. **저장**을 클릭하여 설정을 저장합니다.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>게이트웨이 FQDN 수정

연결 하려는 VPN 장치가 FQDN (정규화 된 도메인 이름)을 변경한 경우 해당 변경 내용을 반영 하도록 로컬 네트워크 게이트웨이를 수정 해야 합니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. **FQDN** 상자에서 도메인 이름을 수정 합니다.
3. **저장**을 클릭하여 설정을 저장합니다.

> ! 두고 FQDN 끝점과 IP 주소 끝점 사이에는 로컬 네트워크 게이트웨이를 변경할 수 없습니다. 이 로컬 네트워크 게이트웨이와 연결 된 모든 연결을 삭제 하 고 새 엔드포인트 (IP 주소 또는 FQDN)를 사용 하 여 새 연결을 만든 다음 연결을 다시 만들어야 합니다.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP 주소 접두사 수정

### <a name="to-add-additional-address-prefixes"></a>추가 주소 접두사를 추가하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. *추가 주소 범위 추가* 상자에 IP 주소 공간을 추가합니다.
3. **Save** 를 클릭하여 설정을 저장합니다.

### <a name="to-remove-address-prefixes"></a>주소 접두사를 제거하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. 제거하려는 접두사를 포함하는 줄에서 **‘...’** 를 클릭합니다.
3. **제거**를 클릭합니다.
4. **Save** 를 클릭하여 설정을 저장합니다.

## <a name="modify-bgp-settings"></a><a name="bgp"></a>BGP 설정 수정

### <a name="to-add-or-update-bgp-settings"></a>BGP 설정을 추가 하거나 업데이트 하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. 이 로컬 네트워크 게이트웨이에 대 한 BGP 구성을 표시 하거나 업데이트 하려면 **"bgp 설정 구성"** 을 선택 합니다.
3. 해당 필드에 자치 시스템 번호 또는 BGP 피어 IP 주소를 추가 하거나 업데이트 합니다.
4. **Save** 를 클릭하여 설정을 저장합니다.

### <a name="to-remove-bgp-settings"></a>BGP 설정을 제거 하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. **"Bgp 설정 구성"** 을 선택 취소 하 여 기존 BGP ASN 및 BGP 피어 IP 주소를 제거 합니다.
3. **Save** 를 클릭하여 설정을 저장합니다.

## <a name="next-steps"></a>다음 단계

게이트웨이 연결을 확인할 수 있습니다. [게이트웨이 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.
