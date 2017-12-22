---
title: "응용 프로그램 게이트웨이 만들기 - Azure CLI 2.0 | Microsoft Docs"
description: "Resource Manager에서 Azure CLI 2.0을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: beb2dab177d021fee1dbbe630f8b6854a7d94f68
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Application Gateway 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 클래식 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 템플릿](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Azure Application Gateway는 ADC(응용 프로그램 배달 컨트롤러)를 서비스로 제공하여 응용 프로그램에 대해 다양한 7계층 부하 분산 기능을 제공하는 전용 가상 어플라이언스입니다.

## <a name="cli-versions"></a>CLI 버전

다음 CLI(명령줄 인터페이스) 버전 중 하나를 사용하여 응용 프로그램 게이트웨이를 만들 수 있습니다.

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md): 클래식 및 Azure Resource Manager 배포 모델용 Azure CLI
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md): Resource Manager 배포 모델용 차세대 CLI

## <a name="prerequisite-install-the-azure-cli-20"></a>필수 조건: Azure CLI 2.0 설치

이 문서의 단계를 수행하려면 [macOS, Linux 및 Windows용 Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-az-cli2)해야 합니다.

> [!NOTE]
> 응용 프로그램 게이트웨이를 만들려면 Azure 계정이 필요합니다. 없는 경우 지금 [무료 평가판](../active-directory/sign-up-organization.md)에 등록하세요.

## <a name="scenario"></a>시나리오

이 시나리오에서는 Azure Portal을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.

이 시나리오에서는 다음을 수행합니다.

* 두 인스턴스를 사용하여 중간 응용 프로그램 게이트웨이를 만듭니다.
* 예약된 CIDR 블록이 10.0.0.0/16이고 이름이 AdatumAppGatewayVNET인 가상 네트워크를 만듭니다.
* CIDR 블록으로 10.0.0.0/28을 사용하는 Appgatewaysubnet이라고 하는 서브넷을 만듭니다.

> [!NOTE]
> 사용자 지정 상태 프로브, 백 엔드 풀 주소, 추가 규칙 등을 비롯한 응용 프로그램 게이트웨이의 추가 구성은 초기 배포 중이 아니라 응용 프로그램 게이트웨이를 만든 후에 수행됩니다.

## <a name="before-you-begin"></a>시작하기 전에

응용 프로그램 게이트웨이에는 자체 서브넷이 필요합니다. 가상 네트워크를 만들 때 여러 서브넷을 둘 수 있는 충분한 주소 공간이 있는지 확인합니다. 응용 프로그램 게이트웨이를 서브넷에 배포한 후에는 해당 서브넷에 응용 프로그램 게이트웨이를 더 추가하는 것만 가능합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

**Microsoft Azure 명령 프롬프트**를 열고 로그인합니다.

```azurecli-interactive
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

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

응용 프로그램 게이트웨이를 만들기 전에 해당 게이트웨이를 포함할 리소스 그룹을 만듭니다. 다음 명령을 사용합니다.

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

백 엔드 서버 IP 주소에는 백 엔드 IP 주소를 사용합니다. 이러한 값은 가상 네트워크의 개인 IP, 공용 IP 또는 백 엔드 서버의 정규화된 도메인 이름일 수 있습니다. 다음 예제에서는 HTTP 설정, 포트 및 규칙에 대한 추가 구성으로 응용 프로그램 게이트웨이를 만듭니다.

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

앞의 예제에서는 응용 프로그램 게이트웨이를 만드는 동안 필요하지 않은 많은 속성을 보여 줍니다. 다음 코드 예제는 필요한 정보를 사용하여 응용 프로그램 게이트웨이를 만듭니다.

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> 만드는 동안 사용할 수 있는 매개 변수 목록을 확인하려면 `az network application-gateway create --help` 명령을 실행합니다.

이 예제에서는 수신기, 백 엔드 풀, 백 엔드 HTTP 설정 및 규칙에 대한 기본 설정으로 기본 응용 프로그램 게이트웨이를 만듭니다. 프로비전에 성공하면 배포에 맞게 이러한 설정을 수정할 수 있습니다.

웹 응용 프로그램이 이전 단계의 백 엔드 풀로 정의된 경우에는 이제 부하 분산이 시작됩니다.

## <a name="get-the-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기
게이트웨이를 만든 후에는 통신용 프런트 엔드를 구성합니다. 공용 IP를 사용할 때 응용 프로그램 게이트웨이는 식별 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 사용자가 응용 프로그램 게이트웨이에 연결할 수 있도록 하려면 CNAME 레코드를 사용하여 응용 프로그램 게이트웨이의 공용 끝점을 가리킵니다. 자세한 내용은 [Azure DNS를 사용하여 Azure 서비스에 대해 사용자 지정 도메인 설정 제공](../dns/dns-custom-domain.md)을 참조하세요.

별칭을 구성하려면 응용 프로그램 게이트웨이에 연결된 PublicIPAddress 요소를 사용하여 응용 프로그램 게이트웨이 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름을 사용하여 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만듭니다. A 레코드는 사용하지 마세요. 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있습니다.


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

## <a name="delete-all-resources"></a>모든 리소스 삭제

이 문서에서 만든 모든 리소스를 삭제하려면 다음 명령을 실행합니다.

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>다음 단계

사용자 지정 상태 프로브를 만드는 방법을 알아보려면 [Portal을 사용하여 Application Gateway에 대한 사용자 지정 프로브 만들기](application-gateway-create-probe-portal.md)로 이동하세요.

SSL 오프로딩을 구성하여 웹 서버에서 비용이 많이 드는 SSL 암호 해독을 수행하지 않아도 되도록 설정하려면 [Azure Resource Manager를 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성](application-gateway-ssl-arm.md)을 참조하세요.

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
