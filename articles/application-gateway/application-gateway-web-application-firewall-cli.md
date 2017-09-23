---
title: "웹 응용 프로그램 방화벽 구성: Azure Application Gateway | Microsoft Docs"
description: "이 문서에서는 기존 또는 새 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽을 사용하는 방법을 안내합니다."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 999d1ad3ee54d84e155254655dbb7a39ac60572c
ms.contentlocale: ko-kr
ms.lasthandoff: 08/29/2017

---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Azure CLI를 사용하여 새 또는 기존 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽 구성

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

WAF(웹 응용 프로그램 방화벽)를 사용하도록 설정된 응용 프로그램 게이트웨이를 만드는 방법에 대해 알아봅니다. 또한 WAF를 기존 응용 프로그램 게이트웨이에 추가하는 방법을 알아봅니다.

Azure Application Gateway의 WAF는 SQL 삽입 공격, 사이트 간 스크립팅 공격, 세션 하이재킹 등의 일반적인 웹 기반 공격으로부터 웹 응용 프로그램을 보호합니다.

 Application Gateway는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다. Application Gateway는 많은 ADC(응용 프로그램 배달 컨트롤러) 기능을 제공합니다.

 * HTTP 부하 분산 
 * 쿠키 기반 세션 선호도 
 * SSL(Secure Sockets Layer) 오프로드 
 * 사용자 지정 상태 프로브 
 * 멀티 사이트 기능에 대한 지원
 
 지원되는 기능의 전체 목록을 찾으려면 [Application Gateway에 대한 개요](application-gateway-introduction.md)를 참조하세요.

이 문서는 [기존 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽을 추가](#add-web-application-firewall-to-an-existing-application-gateway)하는 방법을 보여 줍니다. 또한 [웹 응용 프로그램 방화벽을 사용하여 응용 프로그램 게이트웨이를 만드는](#create-an-application-gateway-with-web-application-firewall) 방법을 보여 줍니다.

![시나리오 이미지][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>필수 조건: Azure CLI 2.0 설치

이 문서의 단계를 수행하려면 [Mac, Linux 및 Windows용 Azure CLI(Azure 명령줄 인터페이스)를 설치](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)해야 합니다.

## <a name="waf-configuration-differences"></a>WAF 구성 차이

[Azure CLI를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway-cli.md)를 읽어 보셨다면 응용 프로그램 게이트웨이를 만들 때 구성하는 SKU 설정에 대해 알고 계실 것입니다. WAF는 응용 프로그램 게이트웨이에 SKU를 구성할 때 정의하는 추가 설정을 제공합니다. Application Gateway 자체에서 추가로 변경해야 하는 사항은 없습니다.

| **설정** | **세부 정보**
|---|---|
|**SKU** |WAF가 없는 일반 응용 프로그램 게이트웨이는 **Standard\_Small**, **Standard\_Medium** 및 **Standard\_Large** 크기를 지원합니다. WAF의 도입으로 두 개의 SKU, 즉 **WAF\_Medium** 및 **WAF\_Large** SKU가 추가되었습니다. 소형 응용 프로그램 게이트웨이에는 WAF가 지원되지 않습니다.|
|**모드** | 이 설정은 WAF 모드입니다. 허용되는 값은 **검색** 및 **방지**입니다. WAF를 **검색** 모드로 설정하면 모든 위협이 로그 파일에 저장됩니다. **방지** 모드에서는 이벤트를 여전히 기록하지만 공격자가 응용 프로그램 게이트웨이로부터 403 권한 없음 응답을 받습니다.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽 추가

다음 명령은 기존 표준 응용 프로그램 게이트웨이를 WAF가 활성화된 응용 프로그램 게이트웨이로 변경합니다.

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

이 명령은 WAF가 있는 응용 프로그램 게이트웨이를 업데이트합니다. 응용 프로그램 게이트웨이에 대한 로그를 보는 방법을 알아보려면 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요. WAF의 보안 특성상, 주기적으로 로그를 검토하여 웹 응용 프로그램의 보안 상태를 파악해야 합니다.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이 만들기

다음 명령은 WAF를 사용하여 응용 프로그램 게이트웨이를 만듭니다.

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> 기본 WAF 구성을 사용하여 만든 응용 프로그램 게이트웨이는 보호를 위해 CRS 3.0으로 구성됩니다.

## <a name="get-an-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 응용 프로그램 게이트웨이는 친근한 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 응용 프로그램 게이트웨이를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 응용 프로그램 게이트웨이의 공용 끝점을 가리키도록 합니다. 자세한 내용은 [Azure 클라우드 서비스에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요. 

CNAME 기록을 구성하려면 응용 프로그램 게이트웨이에 연결된 PublicIPAddress 요소를 사용하여 응용 프로그램 게이트웨이 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름을 사용하여 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만듭니다. VIP가 응용 프로그램 게이트웨이를 다시 시작할 때 변경될 수 있으므로 A 레코드 사용은 권장하지 않습니다.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
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

WAF 규칙을 사용자 지정하는 방법을 알아보려면 [Azure CLI 2.0을 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-cli.md)을 참조하세요.

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png

