---
title: 가상 네트워크에 대한 Azure IoT 허브 지원
description: IoT Hub를 사용하여 가상 네트워크 연결 패턴을 사용하는 방법
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501425"
---
# <a name="iot-hub-support-for-virtual-networks"></a>가상 네트워크를 위한 IoT 허브 지원

이 문서에서는 VNET 연결 패턴을 소개하고 고객 소유의 Azure VNET을 통해 IoT 허브에 개인 연결 환경을 설정하는 방법에 대해 자세히 설명합니다.

> [!NOTE]
> 이 문서에서 설명하는 IoT Hub 기능은 현재 미국 동부, 미국 중남부 및 미국 서부 2의 [관리형 서비스 ID로 만든](#create-an-iot-hub-with-managed-service-identity) IoT 허브에서 사용할 수 있습니다.


## <a name="introduction"></a>소개

기본적으로 IoT Hub 호스트 이름은 인터넷을 통해 공개적으로 라우팅가능한 IP 주소가 있는 공용 끝점에 매핑됩니다. 아래 그림과 같이 이 IoT Hub 공용 엔드포인트는 서로 다른 고객이 소유한 허브 간에 공유되며 광역 네트워크와 온-프레미스 네트워크를 통해 IoT 장치에서 액세스할 수 있습니다.

[메시지 라우팅,](./iot-hub-devguide-messages-d2c.md) [파일 업로드](./iot-hub-devguide-file-upload.md)및 대량 [장치 가져오기/내보내기를](./iot-hub-bulk-identity-mgmt.md) 비롯한 여러 IoT Hub 기능은 마찬가지로 공용 끝점을 통해 IoT Hub에서 고객 소유 Azure 리소스에 대한 연결이 필요합니다. 아래 그림과 같이 이러한 연결 경로는 IoT Hub에서 고객 리소스로의 송신 트래픽을 구성합니다.
![IoT 허브 공용 엔드포인트](./media/virtual-network-support/public-endpoint.png)


여러 가지 이유로 고객은 소유하고 운영하는 VNET을 통해 Azure 리소스(IoT Hub 포함)에 대한 연결을 제한할 수 있습니다. 이러한 이유는 다음과 같습니다.

* 공용 인터넷을 통해 허브에 대한 연결 노출을 방지하여 IoT 허브에 대한 네트워크 수준 격리를 통해 추가 보안 계층을 도입합니다.

* 온-프레미스 네트워크 자산에서 개인 연결 환경을 사용하도록 설정하여 데이터와 트래픽이 Azure 백본 네트워크로 직접 전송되도록 합니다.

* 민감한 온-프레미스 네트워크에서 유출 공격을 방지합니다. 

* [개인 끝점을](../private-link/private-endpoint-overview.md)사용하여 설정된 Azure 전체 연결 패턴에 따라.


이 문서에서는 IoT Hub에서 다른 Azure 리소스에 대한 송신 연결에 대해 Azure 신뢰할 수 있는 자사 서비스 예외를 사용하는 것과 같이 IoT Hub에 대한 연결을 수신하기 위한 [개인 끝점을](../private-link/private-endpoint-overview.md) 사용하여 이러한 목표를 달성하는 방법을 설명합니다.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>개인 엔드포인트를 사용하여 IoT Hub에 연결

개인 끝점은 Azure 리소스에 연결할 수 있는 고객 소유 VNET 내에 할당된 개인 IP 주소입니다. IoT 허브에 대한 전용 엔드포인트를 사용하면 트래픽을 IoT Hub의 공용 끝점으로 전송할 필요 없이 VNET 내에서 작동하는 서비스가 IoT Hub에 도달할 수 있습니다. 마찬가지로 온-프레미스에서 작동하는 장치는 [VPN(가상 사설망)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 또는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 프라이빗 피어링을 사용하여 Azure의 VNET에 연결한 다음 이후에 IoT Hub(개인 엔드포인트를 통해)에 연결할 수 있습니다. 따라서 IoT Hub의 공용 끝점에 대한 연결을 제한하려는 고객은 개인 엔드포인트를 사용하여 허브에 대한 연결을 유지하면서 [IoT Hub 방화벽 규칙을](./iot-hub-ip-filtering.md) 사용하여 이 목표를 달성할 수 있습니다.

> [!NOTE]
> 이 설정의 주요 초점은 온-프레미스 네트워크 내의 장치에 대한 것입니다. 이 설정은 광역 네트워크에 배포된 장치에는 권장되지 않습니다.

![IoT 허브 공용 엔드포인트](./media/virtual-network-support/virtual-network-ingress.png)

진행하기 전에 다음 필수 구성 조건이 충족되었는지 확인합니다.

* IoT 허브는 [관리되는 서비스 ID로](#create-an-iot-hub-with-managed-service-identity)프로비전되어야 합니다.

* IoT 허브는 [지원되는 지역](#regional-availability-private-endpoints)중 하나에 프로비전되어야 합니다.

* 개인 끝점을 만들 수 있는 서브넷을 통해 Azure VNET을 프로비전했습니다. 자세한 내용은 [Azure CLI를 사용하여 가상 네트워크 만들기를](../virtual-network/quick-create-cli.md) 참조하세요.

* 온-프레미스 네트워크 내에서 작동하는 장치의 경우 Azure VNET을 가상 [사설망(VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 또는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 개인 피어링을 설정합니다.


### <a name="regional-availability-private-endpoints"></a>지역별 가용성(프라이빗 엔드포인트)

다음 리전에서 생성된 IoT Hub에서 지원되는 개인 엔드포인트:

* 미국 동부

* 미국 중남부

* 미국 서부 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>IoT Hub 시작에 대한 개인 엔드포인트 설정

개인 끝점을 설정하려면 다음 단계를 따르십시오.

1. 다음 Azure CLI 명령을 실행하여 구독에 Azure IoT Hub 공급자를 다시 등록합니다.

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. IoT Hub 포털의 **비공개 끝점 연결** 탭으로 이동(이 탭은 [지원되는 지역의](#regional-availability-private-endpoints)IoT Hubs에서만 사용할 수 있음) 기호를 **+** 클릭하여 새 개인 끝점을 추가합니다.

3. 새 개인 끝점을 만들 수 있는 구독, 리소스 그룹, 이름 및 지역을 제공합니다(허브와 동일한 리전에서 개인 끝점을 만들어야 하는 것이 좋습니다. 자세한 내용은 [지역 가용성 섹션을](#regional-availability-private-endpoints) 참조하세요).

4. **다음 을 클릭합니다: 리소스**를 클릭 하고 IoT 허브 리소스에 대 한 구독을 제공 하 고 **"Microsoft.Devices/IotHubs"** 리소스 유형으로 선택, **리소스로**IoT 허브 이름 및 대상 하위 리소스로 **iotHub를** 선택 합니다.

5. **다음: 가상** 네트워크 및 서브넷을 구성하고 제공하여 개인 엔드포인트를 만듭니다. 원하는 경우 Azure 개인 DNS 영역과 통합할 옵션을 선택합니다.

6. **다음: 태그를**클릭하고 선택적으로 리소스에 대한 태그를 제공합니다.

7. **검토 + 만들기를** 클릭하여 개인 엔드포인트 리소스를 만듭니다.


### <a name="pricing-private-endpoints"></a>가격(프라이빗 엔드포인트)

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub에서 다른 Azure 리소스로의 연결 송출구

IoT Hub는 Azure Blob 저장소, 이벤트 허브, [메시지 라우팅을](./iot-hub-devguide-messages-d2c.md)위한 서비스 버스 리소스, [파일 업로드](./iot-hub-devguide-file-upload.md)및 대량 [장치 가져오기/내보내기에](./iot-hub-bulk-identity-mgmt.md)대한 액세스가 필요하며, 일반적으로 리소스의 공용 끝점을 통해 수행됩니다. 저장소 계정, 이벤트 허브 또는 서비스 버스 리소스를 VNET에 바인딩하는 경우 권장 구성은 기본적으로 리소스에 대한 연결을 차단합니다. 따라서 이러한 리소스에 액세스해야 하는 IoT Hub의 기능이 방해를 받게 됩니다.

이러한 상황을 완화하려면 **Azure 자사 의 신뢰할 수 있는 서비스** 옵션을 통해 IoT Hub 리소스에서 저장소 계정, 이벤트 허브 또는 서비스 버스 리소스에 대한 연결을 사용하도록 설정해야 합니다.

전제 조건은 다음과 같습니다.

* IoT 허브는 [지원되는 지역](#regional-availability-trusted-microsoft-first-party-services)중 하나에 프로비전되어야 합니다.

* IoT 허브는 허브 프로비저닝 시간에 관리되는 서비스 ID를 할당받아야 합니다. [관리되는 서비스 ID를 사용하여 허브를 만드는](#create-an-iot-hub-with-managed-service-identity)방법에 대한 지침을 따릅니다.


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>지역 별 가용성(신뢰할 수 있는 Microsoft 타사 서비스)

Azure 신뢰할 수 있는 자사 서비스 예외는 Azure 저장소, 이벤트 허브 및 서비스 버스 리소스에 대한 방화벽 제한을 우회하는 예외로 다음 리전의 IoT Hubs에만 지원됩니다.

* 미국 동부

* 미국 중남부

* 미국 서부 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>가격 책정(신뢰할 수 있는 Microsoft 타사 서비스)

신뢰할 수 있는 Microsoft 타사 서비스 예외 기능은 [지원되는 지역의](#regional-availability-trusted-microsoft-first-party-services)IoT Hubs에서 무료입니다. 프로비저닝된 저장소 계정, 이벤트 허브 또는 서비스 버스 리소스에 대한 요금은 별도로 적용됩니다.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>관리형 서비스 ID를 사용하여 IoT 허브 만들기

관리되는 서비스 ID는 리소스 프로비저닝 시간에 허브에 할당할 수 있으며(이 기능은 현재 기존 허브에 대해 지원되지 않음) IoT 허브에서 TLS 1.2를 최소 버전으로 사용해야 합니다. 이를 위해 아래 ARM 리소스 템플릿을 사용해야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

리소스에 `name` `location` `SKU.name` 대한 값을 대체한 후 및 `SKU.tier`의 경우 Azure CLI를 사용하여 다음을 사용하여 기존 리소스 그룹에 리소스를 배포할 수 있습니다.

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

리소스를 만든 후 Azure CLI를 사용하여 허브에 할당된 관리되는 서비스 ID를 검색할 수 있습니다.

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

관리되는 서비스 ID를 가진 IoT Hub가 프로비전되면 해당 섹션을 따라 [저장소 계정,](#egress-connectivity-to-storage-account-endpoints-for-routing) [이벤트 허브](#egress-connectivity-to-event-hubs-endpoints-for-routing)및 서비스 [버스](#egress-connectivity-to-service-bus-endpoints-for-routing) 리소스에 라우팅 끝점을 설정하거나 [파일 업로드](#egress-connectivity-to-storage-accounts-for-file-upload) 및 대량 [장치 가져오기/내보내기를](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)구성합니다.


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>라우팅을 위해 저장소 계정 끝점에 연결송결

IoT Hub는 고객 소유 저장소 계정으로 메시지를 라우팅하도록 구성할 수 있습니다. 방화벽 제한이 있는 동안 라우팅 기능이 저장소 계정에 액세스할 수 있도록 하려면 IoT Hub에 관리되는 서비스 ID가 있어야 [합니다(관리되는 서비스 ID가 있는 허브를 만드는](#create-an-iot-hub-with-managed-service-identity)방법 참조). 관리되는 서비스 ID가 프로비전되면 아래 단계에 따라 허브의 리소스 ID에 RBAC 권한을 부여하여 저장소 계정에 액세스할 수 있습니다.

1. Azure 포털에서 저장소 계정의 **IAM(액세스 제어)** 탭으로 이동하여 **역할 할당 추가** 섹션 아래에 있는 **추가를** 클릭합니다.

2. **저장소 Blob 데이터 기여자를** **역할,** **Azure AD 사용자, 그룹 또는 서비스 주체로** 선택하여 **액세스 권한을 할당하고** 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 단추를 클릭합니다.

3. 저장소 계정의 **방화벽 및 가상 네트워크** 탭으로 이동하여 선택한 네트워크 **옵션에서 액세스 허용을** 사용하도록 설정합니다. **예외** 목록에서 신뢰할 수 있는 **Microsoft 서비스가 이 저장소 계정에 액세스할 수 있도록 허용 확인란을**선택합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 메시지 **라우팅** 탭으로 이동합니다.

5. 사용자 **지정 끝점** 섹션으로 이동한 다음 **추가를**클릭합니다. **저장소를** 끝점 유형으로 선택합니다.

6. 표시되는 페이지에서 끝점의 이름을 제공하고 Blob 저장소에 사용할 컨테이너를 선택하고 인코딩 및 파일 이름 형식을 제공합니다. 저장소 끝점에 **인증 유형으로** **할당된 시스템을** 선택합니다. **만들기** 단추를 클릭합니다.

이제 사용자 지정 저장소 끝점이 허브의 시스템에 할당된 ID를 사용하도록 설정되어 있으며 방화벽 제한에도 불구하고 저장소 리소스에 액세스할 수 있는 권한이 있습니다. 이제 이 끝점을 사용하여 라우팅 규칙을 설정할 수 있습니다.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>라우팅을 위해 이벤트 허브 끝점에 연결송결

IoT Hub는 고객 소유 의 이벤트 허브 네임스페이스로 메시지를 라우팅하도록 구성할 수 있습니다. 라우팅 기능이 방화벽 제한이 있는 동안 이벤트 허브 리소스에 액세스할 수 있도록 하려면 IoT Hub에 관리되는 서비스 ID가 있어야 [합니다(관리되는 서비스 ID가 있는 허브를 만드는](#create-an-iot-hub-with-managed-service-identity)방법 참조). 관리되는 서비스 ID가 프로비전되면 아래 단계에 따라 허브의 리소스 ID에 RBAC 권한을 부여하여 이벤트 허브에 액세스할 수 있습니다.

1. Azure 포털에서 이벤트 허브 액세스 **제어(IAM)** 탭으로 이동한 다음 **역할 할당 추가** 섹션 아래에서 **추가를** 클릭합니다.

2. **이벤트 허브 데이터 보낸 자를** **역할로** **선택합니다.** **Assigning access to** **저장** 단추를 클릭합니다.

3. 이벤트 허브의 **방화벽 및 가상 네트워크** 탭으로 이동하여 선택한 네트워크 **옵션에서 액세스 허용을** 사용하도록 설정합니다. **예외** 목록에서 신뢰할 수 있는 **Microsoft 서비스가 이벤트 허브에 액세스할 수 있도록 허용 확인란을**선택합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 메시지 **라우팅** 탭으로 이동합니다.

5. 사용자 **지정 끝점** 섹션으로 이동한 다음 **추가를**클릭합니다. **이벤트 허브를** 끝점 유형으로 선택합니다.

6. 표시되는 페이지에서 끝점의 이름을 제공하고 이벤트 허브 네임스페이스 및 인스턴스를 선택하고 **만들기** 단추를 클릭합니다.

이제 사용자 지정 이벤트 허브 끝점이 허브의 시스템에 할당된 ID를 사용하도록 설정되어 있으며 방화벽 제한에도 불구하고 이벤트 허브 리소스에 액세스할 수 있는 권한이 있습니다. 이제 이 끝점을 사용하여 라우팅 규칙을 설정할 수 있습니다.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>라우팅을 위한 서비스 버스 엔드포인트에 연결송결

IoT Hub는 고객 소유 서비스 버스 네임스페이스로 메시지를 라우팅하도록 구성할 수 있습니다. 라우팅 기능이 방화벽 제한이 있는 동안 서비스 버스 리소스에 액세스할 수 있도록 하려면 IoT Hub에 관리되는 서비스 ID가 있어야 [합니다(관리되는 서비스 ID가 있는 허브를 만드는](#create-an-iot-hub-with-managed-service-identity)방법 참조). 관리되는 서비스 ID가 프로비전되면 아래 단계에 따라 허브의 리소스 ID에 RBAC 권한을 부여하여 서비스 버스에 액세스할 수 있습니다.

1. Azure 포털에서 서비스 버스의 **IAM(액세스 제어)** 탭으로 이동하여 **역할 할당 추가** 섹션 아래에서 **추가를** 클릭합니다.

2. **서비스 버스 데이터 보낸 자를** **역할로** **선택합니다.** **Assigning access to** **저장** 단추를 클릭합니다.

3. 서비스 버스의 **방화벽 및 가상 네트워크** 탭으로 이동하여 선택한 네트워크 **옵션에서 액세스 허용을** 사용하도록 설정합니다. **예외** 목록에서 신뢰할 수 있는 **Microsoft 서비스가 이 서비스 버스에 액세스할 수 있도록 허용 확인란을**선택합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 메시지 **라우팅** 탭으로 이동합니다.

5. 사용자 **지정 끝점** 섹션으로 이동한 다음 **추가를**클릭합니다. **서비스 버스 큐** 또는 서비스 버스 항목(해당하는 경우)을 끝점 유형으로 선택합니다. **Service bus topic**

6. 표시되는 페이지에서 끝점의 이름을 제공하고 서비스 버스의 네임스페이스와 큐 또는 주제를 선택합니다(해당하는 경우). **만들기** 단추를 클릭합니다.

이제 사용자 지정 서비스 버스 끝점이 허브의 시스템에 할당된 ID를 사용하도록 설정되어 있으며 방화벽 제한에도 불구하고 서비스 버스 리소스에 액세스할 수 있는 권한이 있습니다. 이제 이 끝점을 사용하여 라우팅 규칙을 설정할 수 있습니다.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>파일 업로드를 위해 저장소 계정에 대한 연결을 송귀화

IoT Hub의 파일 업로드 기능을 사용하면 장치에서 고객 소유 저장소 계정에 파일을 업로드할 수 있습니다. 파일 업로드가 작동하도록 하려면 장치와 IoT Hub 모두 저장소 계정에 연결해야 합니다. 저장소 계정에 방화벽 제한이 있는 경우 장치는 지원되는 저장소 계정의 [메커니즘(개인 끝점,](../private-link/create-private-endpoint-storage-portal.md) [서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 또는 [직접 방화벽 구성](../storage/common/storage-network-security.md)포함)을 사용하여 연결을 확보해야 합니다. 마찬가지로 저장소 계정에 방화벽 제한이 있는 경우 신뢰할 수 있는 Microsoft 서비스 예외를 통해 저장소 리소스에 액세스하도록 IoT Hub를 구성해야 합니다. 이를 위해 IoT Hub에는 관리되는 서비스 ID가 있어야 합니다(관리되는 서비스 [ID가 있는 허브를 만드는](#create-an-iot-hub-with-managed-service-identity)방법 참조). 관리되는 서비스 ID가 프로비전되면 아래 단계에 따라 허브의 리소스 ID에 RBAC 권한을 부여하여 저장소 계정에 액세스할 수 있습니다.

1. Azure 포털에서 저장소 계정의 **IAM(액세스 제어)** 탭으로 이동하여 **역할 할당 추가** 섹션 아래에 있는 **추가를** 클릭합니다.

2. **저장소 Blob 데이터 기여자를** **역할,** **Azure AD 사용자, 그룹 또는 서비스 주체로** 선택하여 **액세스 권한을 할당하고** 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 단추를 클릭합니다.

3. 저장소 계정의 **방화벽 및 가상 네트워크** 탭으로 이동하여 선택한 네트워크 **옵션에서 액세스 허용을** 사용하도록 설정합니다. **예외** 목록에서 신뢰할 수 있는 **Microsoft 서비스가 이 저장소 계정에 액세스할 수 있도록 허용 확인란을**선택합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 파일 **업로드** 탭으로 이동합니다.

5. 표시되는 페이지에서 Blob 저장소에서 사용할 컨테이너를 선택하고 **파일 알림 설정,** **SAS TTL,** 기본 **TTL** 및 **최대 배달 수를** 원하는 대로 구성합니다. 저장소 끝점에 **인증 유형으로** **할당된 시스템을** 선택합니다. **만들기** 단추를 클릭합니다.

이제 파일 업로드를 위한 저장소 끝점이 허브의 시스템에 할당된 ID를 사용하도록 설정되어 있으며 방화벽 제한에도 불구하고 저장소 리소스에 액세스할 수 있는 권한이 있습니다.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>대량 장치 가져오기/내보내기를 위해 저장소 계정에 대한 연결을 송신합니다.

IoT Hub는 고객이 제공한 저장소 Blob에서/내보내는 장치의 정보를 대량으로 [가져오/내보내는](./iot-hub-bulk-identity-mgmt.md) 기능을 지원합니다. 대량 가져오기/내보내기 기능이 작동하도록 하려면 장치와 IoT Hub 모두 저장소 계정에 연결해야 합니다.

이 기능을 사용하려면 IoT Hub에서 저장소 계정에 연결해야 합니다. 방화벽 제한이 있는 동안 서비스 버스 리소스에 액세스하려면 IoT Hub에 관리되는 서비스 ID가 있어야 [합니다(관리되는 서비스 ID가 있는 허브를 만드는](#create-an-iot-hub-with-managed-service-identity)방법 참조). 관리되는 서비스 ID가 프로비전되면 아래 단계에 따라 허브의 리소스 ID에 RBAC 권한을 부여하여 서비스 버스에 액세스할 수 있습니다.

1. Azure 포털에서 저장소 계정의 **IAM(액세스 제어)** 탭으로 이동하여 **역할 할당 추가** 섹션 아래에 있는 **추가를** 클릭합니다.

2. **저장소 Blob 데이터 기여자를** **역할,** **Azure AD 사용자, 그룹 또는 서비스 주체로** 선택하여 **액세스 권한을 할당하고** 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 단추를 클릭합니다.

3. 저장소 계정의 **방화벽 및 가상 네트워크** 탭으로 이동하여 선택한 네트워크 **옵션에서 액세스 허용을** 사용하도록 설정합니다. **예외** 목록에서 신뢰할 수 있는 **Microsoft 서비스가 이 저장소 계정에 액세스할 수 있도록 허용 확인란을**선택합니다. **저장** 단추를 클릭합니다.

이제 대량 가져오기/내보내기 기능을 사용하는 방법에 대한 자세한 내용은 가져오기 [내보내기 작업을 만들기](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) 위해 Azure IoT REST API를 사용할 수 있습니다. 요청 본문에 를 `storageAuthenticationType="identityBased"` 제공하고 저장소 계정의 `inputBlobContainerUri="https://..."` 입력 `outputBlobContainerUri="https://..."` 및 출력 URL로 사용해야 합니다.


Azure IoT Hub SDK는 서비스 클라이언트의 레지스트리 관리자에서도 이 기능을 지원합니다. 다음 코드 조각은 C# SDK를 사용하여 가져오기 작업을 시작하거나 작업을 내보내는 방법을 보여 주며 있습니다.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


C#, Java 및 Node.js에 대한 가상 네트워크 지원을 사용하여 이 리전 제한 버전의 Azure IoT SDK를 사용하려면 다음을 수행하십시오.

1. 라는 `EnableStorageIdentity` 환경 변수를 만들고 해당 `1`값을 으로 설정합니다.

2. SDK 다운로드: [자바](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
파이썬의 경우 GitHub에서 제한된 버전을 다운로드하십시오.

1. [GitHub 릴리스 페이지로](https://aka.ms/vnetpythonsdk)이동합니다.

2. 다음 파일을 다운로드하여 릴리스 페이지 하단에 있는 **자산이라는**헤더 아래에 있습니다.
    > *azure_iot_hub-2.2.0_limited py2.py3-none-any-whl*

3. 터미널을 열고 다운로드한 파일이 있는 폴더로 이동합니다.

4. 다음 명령을 실행하여 가상 네트워크를 지원하는 Python Service SDK를 설치합니다.
    > 핍 설치 ./azure_iot_hub-2.2.0_limited py2.py3-none-any.whl


## <a name="next-steps"></a>다음 단계

IoT Hub 기능에 대해 자세히 알아보려면 아래 링크를 사용하십시오.

* [메시지 라우팅](./iot-hub-devguide-messages-d2c.md)
* [파일 업로드](./iot-hub-devguide-file-upload.md)
* [대량 장치 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md) 
