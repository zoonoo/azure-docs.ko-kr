---
title: 가상 네트워크에 대 한 Azure IoT (장치 프로 비전 서비스) 지원
description: Azure IoT 장치 프로 비전 서비스 (DPS)에서 가상 네트워크 연결 패턴을 사용 하는 방법
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: 43d7b3ae906909312a9e9ec4517061a788267a0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612781"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>가상 네트워크에 대 한 DPS (Azure IoT Hub 장치 프로 비전 서비스) 지원

이 문서에서는 DPS를 사용 하 여 IoT hub를 프로 비전 하는 IoT 장치에 대 한 VNET (virtual network) 연결 패턴을 소개 합니다. 이 패턴은 고객 소유 Azure VNET 내의 장치, DPS 및 IoT hub 간에 개인 연결을 제공 합니다. 

DPS가 VNET을 사용 하 여 구성 된 대부분의 시나리오에서는 동일한 VNET에도 IoT Hub 구성 됩니다. VNET 지원 및 IoT Hub의 구성에 대 한 자세한 내용은 [IoT Hub virtual network 지원](../iot-hub/virtual-network-support.md)을 참조 하세요.



## <a name="introduction"></a>소개

기본적으로 DPS 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소를 사용 하는 공용 끝점에 매핑됩니다. 이 공용 끝점은 모든 고객에 게 표시 됩니다. 공용 끝점에 대 한 액세스는 온-프레미스 네트워크 뿐만 아니라 광역 네트워크를 통해 IoT 장치에서 시도할 수 있습니다.

여러 가지 이유로, 고객은 DPS와 같은 Azure 리소스에 대 한 연결을 제한 하고자 할 수 있습니다. 그 이유는 다음과 같습니다.

* 공용 인터넷을 통한 연결 노출을 방지 합니다. IoT hub 및 DPS 리소스에 대 한 네트워크 수준 격리를 통해 추가 보안 계층을 도입 하 여 노출을 줄일 수 있습니다.

* 온-프레미스 네트워크 자산에서 프라이빗 연결 환경을 사용하도록 설정하여 데이터와 트래픽이 Azure 백본 네트워크로 직접 전송되도록 합니다.

* 중요한 온-프레미스 네트워크에서의 반출 공격을 방지합니다. 

* [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 Azure 전역의 연결 패턴을 설정합니다.

연결을 제한 하는 일반적인 방법으로는 [DPS IP 필터 규칙](./iot-dps-ip-filtering.md) 및 [개인 끝점이](../private-link/private-endpoint-overview.md)있는 가상 네트워킹 (VNET)이 있습니다. 이 문서의 목표는 개인 끝점을 사용 하는 DPS에 대 한 VNET 접근 방식을 설명 하는 것입니다. 

온-프레미스 네트워크에서 작동 하는 장치는 [VPN (가상 사설망)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 또는 [express](https://azure.microsoft.com/services/expressroute/) 경로 개인 피어 링을 사용 하 여 Azure의 VNET에 연결 하 고 개인 끝점을 통해 DPS 리소스에 액세스할 수 있습니다. 

개인 끝점은 Azure 리소스에 액세스할 수 있는 고객 소유의 VNET 내에 할당 된 개인 IP 주소입니다. DPS 리소스에 대 한 개인 끝점을 사용 하 여 VNET 내에서 작동 하는 장치가 공용 끝점에 대 한 트래픽을 허용 하지 않고 DPS 리소스에의 한 프로 비전을 요청할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

계속하기 전에 다음과 같은 전제 조건을 충족하는지 확인하세요.

* DPS 리소스가 이미 만들어져 IoT hub에 연결 되어 있습니다. 새 DPS 리소스를 설정 하는 방법에 대 한 지침은 [Azure Portal를 사용 하 여 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md) 을 참조 하세요.

* 개인 끝점이 생성 되는 서브넷을 사용 하 여 Azure VNET을 프로 비전 했습니다. 자세한 내용은 [Azure CLI를 사용 하 여 가상 네트워크 만들기](../virtual-network/quick-create-cli.md)를 참조 하세요.

* 온-프레미스 네트워크 내에서 작동 하는 장치의 경우 Azure VNET에 [VPN (가상 사설망)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 또는 [express](https://azure.microsoft.com/services/expressroute/) 경로 개인 피어 링을 설정 합니다.

## <a name="private-endpoint-limitations"></a>개인 끝점 제한 사항

개인 끝점을 사용 하는 경우 DPS에 대 한 현재 제한 사항은 다음과 같습니다.

* DPS 리소스와 연결 된 허브가 다른 클라우드에 있는 경우 개인 끝점은 DPS와 작동 하지 않습니다. 예를 들어 [Azure Government 및 글로벌 Azure](../azure-government/documentation-government-welcome.md)입니다.

* 현재는 Azure function이 VNET 및 개인 끝점으로 잠긴 경우 DPS에 대 한 Azure Functions를 사용 하는 [사용자 지정 할당 정책이](how-to-use-custom-allocation-policies.md) 작동 하지 않습니다. 

* 현재 DPS VNET 지원은 DPS로만 데이터를 수신 하는 데 사용할 수 있습니다. DPS에서 IoT Hub로 전송 되는 데이터 송신은 전용 VNET이 아닌 내부 서비스 간 메커니즘을 사용 합니다. DPS와 IoT Hub 간의 전체 VNET 기반 송신 잠금에 대 한 지원은 현재 사용할 수 없습니다.

* 가장 낮은 대기 시간 할당 정책은 가장 짧은 대기 시간을 사용 하 여 IoT hub에 장치를 할당 하는 데 사용 됩니다. 이 할당 정책은 가상 네트워크 환경에서 안정적이 지 않습니다. 

## <a name="set-up-a-private-endpoint"></a>개인 끝점 설정

개인 끝점을 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 DPS 리소스를 열고 **네트워킹** 탭을 클릭 합니다. **개인 끝점 연결** 및 **+ 개인 끝점**을 클릭 합니다.

    ![DPS에 대 한 새 개인 끝점 추가](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. _개인 끝점_ 기본 사항 만들기 페이지에서 아래 표에 설명 된 정보를 입력 합니다.

    ![개인 끝점 만들기 기본 사항](./media/virtual-network-support/create-private-endpoint-basics.png)

    | 필드 | 값 |
    | :---- | :-----|
    | **구독** | 원하는 Azure 구독을 선택 하 여 개인 끝점을 포함 합니다.  |
    | **리소스 그룹** | 개인 끝점을 포함할 리소스 그룹을 선택 하거나 만듭니다. |
    | **이름**       | 개인 끝점의 이름 입력 |
    | **지역**     | 선택한 지역은 VNET을 포함 하는 지역과 동일 해야 하지만 DPS 리소스와 같을 필요는 없습니다. |

    **다음: 리소스** 를 클릭 하 여 개인 끝점이 가리키는 리소스를 구성 합니다.

3. _개인 끝점 리소스 만들기_ 페이지에서 아래 표에 설명 된 정보를 입력 합니다.

    ![개인 끝점 리소스 만들기](./media/virtual-network-support/create-private-endpoint-resource.png)

    | 필드 | 값 |
    | :---- | :-----|
    | **구독**        | 개인 끝점이 가리키는 DPS 리소스를 포함 하는 Azure 구독을 선택 합니다.  |
    | **리소스 종류**       | **Microsoft. Devices/ProvisioningServices**를 선택 합니다. |
    | **리소스**            | 개인 끝점이 매핑될 DPS 리소스를 선택 합니다. |
    | **대상 하위 리소스** | **Iotdps**를 선택 합니다. |

    > [!TIP]
    > **리소스 ID 또는 별칭으로 Azure 리소스에 연결** 설정에 대 한 정보는이 문서의 [개인 끝점 요청](#request-a-private-endpoint) 섹션에 제공 됩니다.


    **다음: 구성** 을 클릭 하 여 개인 끝점에 대 한 VNET을 구성 합니다.

4. _개인 끝점 구성 만들기_ 페이지에서 개인 끝점을 만들 가상 네트워크 및 서브넷을 선택 합니다.
 
    **다음: 태그**를 클릭 하 고 필요에 따라 리소스에 대 한 태그를 제공 합니다.

    ![개인 끝점 구성](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. **검토 + 만들기** 를 클릭 한 다음 **만들기** 를 클릭 하 여 개인 끝점 리소스를 만듭니다.


## <a name="request-a-private-endpoint"></a>개인 끝점 요청

리소스 ID로 DPS 리소스에 대 한 개인 끝점을 요청할 수 있습니다. 이 요청을 수행 하려면 리소스 소유자가 리소스 ID를 제공 해야 합니다. 

1. 리소스 ID는 아래와 같이 DPS 리소스의 속성 탭에 제공 됩니다.

    ![DPS 속성 탭](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > 리소스 ID에는 구독 ID가 포함 되어 있습니다. 

2. 리소스 ID를 만들었으면 _개인 끝점 리소스 만들기_ 페이지에서 3 단계에 대 한 [개인 끝점 설정](#set-up-a-private-endpoint) 에서 위의 단계를 따릅니다. **리소스 ID 또는 별칭으로 Azure 리소스에 연결** 을 클릭 하 고 다음 표에 나와 있는 정보를 입력 합니다. 

    | 필드 | 값 |
    | :---- | :-----|
    | **리소스 ID 또는 별칭** | DPS 리소스의 리소스 ID를 입력 합니다. |
    | **대상 하위 리소스** | **Iotdps** 입력 |
    | **요청 메시지** | DPS 리소스 소유자에 대 한 요청 메시지를 입력 합니다.<br>예를 들면 다음과 같습니다. <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    **다음: 구성** 을 클릭 하 여 개인 끝점에 대 한 VNET을 구성 합니다.

3. _개인 끝점 구성 만들기_ 페이지에서 개인 끝점을 만들 가상 네트워크 및 서브넷을 선택 합니다.
 
    **다음: 태그**를 클릭 하 고 필요에 따라 리소스에 대 한 태그를 제공 합니다.

4. **검토 + 만들기** 를 클릭 한 다음 **만들기** 를 클릭 하 여 개인 끝점 요청을 만듭니다.

5. Dps 소유자는 DPS 네트워킹 탭의 **개인 끝점 연결** 목록에서 개인 끝점 요청을 볼 수 있습니다. 해당 페이지에서 소유자는 아래와 같이 개인 끝점 요청을 **승인** 하거나 **거부할** 수 있습니다.

    ![DPS 승인](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>가격 책정 개인 끝점

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.



## <a name="next-steps"></a>다음 단계

다음 링크를 사용 하 여 DPS 보안 기능에 대해 자세히 알아보세요.

* [보안](concepts-security.md)
* [TLS 1.2 지원](tls-support.md)
