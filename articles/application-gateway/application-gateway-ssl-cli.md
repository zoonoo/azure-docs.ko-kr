---
title: "SSL 오프로드 구성 - Azure Application Gateway - Azure CLI 2.0 | Microsoft Docs"
description: "이 문서에서는 Azure CLI 2.0을 사용하여 SSL 오프로드와 함께 응용 프로그램 게이트웨이를 만드는 지침을 제공합니다."
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
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 032a514ddab625e4f7c5ef23a1da03a0162f43e3
ms.contentlocale: ko-kr
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 클래식 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway 구성을 사용하여 웹 팜에서 발생하는 비용이 많이 드는 SSL(Secure Sockets Layer) 암호 해독 작업을 방지하기 위한 게이트웨이에서 SSL 세션을 종료합니다. SSL 오프로드는 프런트 엔드 서버에서 인증서 관리를 단순화합니다.

## <a name="prerequisite-install-the-azure-cli-20"></a>필수 조건: Azure CLI 2.0 설치

이 문서의 단계를 수행하려면 [Mac, Linux 및 Windows용 Azure CLI(Azure 명령줄 인터페이스)를 설치](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)해야 합니다.

## <a name="required-components"></a>필수 구성 요소

* **백 엔드 서버 풀**: 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP 주소 또는 VIP(가상 IP 주소)이어야 합니다.
* **백 엔드 서버 풀 설정**: 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
* **프런트 엔드 포트**: 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
* **수신기**: 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
* **규칙**: 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽을 이동하는 백 엔드 서버 풀을 정의합니다. 현재는 *기본* 규칙만 지원 됩니다. *기본* 규칙은 라운드 로빈 부하 분산입니다.

**추가 구성 정보**

SSL 인증서 구성에서 **HttpListener** 의 프로토콜은 *Https* (대/소문자 구분)로 바꿔야 합니다. **SslCertificate** 요소를 SSL 인증서에 대해 구성된 변수 값과 함께 **HttpListener**에 추가합니다. 프런트 엔드 포트는 **443**으로 업데이트되어야 합니다.

**쿠키 기반 선호도를 사용하도록 설정**: 클라이언트 세션의 요청이 항상 웹 팜에 있는 동일한 VM으로 전송되도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 이를 완료하려면 게이트웨이에서 트래픽을 적절하게 지시할 수 있는 세션 쿠키를 삽입합니다. 쿠키 기반 선호도를 사용하려면 **BackendHttpSettings** 요소에서 **CookieBasedAffinity**를 *Enabled*로 설정합니다.

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에 SSL 오프로드 구성

다음 명령을 입력하여 기존 응용 프로그램 게이트웨이에 대한 SSL 오프로드를 구성합니다.

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>SSL 오프로드를 사용하여 응용 프로그램 게이트웨이 만들기

다음 예제에서는 SSL 오프로드를 사용하여 응용 프로그램 게이트웨이를 만듭니다. 인증서 및 인증서 암호는 유효한 개인 키로 업데이트되어야 합니다.

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다.  공용 IP를 사용할 때 Application Gateway는 친근한 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 응용 프로그램 게이트웨이를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 응용 프로그램 게이트웨이의 공용 끝점을 가리키도록 합니다. 자세한 내용은 [Azure에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요. 

별칭을 구성하려면 응용 프로그램 게이트웨이에 연결된 **PublicIPAddress** 요소를 사용하여 응용 프로그램 게이트웨이 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름을 사용하여 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만듭니다. A 레코드를 사용할 경우 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>다음 단계

내부 부하 분산 장치에서 사용되도록 응용 프로그램 게이트웨이를 구성하려면 [내부 부하 분산 장치를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

일반적 부하 분산 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

