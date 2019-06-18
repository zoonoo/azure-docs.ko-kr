---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161524"
---
### <a name="to-view-local-network-gateways"></a>로컬 네트워크 게이트웨이를 보려면

로컬 네트워크 게이트웨이 목록을 보려면 [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway) 명령을 사용합니다.

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>공유 키 값을 확인하려면

공유 키 값이 VPN 디바이스 구성에 사용한 값과 동일한지 확인합니다. 동일하지 않은 경우 디바이스의 값을 사용하여 연결을 다시 실행하거나 반환되는 값으로 디바이스를 업데이트합니다. 값이 일치해야 합니다. 공유 키를 보려면 [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection) 명령을 사용합니다.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>VPN Gateway 공용 IP 주소 보기

가상 네트워크 게이트웨이의 공용 IP 주소를 찾으려면 [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) 명령을 사용합니다. 이 예제는 읽기 쉽도록 공용 IP 목록을 테이블 형식으로 표시하도록 출력 형식이 지정됩니다.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
