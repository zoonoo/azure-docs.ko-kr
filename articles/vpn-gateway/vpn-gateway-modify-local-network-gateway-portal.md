---
title: 'VPN 게이트웨이: 게이트웨이 IP 주소 설정 수정: Azure 포털'
description: 이 문서는 Azure Portal을 사용하여 로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 변경하는 방법을 안내합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864029"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure Portal을 사용하여 로컬 네트워크 게이트웨이 설정 수정

때로는 로컬 네트워크 게이트웨이 AddressPrefix 또는 GatewayIPAddress에 대한 설정을 변경합니다. 이 문서는 로컬 네트워크 게이트웨이 설정을 수정하는 방법을 안내합니다. 다음 목록에서 다른 옵션을 선택해 다른 방법으로 이러한 설정을 수정할 수도 있습니다.

연결을 삭제하기 전에 정의된 PSK를 가져오기 위해 연결 디바이스에 대한 구성을 다운로드할 수도 있습니다. 이런 방식으로 다른 쪽에서는 재정의할 필요가 없습니다.

> [!div class="op_single_selector"]
> * [Azure 포털](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP 주소 접두사 수정

IP 주소 접두사를 수정하는 경우 수행하는 단계는 로컬 네트워크 게이트웨이에 에 연결이 있는지 여부에 따라 달라집니다.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>게이트웨이 IP 주소 수정

연결하려는 VPN 디바이스의 공용 IP 주소가 변경된 경우 해당 변경 내용을 반영하도록 로컬 네트워크 게이트웨이를 수정해야 합니다. 공용 IP 주소를 변경하는 경우 수행하는 단계는 로컬 네트워크 게이트웨이에 에 연결이 있는지 여부에 따라 달라집니다.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>다음 단계

게이트웨이 연결을 확인할 수 있습니다. [게이트웨이 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.
