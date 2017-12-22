---
title: "URL 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이 만들기 - Azure CLI 2.0 | Microsoft Docs"
description: "이 페이지에서는 URL 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이를 만들고 구성하는 방법에 대한 지침을 제공합니다."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Azure CLI 2.0에서 경로 기반 라우팅을 사용하여 응용 프로그램 게이트웨이 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

URL 경로 기반 라우팅을 사용하면 HTTP 요청의 URL 경로에 따라 경로를 연결할 수 있습니다. URL 경로 기반 라우팅에서는 응용 프로그램 게이트웨이에 나열되어 있는 URL용으로 구성된 백 엔드 서버 풀에 대한 경로가 있는지 확인한 다음 정의된 풀로 네트워크 트래픽을 전송합니다. URL 경로 기반 라우팅은 다양한 콘텐츠 형식에 대한 요청을 여러 백 엔드 서버 풀로 부하 분산하는 데 흔히 사용됩니다.

Azure Application Gateway는 기본 규칙과 URL 경로 기반 규칙의 두 가지 규칙 유형을 사용합니다. 기본 규칙 유형에서는 백 엔드 풀용 라운드 로빈 서비스가 제공됩니다. 경로 기반 규칙에서는 라운드 로빈 배포뿐 아니라 요청 URL의 경로 패턴도 사용하여 적절한 백 엔드 풀을 선택합니다.

## <a name="scenario"></a>시나리오

다음 예제에서 응용 프로그램 게이트웨이는 두 개의 백 엔드 서버 풀(기본 서버 풀 및 이미지 서버 풀)을 사용하여 contoso.com의 트래픽을 처리합니다.

http://contoso.com/image*에 대한 요청은 이미지 서버 풀(**imagesBackendPool**)로 라우팅됩니다. 경로 패턴이 일치하지 않으면 응용 프로그램 게이트웨이는 기본 서버 풀(**appGatewayBackendPool**)을 선택합니다.

![URL 경로](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Azure에 로그인

**Microsoft Azure 명령 프롬프트**를 열고 로그인합니다.

```azurecli
az login -u "username"
```

> [!NOTE]
> aka.ms/devicelogin에서 코드를 입력해야 하는 장치 로그인에 대한 스위치 없이 `az login`을 사용할 수도 있습니다.

위의 명령을 입력하고 나면 코드가 수신됩니다. 브라우저에서 https://aka.ms/devicelogin으로 이동하여 로그인 프로세스를 계속합니다.

![장치 로그인을 보여 주는 cmd][1]

브라우저에서 받은 코드를 입력합니다. 이렇게 하면 로그인 페이지로 리디렉션됩니다.

![코드를 입력할 수 있는 브라우저 화면][2]

코드를 입력하여 로그인한 후 브라우저를 닫고 계속 진행합니다.

![정상 로그인된 후의 화면][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에 경로 기반 규칙 추가

다음 단계에서는 기존 응용 프로그램 게이트웨이에 경로 기반 규칙을 추가하는 방법을 설명합니다.
### <a name="create-a-new-back-end-pool"></a>새 백 엔드 풀 만들기

백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 **imagesBackendPool**을 구성합니다. 이 예제에서는 새 백 엔드 풀에 대한 다른 백 엔드 풀 설정을 구성합니다. 각 백 엔드 풀에는 고유한 설정이 있을 수 있습니다. 경로 기반 규칙은 백 엔드 HTTP 설정을 사용하여 올바른 백 엔드 풀 멤버로 트래픽을 라우팅합니다. 이 과정을 통해 백 엔드 풀 멤버로 트래픽을 보낼 때 사용되는 프로토콜과 포트가 결정됩니다. 쿠키 기반 세션도 백 엔드 HTTP 설정에 따라 결정됩니다.  쿠키 기반 세션 선호도가 사용하도록 설정되어 있으면 각 패킷에 대해 이전 요청과 동일한 백 엔드로 트래픽이 전송됩니다.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>응용 프로그램 게이트웨이용 새 프런트 엔드 포트 만들기

프런트 엔드 포트 구성 개체는 응용 프로그램 게이트웨이가 수신기에서 트래픽을 수신 대기하는 포트를 정의하기 위해 수신기에서 사용됩니다.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>새 수신기 만들기

이 단계에서는 들어오는 네트워크 트래픽을 수신하는 데 사용되는 공용 IP 주소 및 포트에 대한 수신기를 구성합니다. 다음 예제에서는 이전에 구성한 프런트 엔드 IP 구성, 프런트 엔드 포트 구성 및 프로토콜(http 또는 https, 대/소문자 구분)을 가져와 수신기를 구성합니다. 이 예제에서 수신기는 이 시나리오의 앞부분에서 만든 공용 IP 주소의 82 포트에서 HTTP 트래픽을 수신 대기합니다.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>URL 경로 맵 만들기

이 단계에서는 들어오는 트래픽을 처리하는 데 할당되는 백 엔드 풀과 경로 간의 매핑을 정의하기 위해 응용 프로그램 게이트웨이가 사용하는 상대 URL 경로를 구성합니다.

> [!IMPORTANT]
> 각 경로는 "/"로 시작해야 하며 별표는 경로 끝에만 추가할 수 있습니다. 사용 가능한 예는 /xyz, /xyz* 또는 /xyz/*입니다. 경로 검사기에 제공하는 문자열은 "?" 또는 "#"으로 시작하는 텍스트는 포함하지 않습니다. 이러한 문자는 허용되지 않습니다. 

다음 예제에서는 백 엔드 **imagesBackendPool** 로 트래픽을 라우팅하는 /images/\* 경로용 규칙 하나를 만듭니다. 이 규칙을 통해 각 URL 집합에 대한 트래픽이 백 엔드로 라우팅됩니다. 예를 들어 http://adatum.com/images/figure1.jpg는 **imagesBackendPool** 로 이동합니다. 또한 경로가 미리 정의된 경로 규칙과 일치하지 않으면 규칙 경로 맵 구성은 기본 백 엔드 주소 풀을 구성합니다. 예를 들어 http://adatum.com/shoppingcart/test.html은 일치하지 않는 트래픽에 대한 기본 풀로 정의된 **pool1** 로 이동합니다.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>다음 단계

SSL(Secure Sockets Layer) 오프로드에 대해 알아보려는 경우 [SSL 오프로드에 대해 응용 프로그램 게이트웨이 구성](application-gateway-ssl-cli.md)을 참조하세요.


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png
