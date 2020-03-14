---
title: 가상 네트워크에 대 한 Azure IoT Hub 지원
description: IoT Hub에서 가상 네트워크 연결 패턴을 사용 하는 방법
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 1b250bee302cb305ee613010238283ceac4014ed
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375084"
---
# <a name="iot-hub-support-for-virtual-networks"></a>가상 네트워크에 대 한 IoT Hub 지원

이 문서에서는 고객 소유 Azure VNET을 통해 IoT hub에 대 한 개인 연결 환경을 설정 하는 방법에 대 한 VNET 연결 패턴 및 대해 중점적을 소개 합니다.

> [!NOTE]
> 이 문서에서 설명 하는 IoT hub 기능은 현재 미국 동부, 미국 서 부 및 미국 서 부 2 지역에서 만든 IoT Hub 사용할 수 있습니다.


## <a name="introduction"></a>소개

기본적으로 IoT Hub 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소를 사용 하는 공용 끝점에 매핑됩니다. 아래 그림에 표시 된 것 처럼이 IoT Hub 공용 끝점은 서로 다른 고객이 소유한 허브 간에 공유 되며, 광역 네트워크와 온-프레미스 네트워크를 통해 IoT 장치에서 액세스할 수 있습니다.

[메시지 라우팅](./iot-hub-devguide-messages-d2c.md), [파일 업로드](./iot-hub-devguide-file-upload.md)및 [대량 장치 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md) 를 비롯 한 몇 가지 IoT Hub 기능에는 IoT Hub에서 공용 끝점을 통해 고객 소유의 Azure 리소스에 대 한 연결이 필요 합니다. 아래 그림과 같이 이러한 연결 경로는 IoT Hub에서 고객 리소스로의 송신 트래픽을 전체적으로 구성 합니다.
공용 끝점을 IoT Hub ![](./media/virtual-network-support/public-endpoint.png)


여러 가지 이유로 고객은 자신이 소유 하 고 작동 하는 VNET을 통해 Azure 리소스 (IoT Hub 포함)에 대 한 연결을 제한 하고자 할 수 있습니다. 이러한 이유는 다음과 같습니다.

* 공용 인터넷을 통해 허브에 대 한 연결 노출을 방지 하 여 IoT hub에 대 한 네트워크 수준 격리를 통해 보안의 추가 계층을 도입 합니다.

* 온-프레미스 네트워크 자산의 개인 연결 환경을 사용 하도록 설정 하 여 데이터와 트래픽이 Azure 백본 네트워크로 직접 전송 되도록 합니다.

* 중요 한 온-프레미스 네트워크에서의 반출 공격 방지 

* 다음은 [개인 끝점](../private-link/private-endpoint-overview.md)을 사용 하 여 Azure 전체 연결 패턴을 설정 하는 것입니다.


이 문서에서는 IoT Hub IoT Hub에 대 한 수신 연결을 위한 [개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 이러한 목표를 달성 하는 방법을 설명 합니다.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>전용 끝점을 사용 하 여 IoT Hub에 대 한 수신 연결

개인 끝점은 Azure 리소스에 연결할 수 있는 고객 소유의 VNET 내에 할당 된 개인 IP 주소입니다. IoT hub에 대 한 개인 끝점을 사용 하면 IoT Hub의 공용 끝점으로 트래픽을 전송 하지 않고도 VNET 내에서 작동 하는 서비스를 IoT Hub에 도달할 수 있습니다. 마찬가지로 온-프레미스에서 작동 하는 장치는 VPN ( [가상 사설망](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ) 또는 [Express](https://azure.microsoft.com/services/expressroute/) 경로 개인 피어 링을 사용 하 여 Azure에서 VNET에 연결 하 고 이후에 IoT Hub (개인 끝점을 통해)에 연결할 수 있습니다. 따라서 IoT hub의 공용 끝점에 대 한 연결을 제한 (또는 완전히 차단) 하려는 고객은 개인 끝점을 사용 하 여 허브에 대 한 연결을 유지 하면서 [IoT Hub 방화벽 규칙](./iot-hub-ip-filtering.md) 을 사용 하 여이 목표를 달성할 수 있습니다.

> [!NOTE]
> 이 설정의 주요 초점은 온-프레미스 네트워크 내의 장치에 대 한 것입니다. 광역 네트워크에 배포 된 장치에는이 설정이 권장 되지 않습니다.

![IoT Hub 공용 끝점](./media/virtual-network-support/virtual-network-ingress.png)

계속 하기 전에 다음 필수 구성 요소를 충족 하는지 확인 합니다.

* IoT hub는 [지원 되는 지역](#regional-availability-private-endpoints)중 하나에 프로 비전 되어야 합니다.

* 개인 끝점이 생성 되는 서브넷을 사용 하 여 Azure VNET을 프로 비전 했습니다. 자세한 내용은 [Azure CLI를 사용 하 여 가상 네트워크 만들기를](../virtual-network/quick-create-cli.md) 참조 하세요.

* 온-프레미스 네트워크 내에서 작동 하는 장치의 경우 Azure VNET에 [VPN (가상 사설망)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 또는 [express](https://azure.microsoft.com/services/expressroute/) 경로 개인 피어 링을 설정 합니다.


### <a name="regional-availability-private-endpoints"></a>지역별 가용성 (전용 끝점)

IoT Hub에서 지원 되는 개인 끝점은 다음 지역에서 생성 됩니다.

* 미국 동부

* 미국 중남부

* 미국 서부 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>IoT Hub 수신에 대 한 개인 끝점 설정

개인 끝점을 설정 하려면 다음 단계를 수행 합니다.

1. 다음 Azure CLI 명령을 실행 하 여 구독에 Azure IoT Hub 공급자를 다시 등록 합니다.

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. IoT Hub 포털에서 **개인 끝점 연결** 탭으로 이동 합니다 (이 탭은 [지원 되는 지역의](#regional-availability-private-endpoints)IoT hub 에서만 사용할 수 있음). **+** 기호를 클릭 하 여 새 개인 끝점을 추가 합니다.

3. 에서 새 개인 끝점을 만들 구독, 리소스 그룹, 이름 및 지역을 제공 합니다. 이상적으로는 허브와 동일한 지역에 개인 끝점이 만들어져야 합니다. 자세한 내용은 [지역별 가용성 섹션](#regional-availability-private-endpoints) 을 참조 하세요.

4. **다음: 리소스**를 클릭 하 고 IoT Hub 리소스에 대 한 구독을 제공 하 고 **"Microsoft. Devices/IotHubs"** 를 리소스 유형으로, IoT Hub 이름을 **리소스로**, **iotHub** 를 대상 하위 리소스로 선택 합니다.

5. **다음: 구성** 을 클릭 하 고 가상 네트워크 및 서브넷을 제공 하 여에서 개인 끝점을 만듭니다. 원하는 경우 Azure 개인 DNS 영역과 통합 하는 옵션을 선택 합니다.

6. **다음: 태그**를 클릭 하 고 필요에 따라 리소스에 대 한 태그를 제공 합니다.

7. **검토 + 만들기** 를 클릭 하 여 개인 끝점 리소스를 만듭니다.


### <a name="pricing-private-endpoints"></a>가격 (전용 끝점)

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub에서 다른 Azure 리소스로의 송신 연결

IoT Hub는 Azure blob 저장소, event hubs, [메시지 라우팅](./iot-hub-devguide-messages-d2c.md)에 대 한 service bus 리소스, [파일 업로드](./iot-hub-devguide-file-upload.md)및 [대량 장치 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md)에 대 한 액세스 권한이 필요 합니다 .이는 일반적으로 리소스의 공용 끝점에 대해 발생 합니다. 저장소 계정, event hubs 또는 service bus 리소스를 VNET에 바인딩하는 경우 advise 구성은 기본적으로 리소스에 대 한 연결을 차단 합니다. 따라서 이러한 리소스에 액세스 해야 하는 IoT Hub 기능을 방해 합니다.

이러한 상황을 줄이려면 **Azure 자사 신뢰할 수 있는 서비스** 옵션을 통해 IoT Hub 리소스에서 저장소 계정, event hubs 또는 service bus 리소스에 연결할 수 있도록 설정 해야 합니다.

필수 구성 요소는 다음과 같습니다.

* IoT hub는 [지원 되는 지역](#regional-availability-trusted-microsoft-first-party-services)중 하나에 프로 비전 되어야 합니다.

* IoT Hub에는 허브 프로 비전 시간에 관리 서비스 id를 할당 해야 합니다. [관리 서비스 id를 사용 하 여 허브를 만드는](#create-a-hub-with-managed-service-identity)방법에 대 한 지침을 따르세요.


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>지역별 가용성 (신뢰할 수 있는 Microsoft 자사 서비스)

Azure trusted first 파티 서비스 예외 Azure storage에 대 한 방화벽 제한을 무시 하는 예외, event hubs 및 service bus 리소스는 다음 지역의 IoT Hub에만 지원 됩니다.

* 미국 동부

* 미국 중남부

* 미국 서부 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>가격 책정 (신뢰할 수 있는 Microsoft 자사 서비스)

신뢰할 수 있는 Microsoft 자사 서비스 예외 기능은 지원 되는 [지역의](#regional-availability-trusted-microsoft-first-party-services)IoT hub에서 무료로 제공 됩니다. 프로 비전 된 저장소 계정, event hubs 또는 service bus 리소스에 대 한 요금은 별도로 적용 됩니다.


### <a name="create-a-hub-with-managed-service-identity"></a>관리 서비스 id를 사용 하 여 허브 만들기

관리 서비스 id는 리소스 프로 비전 시 허브에 할당할 수 있습니다 .이 기능은 현재 기존 허브에 대해 지원 되지 않습니다. 이러한 목적을 위해 아래 ARM 리소스 템플릿을 사용 해야 합니다.

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
            "identity": { "type": "SystemAssigned" },
            "properties": { "minTlsVersion": "1.2" },
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
            "dependsOn": [ "<provide-a-valid-resource-name>" ],
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
                            "identity": { "type": "SystemAssigned" },
                            "properties": { "minTlsVersion": "1.2" },
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

리소스 `name``location`, `SKU.name` 및 `SKU.tier`에 대 한 값을 대체 한 후에는 다음을 사용 하 여 기존 리소스 그룹에 리소스를 배포 하는 데 Azure CLI를 사용할 수 있습니다.

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

리소스를 만든 후 Azure CLI를 사용 하 여 허브에 할당 된 관리 서비스 id를 검색할 수 있습니다.

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

관리 서비스 id를 사용 하 여 IoT Hub 프로 비전 되 면 해당 섹션에 따라 [저장소 계정](#egress-connectivity-to-storage-account-endpoints-for-routing), [event hubs](#egress-connectivity-to-event-hubs-endpoints-for-routing)및 [service bus](#egress-connectivity-to-service-bus-endpoints-for-routing) 리소스에 대 한 라우팅 끝점을 설정 하거나 [파일 업로드](#egress-connectivity-to-storage-accounts-for-file-upload) 및 [대량 장치 가져오기/내보내기를](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)구성 합니다.


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>라우팅을 위한 저장소 계정 끝점에 대 한 송신 연결

고객 소유의 저장소 계정으로 메시지를 라우팅하도록 IoT Hub를 구성할 수 있습니다. 방화벽 제한이 적용 되는 동안 라우팅 기능에서 저장소 계정에 액세스 하도록 허용 하려면 IoT Hub 관리 서비스 id가 있어야 합니다 ( [관리 서비스 id로 허브를 만드는](#create-a-hub-with-managed-service-identity)방법 참조). 관리 서비스 id가 프로 비전 되 면 아래 단계에 따라 저장소 계정에 액세스 하기 위해 허브의 리소스 id에 RBAC 권한을 부여 합니다.

1. Azure Portal에서 저장소 계정의 **액세스 제어 (IAM)** 탭으로 이동 하 고 **역할 할당 추가** 섹션에서 **추가** 를 클릭 합니다.

2. 드롭다운 목록에서에 대 한 **액세스를 할당** 하 고 IoT Hub의 리소스 이름을 선택 하는 **역할**, **Azure AD 사용자, 그룹 또는 서비스 사용자** 로 **저장소 Blob 데이터 참가자** 를 선택 합니다. **저장** 단추를 클릭합니다.

3. 저장소 계정에서 **방화벽 및 가상 네트워크** 탭으로 이동 하 고 **선택한 네트워크에서 액세스 허용** 옵션을 사용 하도록 설정 합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용**확인란을 선택 합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 **메시지 라우팅** 탭으로 이동 합니다.

5. **사용자 지정 끝점** 섹션으로 이동 하 고 **추가**를 클릭 합니다. **저장소** 를 끝점 유형으로 선택 합니다.

6. 표시 되는 페이지에서 끝점에 대 한 이름을 제공 하 고, blob 저장소에서 사용 하려는 컨테이너를 선택 하 고, 인코딩 및 파일 이름을 지정 합니다. 저장소 끝점에 대 한 **인증 유형** 으로 **할당 된 시스템** 을 선택 합니다. **만들기** 단추를 클릭합니다.

이제 사용자 지정 저장소 끝점은 허브의 시스템 할당 id를 사용 하도록 설정 되며, 방화벽 제한에도 불구 하 고 저장소 리소스에 액세스할 수 있는 권한이 있습니다. 이제이 끝점을 사용 하 여 라우팅 규칙을 설정할 수 있습니다.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>라우팅을 위한 event hubs 끝점에 대 한 송신 연결

고객 소유 event hubs 네임 스페이스로 메시지를 라우팅하도록 IoT Hub를 구성할 수 있습니다. 방화벽 제한이 적용 되는 동안 라우팅 기능에서 event hubs 리소스에 액세스할 수 있도록 하려면 IoT Hub 관리 서비스 id가 있어야 합니다 ( [관리 서비스 id로 허브를 만드는](#create-a-hub-with-managed-service-identity)방법 참조). 관리 서비스 id가 프로 비전 되 면 아래 단계에 따라 허브의 리소스 id에 대 한 RBAC 권한을 제공 하 여 event hubs에 액세스 합니다.

1. Azure Portal에서 event hubs **Access control (IAM)** 탭으로 이동 하 여 **역할 할당 추가** 섹션 아래에서 **추가** 를 클릭 합니다.

2. **에 액세스를 할당** 하 고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택 하 여 **역할**, **Azure AD 사용자, 그룹 또는 서비스 주체로** **Event Hubs 데이터 발신자** 를 선택 합니다. **저장** 단추를 클릭합니다.

3. Event hubs의 **방화벽 및 가상 네트워크** 탭으로 이동 하 고 **선택한 네트워크에서 액세스 허용** 옵션을 사용 하도록 설정 합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스에서 event hubs에 액세스 하도록 허용**확인란을 선택 합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 **메시지 라우팅** 탭으로 이동 합니다.

5. **사용자 지정 끝점** 섹션으로 이동 하 고 **추가**를 클릭 합니다. 끝점 유형으로 **Event hubs** 를 선택 합니다.

6. 표시 되는 페이지에서 끝점의 이름을 입력 하 고 event hubs 네임 스페이스 및 인스턴스를 선택 하 고 **만들기** 단추를 클릭 합니다.

이제 사용자 지정 event hubs 끝점은 허브의 시스템 할당 id를 사용 하도록 설정 되며, 방화벽 제한에도 불구 하 고 event hubs 리소스에 액세스할 수 있는 권한이 있습니다. 이제이 끝점을 사용 하 여 라우팅 규칙을 설정할 수 있습니다.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>라우팅을 위한 service bus 끝점에 대 한 송신 연결

고객 소유의 service bus 네임 스페이스로 메시지를 라우팅하도록 IoT Hub를 구성할 수 있습니다. 방화벽 제한이 적용 되는 동안 라우팅 기능에서 service bus 리소스에 액세스할 수 있도록 하려면 IoT Hub 관리 서비스 id가 있어야 합니다 ( [관리 서비스 id로 허브를 만드는](#create-a-hub-with-managed-service-identity)방법 참조). 관리 서비스 id가 프로 비전 되 면 아래 단계에 따라 service bus에 액세스 하기 위해 허브의 리소스 id에 RBAC 권한을 부여 합니다.

1. Azure Portal에서 service bus의 **액세스 제어 (IAM)** 탭으로 이동 하 여 **역할 할당 추가** 섹션 아래에서 **추가** 를 클릭 합니다.

2. 에 대 한 **액세스를 할당** 하 고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택 하 여 **역할**, **Azure AD 사용자, 그룹 또는 서비스 사용자** 로 **서비스 버스 데이터 발신자** 를 선택 합니다. **저장** 단추를 클릭합니다.

3. Service bus에서 **방화벽 및 가상 네트워크** 탭으로 이동 하 고 **선택한 네트워크에서 액세스 허용** 옵션을 사용 하도록 설정 합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스가이 service bus에 액세스 하도록 허용**확인란을 선택 합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 **메시지 라우팅** 탭으로 이동 합니다.

5. **사용자 지정 끝점** 섹션으로 이동 하 고 **추가**를 클릭 합니다. 끝점 형식으로 **service bus 큐** 또는 **service bus 토픽** (해당 되는 경우)을 선택 합니다.

6. 표시 되는 페이지에서 끝점의 이름을 입력 하 고, 해당 하는 경우 service bus의 네임 스페이스 및 큐 또는 토픽을 선택 합니다. **만들기** 단추를 클릭합니다.

이제 사용자 지정 service bus 끝점이 허브의 시스템 할당 id를 사용 하도록 설정 되 고 방화벽 제한에도 불구 하 고 service bus 리소스에 액세스할 수 있는 권한이 있습니다. 이제이 끝점을 사용 하 여 라우팅 규칙을 설정할 수 있습니다.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>파일 업로드를 위해 저장소 계정에 대 한 송신 연결

IoT Hub의 파일 업로드 기능을 사용 하면 장치에서 고객이 소유한 저장소 계정에 파일을 업로드할 수 있습니다. 파일 업로드가 작동 하도록 허용 하려면 장치와 IoT Hub 모두 저장소 계정에 연결 되어 있어야 합니다. 저장소 계정에 방화벽 제한이 적용 되는 경우 장치에서 지원 되는 저장소 계정의 메커니즘 ( [개인 끝점](../private-link/create-private-endpoint-storage-portal.md), [서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 또는 [직접 방화벽 구성](../storage/common/storage-network-security.md)포함) 중 하나를 사용 하 여 연결 해야 합니다. 마찬가지로, 저장소 계정에 방화벽 제한이 적용 되는 경우 신뢰할 수 있는 Microsoft 서비스 예외를 통해 저장소 리소스에 액세스 하도록 IoT Hub를 구성 해야 합니다. 이러한 목적을 위해 IoT Hub에는 관리 서비스 id가 있어야 합니다 ( [관리 서비스 id로 허브를 만드는](#create-a-hub-with-managed-service-identity)방법 참조). 관리 서비스 id가 프로 비전 되 면 아래 단계에 따라 저장소 계정에 액세스 하기 위해 허브의 리소스 id에 RBAC 권한을 부여 합니다.

1. Azure Portal에서 저장소 계정의 **액세스 제어 (IAM)** 탭으로 이동 하 고 **역할 할당 추가** 섹션에서 **추가** 를 클릭 합니다.

2. 드롭다운 목록에서에 대 한 **액세스를 할당** 하 고 IoT Hub의 리소스 이름을 선택 하는 **역할**, **Azure AD 사용자, 그룹 또는 서비스 사용자** 로 **저장소 Blob 데이터 참가자** 를 선택 합니다. **저장** 단추를 클릭합니다.

3. 저장소 계정에서 **방화벽 및 가상 네트워크** 탭으로 이동 하 고 **선택한 네트워크에서 액세스 허용** 옵션을 사용 하도록 설정 합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용**확인란을 선택 합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 **파일 업로드** 탭으로 이동 합니다.

5. 표시 되는 페이지에서 blob 저장소에 사용 하려는 컨테이너를 선택 하 고, **파일 알림 설정**, **SAS TTL**, **기본 TTL** 및 **최대 배달 횟수** 를 원하는 대로 구성 합니다. 저장소 끝점에 대 한 **인증 유형** 으로 **할당 된 시스템** 을 선택 합니다. **만들기** 단추를 클릭합니다.

이제 파일 업로드에 대 한 저장소 끝점은 허브의 시스템 할당 id를 사용 하도록 설정 되 고 방화벽 제한에도 불구 하 고 저장소 리소스에 액세스할 수 있는 권한이 있습니다.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>대량 장치 가져오기/내보내기에 대 한 저장소 계정에 대 한 송신 연결

IoT Hub는 고객 제공 저장소 blob에서 장치 정보를 대량으로 [가져오거나 내보내기](./iot-hub-bulk-identity-mgmt.md) 위한 기능을 지원 합니다. 대량 가져오기/내보내기 기능이 작동 하도록 허용 하려면 장치와 IoT Hub 모두 저장소 계정에 연결 되어 있어야 합니다.

이 기능을 사용 하려면 IoT Hub에서 저장소 계정으로 연결 해야 합니다. 방화벽 제한이 적용 되는 동안 service bus 리소스에 액세스 하려면 IoT Hub 관리 서비스 id가 있어야 합니다 ( [관리 서비스 id를 사용 하 여 허브를 만드는](#create-a-hub-with-managed-service-identity)방법 참조). 관리 서비스 id가 프로 비전 되 면 아래 단계에 따라 service bus에 액세스 하기 위해 허브의 리소스 id에 RBAC 권한을 부여 합니다.

1. Azure Portal에서 저장소 계정의 **액세스 제어 (IAM)** 탭으로 이동 하 고 **역할 할당 추가** 섹션에서 **추가** 를 클릭 합니다.

2. 드롭다운 목록에서에 대 한 **액세스를 할당** 하 고 IoT Hub의 리소스 이름을 선택 하는 **역할**, **Azure AD 사용자, 그룹 또는 서비스 사용자** 로 **저장소 Blob 데이터 참가자** 를 선택 합니다. **저장** 단추를 클릭합니다.

3. 저장소 계정에서 **방화벽 및 가상 네트워크** 탭으로 이동 하 고 **선택한 네트워크에서 액세스 허용** 옵션을 사용 하도록 설정 합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용**확인란을 선택 합니다. **저장** 단추를 클릭합니다.

이제 대량 가져오기/내보내기 기능을 사용 하는 방법에 대 한 정보에 대 한 [가져오기 내보내기 작업을 만들기](https://docs.microsoft.com/rest/api/iothub/jobclient/getimportexportjobs) 위한 Azure IoT REST API를 사용할 수 있습니다. 요청 본문에 `storageAuthenticationType="identityBased"`을 제공 하 고 `inputBlobContainerUri="https://..."` 및 `outputBlobContainerUri="https://..."`을 저장소 계정의 입력 및 출력 URL로 각각 사용 해야 합니다.


Azure IoT Hub SDK는 또한 서비스 클라이언트의 레지스트리 관리자에서이 기능을 지원 합니다. 다음 코드 조각에서는 SDK를 C# 사용 하 여에서 가져오기 작업 또는 내보내기 작업을 시작 하는 방법을 보여 줍니다.

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


, Java 및 node.js에 대 한 C#VNET 지원과 함께이 지역에서 제한 된 버전의 Azure IoT sdk를 사용 하려면 다음을 수행 합니다.

1. `EnableStorageIdentity` 이라는 환경 변수를 만들고 해당 값을 `1`로 설정 합니다.

2. SDK를 다운로드 합니다.
    - > [Java](https://aka.ms/vnetjavasdk)
    - > [C#](https://aka.ms/vnetcsharsdk)
    - > [Node.JS](https://aka.ms/vnetnodesdk)
 
Python의 경우 Github에서 제한 된 버전을 다운로드 합니다.

1. [Github 릴리스 페이지로](https://aka.ms/vnetpythonsdk)이동 합니다.

2. 다음 파일을 다운로드 합니다 .이 파일은 릴리스 페이지의 아래쪽에 있는 **자산**이라는 헤더 아래에 있습니다.
    > *azure_iot_hub 2.2.0. limited-py2. py3-none-any*

3. 터미널을 열고 다운로드 한 파일이 있는 폴더로 이동 합니다.

4. 다음 명령을 실행 하 여 Vnet에 대 한 지원을 포함 하는 Python Service SDK를 설치 합니다.
    > pip install./azure_iot_hub-2.2.0. limited-py2. py3-none-any


## <a name="next-steps"></a>다음 단계

IoT Hub 기능에 대 한 자세한 내용을 보려면 아래 링크를 사용 하세요.

* [메시지 라우팅](./iot-hub-devguide-messages-d2c.md)
* [파일 업로드](./iot-hub-devguide-file-upload.md)
* [대량 장치 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md) 