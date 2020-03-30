---
title: 내부 라우팅에 개인 IP 주소 사용
description: 이 문서에서는 내부 라우팅에 개인 IP를 사용하는 방법에 대한 정보를 제공하므로 클러스터 내의 침투 끝점을 VNet의 나머지 부분에 노출합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795486"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>내부 라우팅에 개인 IP 사용 

이 기능을 사용하면 개인 IP를 사용하여 내의 받는 끝점을 노출할 `Virtual Network` 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소  
프라이빗 IP [구성을](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip) 갖춘 애플리케이션 게이트웨이

받는 데 개인 IP를 사용하도록 컨트롤러를 구성하는 방법에는 두 가지가 있습니다.

## <a name="assign-to-a-particular-ingress"></a>특정 인서에 할당
개인 IP를 통해 특정 초기화를 노출하려면 Inress에서 추가를 [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) 사용합니다.

### <a name="usage"></a>사용
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

개인 IP가 없는 응용 프로그램 게이트웨이의 경우 추가된 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 인그레스는 무시됩니다. 이는 inress 이벤트 및 AGIC 포드 로그에 표시됩니다.

* Inress 이벤트에 표시된 오류

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
경우에 요구 사항은 모든 Ingressess가 개인 IP를 `appgw.usePrivateIP: true` 통해 `helm` 노출하도록 제한하고 구성에서 사용하는 것입니다.

### <a name="usage"></a>사용
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

이렇게 하면 응용 프로그램 게이트웨이에서 프런트 엔드 리스너를 구성할 때 수신기 컨트롤러가 개인 IP에 대한 IP 주소 구성을 필터링합니다.
개인 IP가 할당되지 `usePrivateIP: true` 않은 경우 AGIC는 공황 및 충돌합니다.

> [!NOTE]
> 응용 프로그램 게이트웨이 v2 SKU에는 공용 IP가 필요합니다. 응용 프로그램 게이트웨이를 비공개로 유지하려면 응용 프로그램 게이트웨이의 서브넷에 연결하여 [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) 트래픽을 제한합니다.
