---
title: "URL 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이 만들기 - Azure CLI 2.0 | Microsoft Docs"
description: "이 페이지에서는 URL 라우팅 규칙을 사용하여 Azure 응용 프로그램 게이트웨이를 만들고, 구성하기 위한 지침을 제공합니다."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 958049830d6753ec26635f18f8f8b2fabdec0733
ms.contentlocale: ko-kr
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-using-path-based-routing-with-azure-cli-20"></a>Azure CLI 2.0과 함께 경로 기반 라우팅을 사용하여 응용 프로그램 게이트웨이 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

URL 경로 기반 라우팅을 사용하여 Http 요청의 URL 경로에 따라 경로를 연결할 수 있습니다. Application Gateway에 제공된 URL에 대해 구성된 백 엔드 풀에 대한 경로가 있는지 확인하고 정의된 백 엔드 풀로 네트워크 트래픽을 전송합니다. URL 기반 라우팅의 일반적인 용도는 여러 콘텐츠 형식에 대한 요청의 부하를 여러 백 엔드 서버 풀에 분산하는 것입니다.

URL 기반 라우팅에서는 응용 프로그램 게이트웨이에 새로운 규칙 형식을 제공합니다. 응용 프로그램 게이트웨이에는 두 가지 규칙 형식(기본 및 PathBasedRouting)이 있습니다. 기본 규칙 형식은 라운드 로빈 배포 외에도 PathBasedRouting 동안 백 엔드 풀에 대한 라운드 로빈 서비스를 제공하며 백 엔드 풀을 선택한 상태에서는 요청 URL의 경로 패턴도 적용합니다.

## <a name="scenario"></a>시나리오

다음 예제에서 Application Gateway는 두 개의 백 엔드 서버 풀(기본 서버 풀 및 이미지 서버 풀)과 함께 contoso.com에 대한 트래픽을 제공합니다.

http://contoso.com/image*에 대한 요청은 이미지 서버 풀(imagesBackendPool)로 라우팅됩니다. 경로 패턴이 일치하지 않는 경우 기본 서버 풀(appGatewayBackendPool)이 선택됩니다.

![url 경로](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="log-in-to-azure"></a>Azure에 로그인

**Microsoft Azure 명령 프롬프트**를 열고 로그인합니다. 

```azurecli
az login -u "username"
```

> [!NOTE]
> aka.ms/devicelogin에서 코드를 입력해야 하는 장치 로그인에 대한 스위치 없이 `az login`을 사용할 수도 있습니다.

앞의 예제를 입력하면 코드가 제공됩니다. 브라우저에서 https://aka.ms/devicelogin으로 이동하여 로그인 프로세스를 계속합니다.

![장치 로그인을 보여 주는 cmd][1]

브라우저에서 받은 코드를 입력합니다. 로그인 페이지로 리디렉션됩니다.

![코드를 입력하는 브라우저][2]

로그인한 코드가 입력된 후 브라우저를 닫아 시나리오를 계속합니다.

![성공적으로 로그인][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에 경로 기반 규칙 추가

정의된 경로 규칙을 사용하여 응용 프로그램 게이트웨이 만들기

### <a name="create-a-new-back-end-pool"></a>새 백 엔드 풀 만들기

백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 **imagesBackendPool**을 구성합니다. 이 예제에서는 새 백 엔드 풀에 대한 다른 백 엔드 풀 설정을 구성합니다. 각 백 엔드 풀에는 고유한 백 엔드 풀 설정이 있을 수 있습니다.  백 엔드 HTTP 설정은 올바른 백 엔드 풀 멤버에게 트래픽을 라우팅하는 규칙에서 사용되며, 백 엔드 풀 멤버에 트래픽을 보낼 때 사용되는 프로토콜과 포트를 결정합니다. 쿠키 기반 세션도 백 엔드 HTTP 설정에 따라 결정됩니다.  이 설정이 활성화되면 쿠키 기반 세션 선호도는 각 패킷에 대한 이전 요청과 동일한 백 엔드로 트래픽을 보냅니다.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port"></a>새 프런트 엔드 포트 만들기

응용 프로그램 게이트웨이에 대한 프런트 엔드 포트를 구성합니다. 프런트 엔드 포트 구성 개체는 Application Gateway에서 수신기의 트래픽을 수신 대기하는 포트를 정의하기 위해 수신기에서 사용합니다.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>새 수신기 만들기

수신기를 구성합니다. 이 단계에서는 들어오는 네트워크 트래픽을 수신하는 데 사용되는 공용 IP 주소 및 포트에 대한 수신기를 구성합니다. 다음 예제에서는 이전에 구성한 프런트 엔드 IP 구성, 프런트 엔드 포트 구성 및 프로토콜(http 또는 https)을 사용하여 수신기를 구성합니다. 이 예제에서 수신기는 이전에 만든 공용 IP 주소의 82 포트에서 HTTP 트래픽을 수신 대기합니다.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Url 경로 맵 만들기

백 엔드 풀에 대한 URL 규칙 경로를 구성합니다. 이 단계에서는 응용 프로그램 게이트웨이가 URL 경로와 들어오는 트래픽을 처리하는 데 할당되는 백 엔드 풀 간의 매핑을 정의하는 데 사용하는 상대 경로를 구성합니다.

> [!IMPORTANT]
> 각 경로는 /로 시작해야 하고 "\*"는 끝에만 올 수 있습니다. 사용 가능한 예는 /xyz, /xyz* 또는 /xyz/*입니다. 경로 검사기에 제공하는 문자열은 "?" 또는 "#"으로 시작하는 텍스트는 포함하지 않습니다. 이러한 문자는 허용되지 않습니다. 

다음 예제에서는 백 엔드 "imagesBackendPool"에 대한 트래픽을 라우팅하는 "/images/*" 경로에 하나의 규칙을 만듭니다. 이 규칙은 각 url 집합에 대한 트래픽이 백 엔드에 라우팅되는지 확인합니다. 예를 들어 http://adatum.com/images/figure1.jpg는 "imagesBackendPool"로 이동합니다. 또한 경로가 미리 정의된 경로 규칙과 일치하지 않으면 규칙 경로 맵 구성은 기본 백 엔드 주소 풀을 구성합니다. 예를 들어 http://adatum.com/shoppingcart/test.html은 일치하지 않는 트래픽에 대한 기본 풀로 정의된 대로 pool1로 이동합니다.

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

