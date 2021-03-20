---
title: Application Gateway 및 Azure 방화벽을 사용 하 여 응용 프로그램을 인터넷에 노출
description: Application Gateway 및 Azure 방화벽을 사용 하 여 응용 프로그램을 인터넷에 노출 하는 방법
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738721"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Application Gateway 및 Azure 방화벽을 사용 하 여 응용 프로그램을 인터넷에 노출

이 문서에서는 Application Gateway 및 Azure 방화벽을 사용 하 여 응용 프로그램을 인터넷에 노출 하는 방법을 설명 합니다. Azure 스프링 클라우드 서비스 인스턴스를 가상 네트워크에 배포 하는 경우 서비스 인스턴스의 응용 프로그램은 개인 네트워크 에서만 액세스할 수 있습니다. 인터넷에서 응용 프로그램에 액세스할 수 있도록 하려면 **Azure 애플리케이션 게이트웨이와** 통합 하 고 필요에 따라 **Azure 방화벽과** 함께 통합 해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [2.0.4 이상을 이상 버전을 Azure CLI](/cli/azure/install-azure-cli)합니다.

## <a name="define-variables"></a>변수 정의

[Azure virtual network에서 Azure 스프링 클라우드 배포 (VNet 주입)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)에서 지시한 대로 만든 리소스 그룹 및 가상 네트워크에 대 한 변수를 정의 합니다. 실제 환경에 따라 값을 사용자 지정 합니다.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Azure에 로그인

Azure CLI에 로그인하고 활성 구독을 선택합니다.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

만들 **Azure 애플리케이션 게이트웨이** 는 Azure 스프링 클라우드 서비스 인스턴스와 동일한 가상 네트워크를--또는 피어 링 가상 네트워크에 연결 합니다. 먼저를 사용 하 여 가상 네트워크의 Application Gateway에 대 한 새 서브넷을 만들고 `az network vnet subnet create` 를 사용 하 여 Application Gateway의 프런트 엔드로 공용 IP 주소를 만듭니다 `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>애플리케이션 게이트웨이 만들기

를 사용 하 여 응용 프로그램 게이트웨이를 만들고 `az network application-gateway create` 응용 프로그램의 개인 FQDN (정규화 된 도메인 이름)을 백 엔드 풀의 서버로 지정 합니다. 그런 다음를 사용 하 여 `az network application-gateway http-settings update` 백 엔드 풀의 호스트 이름을 사용 하도록 HTTP 설정을 업데이트 합니다.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Azure가 애플리케이션 게이트웨이를 만들 때까지 최대 30분이 걸릴 수 있습니다. 만든 후에는를 사용 하 여 백 엔드 상태를 확인 합니다 `az network application-gateway show-backend-health` .  이는 응용 프로그램 게이트웨이가 해당 개인 FQDN을 통해 응용 프로그램에 도달 하는지 여부를 검사 합니다.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

출력은 백 엔드 풀의 정상 상태를 나타냅니다.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>응용 프로그램 게이트웨이의 프런트 엔드 공용 IP를 사용 하 여 응용 프로그램에 액세스

을 사용 하 여 응용 프로그램 게이트웨이의 공용 IP 주소를 가져옵니다 `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

  ![공용 IP의 앱](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>참고 항목

- [VNET에서 Azure Spring Cloud 문제 해결](spring-cloud-troubleshooting-vnet.md)
- [VNET에서 Azure Spring Cloud를 실행하는 고객의 책임](spring-cloud-vnet-customer-responsibilities.md)