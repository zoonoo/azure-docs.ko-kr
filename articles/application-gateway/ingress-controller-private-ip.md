---
title: 수신 엔드포인트에 대한 내부 라우팅에 개인 IP 주소 사용
description: 이 문서에서는 내부 라우팅에 비공개 IP를 사용하여 클러스터 내의 수신 엔드포인트를 나머지 VNet에 노출하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397311"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>수신 엔드포인트에 대한 내부 라우팅에 개인 IP 사용 

이 기능을 사용하면 개인 IP를 사용하는 `Virtual Network` 내에서 수신 엔드포인트를 노출할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소  
[개인 IP 구성](./configure-application-gateway-with-private-frontend-ip.md)인 애플리케이션 게이트웨이

수신에 개인 IP를 사용하여 컨트롤러를 구성하는 두 가지 방법이 있습니다.

## <a name="assign-to-a-particular-ingress"></a>특정 수신에 할당
개인 IP를 통해 특정 수신을 노출하려면 수신에서 주석 [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip)를 사용합니다.

### <a name="usage"></a>사용량
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

개인 IP가 없는 Application Gateway의 경우 `appgw.ingress.kubernetes.io/use-private-ip: "true"`로 주석이 달린 수신은 무시됩니다. 이는 수신 이벤트와 AGIC Pod 로그에 표시됩니다.

* 수신 이벤트에 표시된 오류

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* AGIC 로그에 표시된 오류

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>전역 할당
개인 IP를 통해 노출되는 모든 수신을 제한해야 하는 경우 `helm` 구성에서 `appgw.usePrivateIP: true`를 사용합니다.

### <a name="usage"></a>사용량
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

그러면 Application Gateway에서 프런트 엔드 수신기를 구성할 때 수신 컨트롤러에서 개인 IP에 대해 IP 주소 구성을 필터링합니다.
`usePrivateIP: true`가 할당되었지만 개인 IP는 할당되지 않은 경우 AGIC는 비상 상태가 되며 충돌합니다.

> [!NOTE]
> Application Gateway v2 SKU에는 공용 IP가 필요합니다. Application Gateway가 비공개여야 하는 경우 트래픽을 제한하려면 Application Gateway 서브넷에 [`Network Security Group`](../virtual-network/network-security-groups-overview.md)을 연결합니다.