---
title: 파일 포함
description: 파일 포함
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025633"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>로컬 네트워크 게이트웨이의 'gatewayIpAddress'를 수정하려면

연결하려는 VPN 디바이스의 공용 IP 주소가 변경된 경우 해당 변경 내용을 반영하도록 로컬 네트워크 게이트웨이를 수정해야 합니다. 기존 VPN Gateway 연결을 제거하지 않고도(있는 경우) 게이트웨이 IP 주소를 변경할 수 있습니다. 게이트웨이 IP 주소를 수정하려면 [az network local-gateway update](/cli/azure/network/local-gateway) 명령을 사용하여 'Site2' 및 'TestRG1' 값을 사용자 고유의 값으로 바꿉니다.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

출력에서 IP 주소가 올바른지 확인합니다.

```
"gatewayIpAddress": "23.99.222.170",
```