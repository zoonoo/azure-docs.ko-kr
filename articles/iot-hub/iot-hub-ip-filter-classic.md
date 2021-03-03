---
title: 클래식 IP 필터 Azure IoT Hub (사용 되지 않음) | Microsoft Docs
description: 클래식 IP 필터에서 업그레이드 하는 방법 및 이점
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661157"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT Hub 클래식 IP 필터 및 업그레이드 방법 

IoT Hub에 대해 업그레이드 된 IP 필터는 기본 제공 끝점을 보호 하며 기본적으로 안전 합니다. 주요 변경 사항을 적용 하지는 않지만, 업그레이드 된 IP 필터의 강화 된 보안 모델이 클래식 IP 필터와 호환 되지 않으므로 사용 중지를 발표 합니다. 새 업그레이드 된 IP 필터에 대 한 자세한 내용은 [새로운 기능](#whats-new) 및 [IoT hub ip 필터](iot-hub-ip-filtering.md)를 참조 하세요.

서비스 중단을 방지 하려면 마이그레이션 최종 기한 전에 단계별 업그레이드를 수행 해야 합니다. 이때 업그레이드는 자동으로 수행 됩니다. 마이그레이션 타임 라인에 대 한 자세한 내용은 [Azure 업데이트](https://aka.ms/ipfilterv2azupdate)를 참조 하세요.

## <a name="how-to-upgrade"></a>업그레이드하는 방법

1.  Azure Portal 방문
2.  IoT Hub로 이동
3.  왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.
4.  IP 필터를 새 모델로 업그레이드할지 묻는 배너가 표시 됩니다. 계속하려면 **예** 를 선택합니다.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="클래식 IP 필터에서 업그레이드 하는 배너 프롬프트를 보여 주는 이미지":::
5.  새 IP 필터는 기본적으로 모든 IP를 차단 하므로, 업그레이드는 개별 거부 규칙을 제거 하지만 저장 하기 전에 검토 하는 기회를 제공 합니다. 규칙을 신중 하 게 검토 하 여 제대로 작동 하는지 확인 합니다.
6.  프롬프트에 따라 업그레이드를 완료 합니다.

## <a name="whats-new"></a>새로운 기능

### <a name="secure-by-default"></a>기본적으로 보안 적용

클래식 IP 필터를 사용 하면 기본적으로 모든 IP 주소가 IoT Hub에 연결 될 수 있으며, 가장 일반적인 네트워크 보안 시나리오와 잘 맞지 않습니다. 일반적으로는 신뢰할 수 있는 IP 주소만 IoT hub에 연결 하 고 다른 모든 항목을 거부할 수 있습니다. 클래식 IP 필터를 사용 하 여이 목표를 달성 하기 위해이 작업은 여러 단계로 진행 됩니다. 예를 들어에서 트래픽만 수락 하려면 다음을 `192.168.100.0/22` 해야 합니다.

1. 에 대해 단일 *허용* 규칙을 구성 `192.168.100.0/22` 합니다.
1. 에 대해 다른 *차단* 규칙 구성 `0.0.0.0/0` ("모두 차단" 규칙)
1. 규칙이 블록 규칙 위에 순서 대로 정렬 되어 있는지 확인 합니다.

실제로이 다중 단계 프로세스는 혼동을 일으킵니다. 사용자가 "모두 차단" 규칙을 구성 하지 않았거나 규칙을 제대로 정렬 하지 않았기 때문에 의도 하지 않은 노출이 발생 합니다. 

새 IP 필터는 기본적으로 모든 IP 주소를 차단 합니다. 명시적으로 추가 하는 IP 범위만 IoT Hub에 연결할 수 있습니다. 위의 예제에서 2 단계와 3 단계는 더 이상 필요 하지 않습니다. 이 새로운 동작으로 인해 보안이 간소화 되 고 [기본 원칙에 따라 보안이](https://wikipedia.org/wiki/Secure_by_default)유럽 연합 됩니다.

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>기본 제공 Event Hub 호환 끝점 보호

기본 제공 끝점에는 클래식 IP 필터를 적용할 수 없습니다. 이러한 제한 사항은 모든 규칙 (블록)이 구성 된 이벤트는 모든 `0.0.0.0/0` IP 주소에서 기본 제공 끝점에 액세스할 수 있음을 의미 합니다.

새 IP 필터는 기본 제공 끝점에 규칙을 적용 하는 옵션을 제공 하므로 네트워크 보안 위협에 대 한 노출을 줄일 수 있습니다.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="기본 제공 끝점에 적용할 토글을 보여 주는 이미지":::

> [!NOTE]
> 이 옵션은 무료 (F1) IoT hub에서 사용할 수 없습니다. 기본 제공 끝점에 IP 필터 규칙을 적용 하려면 유료 IoT hub를 사용 합니다.

### <a name="api-impact"></a>API 영향

업그레이드 된 IP 필터는 `2020-08-31` (및)의 IoT Hub 리소스 API에서 사용할 수 있습니다 `2020-08-31-preview` . 클래식 IP 필터는 모든 API 버전에서 계속 사용할 수 있지만 마이그레이션 최종 기한 근처의 이후 API 버전에서는 제거 될 예정입니다. 마이그레이션 타임 라인에 대 한 자세한 내용은 [Azure 업데이트](https://aka.ms/ipfilterv2azupdate)를 참조 하세요.

## <a name="tip-try-the-changes-before-they-apply"></a>팁: 변경 내용을 적용 하기 전에 다시 시도 하세요.

새 IP 필터는 모든 IP 주소를 기본적으로 차단 하므로 개별 블록 규칙은 더 이상 호환 되지 않습니다. 따라서 단계별 업그레이드 프로세스는 이러한 개별 블록 규칙을 제거 합니다. 

클래식 IP 필터를 사용 하 여 변경 하려면 다음을 수행 합니다.

1. IoT hub의 **네트워킹** 탭을 방문 합니다.
1. 롤백하는 경우 기존 IP 필터 (클래식) 구성을 적어 둡니다.
1. **블록이** 있는 규칙 옆에서 휴지통 아이콘을 선택 하 여 제거 합니다.
1. 아래쪽에 다른 규칙을 추가 하 `0.0.0.0/0` 고 **차단** 을 선택 합니다.
1. **저장** 을 선택합니다.

이 구성은 클래식에서 업그레이드 한 후 새 IP 필터가 작동 하는 방식을 모방 합니다. 한 가지 예외는 기본 제공 endpoint protection 이지만 클래식 IP 필터를 사용해 볼 수 없습니다. 그러나이 기능은 선택 사항이 며, 어떤 항목이 손상 될 수 있다고 생각 되 면 사용할 필요가 없습니다.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>팁: IoT hub에 대 한 모든 IP 연결에 대 한 진단 로그를 확인 합니다.

원활한 전환을 위해 연결 범주에서 진단 로그를 확인 합니다. 속성을 찾아 `maskedIpAddress` 범위가 기대한 것인지 확인 합니다. 주의: 새 IP 필터는 명시적으로 추가 되지 않은 모든 IP 주소를 차단 합니다.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>클래식 IP 필터 설명서 IoT Hub (사용 되지 않음)

> [!IMPORTANT]
> 다음은 사용이 중지 되는 클래식 IP 필터에 대 한 원본 설명서입니다.

보안은 Azure IoT Hub를 기반으로 하는 모든 IoT 솔루션의 중요한 측면입니다. 경우에 따라 디바이스가 보안 구성의 일부로 연결할 수 있는 IP 주소를 명시적으로 지정해야 합니다. *IP 필터* 기능을 사용하면 특정 IPv4 주소에서 들어오는 트래픽을 거부하거나 수락하는 규칙을 구성할 수 있습니다.

### <a name="when-to-use"></a>사용 시기

특정 IP 주소에 대해 IoT Hub 엔드포인트를 차단하는 것이 유용한 두 가지 사용 사례가 있습니다.

* IoT Hub가 지정된 범위의 IP 주소에서 오는 트래픽만 수신하고 그 밖의 트래픽은 거부합니다. 예를 들어 IoT Hub를 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)와 사용하여 IoT Hub와 온-프레미스 인프라 간의 프라이빗 연결을 만드는 경우입니다.

* IoT Hub 관리자에 의해 의심스러운 것으로 식별된 IP 주소에서 오는 트래픽을 거부해야 합니다.

### <a name="how-filter-rules-are-applied"></a>필터 규칙이 적용되는 방식

IP 필터 규칙은 IoT Hub 서비스 수준에 적용됩니다. 따라서 IP 필터 규칙은 지원되는 모든 프로토콜을 사용하는 디바이스 및 백 엔드 앱의 모든 연결에 적용됩니다. 그러나 [기본 제공 Event Hub 호환 엔드포인트](iot-hub-devguide-messages-read-builtin.md)에서 직접(IoT Hub 연결 문자열을 통하지 않음) 읽는 클라이언트는 IP 필터 규칙에 바인딩되지 않습니다. 

IoT Hub의 거부 IP 규칙에 일치하는 IP 주소에서 오는 모든 연결 시도는 권한 없음 401 상태 코드 및 설명을 수신합니다. 응답 메시지는 IP 규칙을 언급하지 않습니다. IP 주소를 거부하면 다른 Azure 서비스(예: Azure Stream Analytics, Azure Virtual Machines 또는 Azure Portal의 Device Explorer)가 IoT 허브와 상호 작용하는 것을 막을 수 있습니다.

> [!NOTE]
> ASA(Azure Stream Analytics)를 사용하여 IP 필터링을 사용하도록 설정한 상태에서 IoT 허브의 메시지를 읽어야 하는 경우 이벤트 허브와 호환되는 IoT 허브 이름 및 엔드포인트를 사용하여 수동으로 ASA에 [Event Hubs 스트림 입력](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)을 추가합니다.

### <a name="default-setting"></a>기본 설정

기본적으로 IoT Hub에 대한 포털의 **IP 필터** 그리드는 비어있습니다. 이러한 기본 설정은 허브가 모든 IP 주소의 연결을 수락한다는 것을 의미합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다.

IP 필터 설정 페이지로 이동하려면 **네트워킹**, **공용 액세스** 를 차례로 선택한 다음, **선택한 IP 범위** 를 선택합니다.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="IoT Hub 기본 IP 필터 설정":::

### <a name="add-or-edit-an-ip-filter-rule"></a>IP 필터 규칙 추가 또는 편집

IP 필터 규칙을 추가하려면 **+ IP 필터 규칙 추가** 를 선택합니다.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="IP 필터 규칙을 IoT 허브에 추가":::

**IP 필터 규칙 추가** 를 선택한 후 필드를 입력합니다.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="IP 필터 규칙 추가를 선택한 후":::

* IP 필터 규칙의 **이름** 을 제공합니다. 이름은 최대 128자 길이의 대/소문자를 구분하는 고유한 영숫자 문자열이어야 합니다. ASCII 7 비트 영숫자 및 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`만 허용됩니다.

* 단일 IPv4 주소 또는 CIDR 표기법으로 IP 주소 블록을 제공합니다. 예를 들어 CIDR 표기법 192.168.100.0/22는 192.168.100.0에서 192.168.103.255까지 IPv4 주소 1024개를 나타냅니다.

* IP 필터 규칙에 대한 **작업** 으로 **허용** 또는 **차단** 을 선택합니다.

필드를 채운 후 **저장** 을 선택하여 규칙을 저장합니다. 업데이트가 진행 중임을 알리는 경고가 표시됩니다.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="IP 필터 규칙 저장에 대한 알림":::

최대 10개의 IP 필터 규칙에 도달하면 **추가** 옵션이 비활성화됩니다.

기존 규칙을 편집하려면 변경하려는 데이터를 선택하고 변경 내용을 적용한 다음, **저장** 을 선택하여 편집 내용을 저장합니다.

### <a name="delete-an-ip-filter-rule"></a>IP 필터 규칙 삭제

IP 필터 규칙을 삭제하려면 해당 행에서 휴지통 아이콘을 선택하고 **저장** 을 선택합니다. 규칙이 제거되고 변경 내용이 저장됩니다.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="IoT Hub IP 필터 규칙 삭제":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Azure CLI를 사용하여 IP 필터 검색 및 업데이트

IoT Hub의 IP 필터는 [Azure  CLI](/cli/azure/)를 통해 검색 및 업데이트할 수 있습니다.

IoT Hub의 현재 IP 필터를 검색하려면 다음을 실행합니다.

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

그러면 JSON 개체로 돌아가고 기존 IP 필터가 `properties.ipFilterRules` 키 아래에 나열됩니다.

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

IoT Hub에 대한 새 IP 필터를 추가하려면 다음을 실행합니다.

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

IoT Hub의 기존 IP 필터를 제거하려면 다음을 실행합니다.

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

`<ipFilterIndexToRemove>`는 IoT Hub의`properties.ipFilterRules`에서 IP 필터의 순서에 해당합니다.

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Azure PowerShell을 사용하여 IP 필터 검색 및 업데이트

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

IoT Hub의 IP 필터는 [Azure PowerShell](/powershell/azure/)을 통해 검색 및 설정할 수 있습니다.

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>REST를 사용하여 IP 필터 규칙 업데이트

또한 Azure 리소스 공급자의 REST 엔드포인트를 사용하여 IoT 허브의 IP 필터를 검색 및 수정할 수도 있습니다. [createorupdate 메서드](/rest/api/iothub/iothubresource/createorupdate)의 `properties.ipFilterRules`를 참조하세요.

### <a name="ip-filter-rule-evaluation"></a>IP 필터 규칙 평가

IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

예를 들어 192.168.100.0/22 범위의 주소를 수락하고 그 외의 주소는 거부하려는 경우 그리드에 있는 첫 번째 규칙이 주소 범위 192.168.100.0/22를 수락해야 합니다. 다음 규칙은 0.0.0.0/0 범위를 사용하여 모든 주소를 거부해야 합니다.

행의 시작 부분에 있는 세 개의 세로 점을 클릭하고 끌어서 놓기를 사용하여 그리드에서 IP 필터 규칙의 순서를 변경할 수 있습니다.

새 IP 필터 규칙 순서를 저장하려면 **저장** 을 클릭합니다.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="IoT Hub IP 필터 규칙의 순서 변경":::

## <a name="next-steps"></a>다음 단계

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IP 필터 사용](iot-hub-ip-filtering.md)