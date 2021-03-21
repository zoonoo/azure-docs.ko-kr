---
title: Azure IoT Hub IP 필터 | Microsoft Docs
description: IP 필터링을 사용 하 여의 특정 IP 주소에서 Azure IoT hub로의 연결을 허용 하는 방법입니다.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: jlian
ms.openlocfilehash: 2a76cede4bc72da9f30564f98ab9bb84028680f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581493"
---
# <a name="use-ip-filters"></a>IP 필터 사용

보안은 Azure IoT Hub를 기반으로 하는 모든 IoT 솔루션의 중요한 측면입니다. 경우에 따라 디바이스가 보안 구성의 일부로 연결할 수 있는 IP 주소를 명시적으로 지정해야 합니다. *IP 필터* 기능을 사용하면 특정 IPv4 주소에서 들어오는 트래픽을 거부하거나 수락하는 규칙을 구성할 수 있습니다.

## <a name="when-to-use"></a>사용 시기

IP 필터를 사용 하 여 지정 된 범위의 IP 주소 에서만 트래픽을 수신 하 고 다른 모든 항목을 거부 합니다. 예를 들어 iot hub와 [Azure Express 경로](../expressroute/expressroute-faqs.md#supported-services) 를 사용 하 여 iot hub와 온-프레미스 인프라 간의 개인 연결을 만들 수 있습니다.

## <a name="default-setting"></a>기본 설정

IP 필터 설정 페이지로 이동하려면 **네트워킹**, **공용 액세스** 를 차례로 선택한 다음, **선택한 IP 범위** 를 선택합니다.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub 기본 IP 필터 설정":::

기본적으로 IoT Hub에 대한 포털의 **IP 필터** 그리드는 비어있습니다. 이 기본 설정은 허브에서 모든 IP 주소의 연결을 차단 한다는 의미입니다. 이 기본 설정은 IP 주소 범위를 차단 하는 규칙과 동일 `0.0.0.0/0` 합니다.

## <a name="add-or-edit-an-ip-filter-rule"></a>IP 필터 규칙 추가 또는 편집

IP 필터 규칙을 추가하려면 **+ IP 필터 규칙 추가** 를 선택합니다.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="IP 필터 규칙을 IoT 허브에 추가":::

**IP 필터 규칙 추가** 를 선택한 후 필드를 입력합니다.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="IP 필터 규칙 추가를 선택한 후":::

* IP 필터 규칙의 **이름** 을 제공합니다. 이 이름은 128 자까지 대/소문자를 구분 하지 않는 고유 영숫자 문자열 이어야 합니다. ASCII 7 비트 영숫자 및 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`만 허용됩니다.

* 단일 IPv4 주소 또는 CIDR 표기법으로 IP 주소 블록을 제공합니다. 예를 들어 CIDR 표기법 192.168.100.0/22는 192.168.100.0에서 192.168.103.255까지 IPv4 주소 1024개를 나타냅니다.

필드를 채운 후 **저장** 을 선택하여 규칙을 저장합니다. 업데이트가 진행 중임을 알리는 경고가 표시됩니다.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="IP 필터 규칙 저장에 대한 알림":::

최대 10개의 IP 필터 규칙에 도달하면 **추가** 옵션이 비활성화됩니다.

기존 규칙을 편집하려면 변경하려는 데이터를 선택하고 변경 내용을 적용한 다음, **저장** 을 선택하여 편집 내용을 저장합니다.

## <a name="delete-an-ip-filter-rule"></a>IP 필터 규칙 삭제

IP 필터 규칙을 삭제하려면 해당 행에서 휴지통 아이콘을 선택하고 **저장** 을 선택합니다. 규칙이 제거되고 변경 내용이 저장됩니다.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="IoT Hub IP 필터 규칙 삭제":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>기본 제공 Event Hub 호환 끝점에 IP 필터 규칙 적용

IP 필터 규칙을 기본 제공 Event Hub 호환 끝점에 적용 하려면 **기본 제공 끝점에 ip 필터 적용** 옆의 확인란을 선택 하 고 **저장** 을 선택 합니다.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="기본 제공 끝점에 대 한 토글을 표시 하는 이미지 및 저장":::

> [!NOTE]
> 이 옵션은 무료 (F1) IoT hub에서 사용할 수 없습니다. 기본 제공 끝점에 IP 필터 규칙을 적용 하려면 유료 IoT hub를 사용 합니다.

이 옵션을 사용 하도록 설정 하면 IP 필터 규칙이 기본 제공 끝점에 복제 되므로 신뢰할 수 있는 IP 범위만 액세스할 수 있습니다.

이 옵션을 사용 하지 않도록 설정 하면 모든 IP 주소에서 기본 제공 끝점에 액세스할 수 있습니다. 이 동작은 Azure Stream Analytics 같이 IP 주소를 변경 하는 서비스를 사용 하 여 끝점에서 읽으려고 하는 경우에 유용할 수 있습니다. 

## <a name="how-filter-rules-are-applied"></a>필터 규칙이 적용되는 방식

IP 필터 규칙은 IoT Hub 서비스 수준에 적용됩니다. 따라서 IP 필터 규칙은 지원되는 모든 프로토콜을 사용하는 디바이스 및 백 엔드 앱의 모든 연결에 적용됩니다. 또한 [기본 제공 Event Hub 호환 끝점](iot-hub-devguide-messages-read-builtin.md) (IoT Hub 연결 문자열을 통해)이 이러한 규칙에 바인딩되어 있는지 여부를 선택할 수 있습니다. 

명시적으로 허용 되지 않는 IP 주소에서의 모든 연결 시도에는 권한이 없는 401 상태 코드 및 설명이 수신 됩니다. 응답 메시지는 IP 규칙을 언급하지 않습니다. IP 주소를 거부하면 다른 Azure 서비스(예: Azure Stream Analytics, Azure Virtual Machines 또는 Azure Portal의 Device Explorer)가 IoT 허브와 상호 작용하는 것을 막을 수 있습니다.

> [!NOTE]
> IP 필터를 사용 하는 IoT hub에서 메시지를 읽으려면 Azure Stream Analytics (사용자)를 사용 해야 하는 경우 **기본 제공 끝점에 ip 필터 적용** 옵션을 사용 **하지 않도록 설정** 하 고, IoT hub의 이벤트 허브 호환 이름 및 끝점을 사용 하 여 수동으로 [Event Hubs 스트림 입력](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) 을 추가 합니다.

### <a name="ordering"></a>순서 지정

IP 필터 규칙은 순서 없이 *허용* 규칙 및 적용 됩니다. 추가한 IP 주소만 IoT Hub에 연결할 수 있습니다. 

예를 들어 범위에서 주소를 수락 하 `192.168.100.0/22` 고 다른 모든 항목을 거부 하려는 경우에는 주소 범위가 있는 표에 규칙을 하나 추가 하기만 하면 `192.168.100.0/22` 됩니다.

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Azure CLI를 사용하여 IP 필터 검색 및 업데이트

IoT Hub의 IP 필터는 [Azure  CLI](/cli/azure/)를 통해 검색 및 업데이트할 수 있습니다.

IoT Hub의 현재 IP 필터를 검색하려면 다음을 실행합니다.

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

그러면 JSON 개체로 돌아가고 기존 IP 필터가 `properties.networkRuleSets` 키 아래에 나열됩니다.

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

IoT Hub에 대한 새 IP 필터를 추가하려면 다음을 실행합니다.

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

IoT Hub의 기존 IP 필터를 제거하려면 다음을 실행합니다.

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

여기서는 `<ipFilterIndexToRemove>` IoT Hub의 IP 필터 순서와 일치 해야 합니다 `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Azure PowerShell을 사용하여 IP 필터 검색 및 업데이트

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

IoT Hub의 IP 필터는 [Azure PowerShell](/powershell/azure/)을 통해 검색 및 설정할 수 있습니다.

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>REST를 사용하여 IP 필터 규칙 업데이트


또한 Azure 리소스 공급자의 REST 엔드포인트를 사용하여 IoT 허브의 IP 필터를 검색 및 수정할 수도 있습니다. [createorupdate 메서드](/rest/api/iothub/iothubresource/createorupdate)의 `properties.networkRuleSets`를 참조하세요.

## <a name="ip-filter-classic-retirement"></a>IP 필터 (클래식) 사용 중지

클래식 IP 필터가 사용 중지 되었습니다. 자세히 알아보려면 [IoT Hub 클래식 IP 필터 및 업그레이드 방법](iot-hub-ip-filter-classic.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 메트릭](./monitor-iot-hub.md)
* [Private Link 및 관리 ID를 사용하는 가상 네트워크에 대한 IoT Hub 지원](virtual-network-support.md)
* [IoT hub에 대 한 공용 네트워크 액세스 관리](iot-hub-public-network-access.md)
* [IoT Hub 모니터링](monitor-iot-hub.md)
