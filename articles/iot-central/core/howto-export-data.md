---
title: Azure IoT Central에서 데이터 내보내기 (미리 보기) | Microsoft Docs
description: 새 데이터 내보내기를 사용 하 여 Azure 및 사용자 지정 클라우드 대상으로 IoT 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/02/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: contperfq1
ms.openlocfilehash: 0a07d7e57ced5e2cd9457dc51ebcd355306fc48e
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461938"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>데이터 내보내기를 사용 하 여 IoT 데이터를 클라우드 대상으로 내보내기 (미리 보기)

> [!Note]
> 이 문서에서는 IoT Central의 미리 보기 데이터 내보내기 기능을 설명 합니다.
>
> - 레거시 데이터 내보내기 기능에 대 한 자세한 내용은 [데이터 내보내기 (레거시)를 사용 하 여 클라우드 대상으로 IoT 데이터 내보내기](./howto-export-data-legacy.md)를 참조 하세요.
> - 미리 보기 데이터 내보내기 및 레거시 데이터 내보내기 기능 간의 차이점에 대 한 자세한 내용은 아래의 [비교 표](#comparison-of-legacy-data-export-and-preview-data-export) 를 참조 하세요.

이 문서에서는 Azure IoT Central에서 새로운 데이터 내보내기 미리 보기 기능을 사용 하는 방법을 설명 합니다. 이 기능을 사용 하 여 IoT Central 응용 프로그램에서 필터링 및 보강 IoT 데이터를 지속적으로 내보낼 수 있습니다. 데이터 내보내기는 웜 경로 통찰력, 분석 및 저장소에 대 한 클라우드 솔루션의 다른 부분에 거의 실시간으로 변경 내용을 푸시합니다.

예를 들어, 다음을 수행할 수 있습니다.

- JSON 형식의 원격 분석 데이터 및 속성 변경을 거의 실시간으로 계속 내보냅니다.
- 데이터 스트림을 필터링 하 여 사용자 지정 조건과 일치 하는 데이터를 내보냅니다.
- 장치에서 사용자 지정 값 및 속성 값을 사용 하 여 데이터 스트림을 보강 합니다.
- Azure Event Hubs, Azure Service Bus, Azure Blob Storage, webhook 끝점 등의 대상으로 데이터를 보냅니다.

> [!Tip]
> 데이터 내보내기를 켜면 해당 순간부터의 데이터만 가져옵니다. 현재 데이터 내보내기가 해제 된 시간에 대 한 데이터를 검색할 수 없습니다. 기록 데이터를 더 보존 하려면 초기에 데이터 내보내기를 켭니다.

## <a name="prerequisites"></a>전제 조건

데이터 내보내기 미리 보기 기능을 사용 하려면 [V3 응용 프로그램이](howto-get-app-info.md)있어야 하며 [데이터 내보내기](howto-manage-users-roles.md) 권한이 있어야 합니다.

## <a name="set-up-export-destination"></a>내보내기 대상 설정

데이터 내보내기를 구성 하기 전에 내보내기 대상이 있어야 합니다. 현재 사용할 수 있는 대상 유형은 다음과 같습니다.

- Azure Event Hubs
- Azure Service Bus 큐
- Azure Service Bus 항목
- Azure Blob Storage
- 웹후크

### <a name="create-an-event-hubs-destination"></a>Event Hubs 대상 만들기

내보낼 기존 Event Hubs 네임 스페이스가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에서 새 Event Hubs 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.EventHub)를 만듭니다. [Azure Event Hubs 문서](../../event-hubs/event-hubs-create.md)에서 자세히 알아볼 수 있습니다.

1. Event Hubs 네임스페이스에서 이벤트 허브를 만듭니다. 네임스페이스로 이동한 다음, 맨 위에서 **+ 이벤트 허브**를 선택하여 이벤트 허브 인스턴스를 만듭니다.

1. IoT Central에서 데이터 내보내기를 설정 하는 데 사용할 키를 생성 합니다.

    - 만든 이벤트 허브 인스턴스를 선택 합니다.
    - **설정 > 공유 액세스 정책**을 선택 합니다.
    - 새 키를 만들거나 **송신** 권한이 있는 기존 키를 선택 합니다.
    - 기본 또는 보조 연결 문자열을 복사합니다. 이 연결 문자열을 사용 하 여 IoT Central에서 새 대상을 설정 합니다.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Service Bus 큐 또는 토픽 대상 만들기

내보낼 기존 Service Bus 네임 스페이스가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에 새 Service Bus 네임 스페이스](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)를 만듭니다. [Azure Service Bus 문서](../../service-bus-messaging/service-bus-create-namespace-portal.md)에서 자세히 알아볼 수 있습니다.

1. 로 내보낼 큐 또는 토픽을 만들려면 Service Bus 네임 스페이스로 이동 하 고 **+ queue** 또는 **+ 토픽**을 선택 합니다.

1. IoT Central에서 데이터 내보내기를 설정 하는 데 사용할 키를 생성 합니다.

    - 만든 큐 또는 토픽을 선택 합니다.
    - **설정/공유 액세스 정책**을 선택 합니다.
    - 새 키를 만들거나 **송신** 권한이 있는 기존 키를 선택 합니다.
    - 기본 또는 보조 연결 문자열을 복사합니다. 이 연결 문자열을 사용 하 여 IoT Central에서 새 대상을 설정 합니다.

### <a name="create-an-azure-blob-storage-destination"></a>Azure Blob Storage 대상 만들기

로 내보낼 기존 Azure storage 계정이 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. 새 [Azure Blob storage 계정](https://aka.ms/blobdocscreatestorageaccount) 또는 [Azure Data Lake Storage v2 저장소 계정](../../storage/blobs/data-lake-storage-quickstart-create-account.md)만들기에 대해 자세히 알아볼 수 있습니다. 데이터 내보내기는 블록 blob을 지 원하는 저장소 계정에만 데이터를 쓸 수 있습니다. 다음 목록에는 알려진 호환 저장소 계정 유형이 나와 있습니다.

    |성능 계층|계정 유형|
    |-|-|
    |Standard|범용 V2|
    |Standard|범용 V1|
    |Standard|Blob Storage|
    |Premium|블록 Blob 저장소|

1. 저장소 계정에서 컨테이너를 만들려면 저장소 계정으로 이동 합니다. **Blob 서비스**에서 **Blob 찾아보기**를 선택합니다. 맨 위에서 **+ 컨테이너**를 선택하여 새 컨테이너를 만듭니다.

1. **설정 > 액세스 키**로 이동 하 여 저장소 계정에 대 한 연결 문자열을 생성 합니다. 두 연결 문자열 중 하나를 복사 합니다.

### <a name="create-a-webhook-endpoint"></a>Webhook 끝점 만들기

공개적으로 사용 가능한 HTTP webhook 끝점으로 데이터를 내보낼 수 있습니다. [Requestbin](https://requestbin.net/)을 사용 하 여 테스트 webhook 끝점을 만들 수 있습니다. 요청 제한에 도달한 경우 RequestBin 제한 request:

1. [RequestBin](https://requestbin.net/)을 엽니다.
2. 새 RequestBin을 만들고 **Bin URL**을 복사합니다. 데이터 내보내기를 테스트할 때이 URL을 사용 합니다.

## <a name="set-up-data-export"></a>데이터 내보내기 설정

이제 데이터를 내보낼 대상이 있으므로 IoT Central 응용 프로그램에서 데이터 내보내기를 설정 합니다.

1. IoT Central 애플리케이션에 로그인합니다.

1. 왼쪽 창에서 **데이터 내보내기 (미리 보기)** 를 선택 합니다.

    > [!Tip]
    > 왼쪽 창에 **데이터 내보내기 (미리 보기)** 가 표시 되지 않으면 앱에서 데이터 내보내기를 구성할 수 있는 권한이 없습니다. 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

1. **+ 새로 만들기 내보내기**를 선택 합니다.

1. 새 내보내기에 대 한 표시 이름을 입력 하 고 데이터 내보내기가 **활성화**되어 있는지 확인 합니다.

1. 내보낼 데이터 형식을 선택 합니다. 다음 표에서는 지원 되는 데이터 내보내기 유형을 나열 합니다.

    | 데이터 형식 | Description | 데이터 형식 |
    | :------------- | :---------- | :----------- |
    |  원격 분석 | 거의 실시간으로 장치에서 원격 분석 메시지를 내보냅니다. 내보낸 각 메시지에는 정규화 된 원본 장치 메시지의 전체 내용이 포함 되어 있습니다.   |  [원격 분석 메시지 형식](#telemetry-format)   |
    | 속성 변경 | 거의 실시간으로 장치 및 클라우드 속성에 대 한 변경 내용을 내보냅니다. 읽기 전용 장치 속성의 경우 보고 된 값에 대 한 변경 내용을 내보냅니다. 읽기/쓰기 속성의 경우 보고 된 값과 원하는 값을 모두 내보냅니다. | [속성 변경 메시지 형식](#property-changes-format) |

1. 필요에 따라 필터를 추가 하 여 내보내는 데이터의 양을 줄입니다. 각 데이터 내보내기 유형에는 다음과 같은 다양 한 유형의 필터를 사용할 수 있습니다.

    원격 분석을 필터링 하려면 다음을 사용 합니다.

    - **기능 필터**: **이름** 드롭다운에서 원격 분석 항목을 선택 하면 내보낸 스트림에는 필터 조건을 충족 하는 원격 분석만 포함 됩니다. **이름** 드롭다운에서 장치 또는 클라우드 속성 항목을 선택 하는 경우 내보낸 스트림에는 필터 조건과 일치 하는 속성이 있는 장치의 원격 분석만 포함 됩니다.
    - **메시지 속성 필터**: 장치 sdk를 사용 하는 장치는 각 원격 분석 메시지에서 *메시지 속성* 또는 *응용 프로그램 속성* 을 보낼 수 있습니다. 속성은 사용자 지정 식별자를 사용 하 여 메시지에 태그를 지정 하는 키-값 쌍의 모음입니다. 메시지 속성 필터를 만들려면 찾으려는 메시지 속성 키를 입력 하 고 조건을 지정 합니다. 지정 된 필터 조건과 일치 하는 속성이 있는 원격 분석 메시지만 내보내집니다. 다음 문자열 비교 연산자가 지원 됩니다. equals, 같지 않음, 포함, 포함 안 함, 존재 안 함, 존재 하지 않음 [IoT Hub 문서에서 응용 프로그램 속성에 대해 자세히 알아보세요](../../iot-hub/iot-hub-devguide-messages-construct.md).

    속성 변경 내용을 필터링 하려면 **기능 필터**를 사용 합니다. 드롭다운에서 속성 항목을 선택 합니다. 내보낸 스트림에는 필터 조건을 충족 하는 선택한 속성의 변경 내용만 포함 됩니다.

1. 필요에 따라 추가 키-값 쌍 메타 데이터를 사용 하 여 내보낸 메시지를 보강 합니다. 다음 강화은 원격 분석 및 속성 변경 데이터 내보내기 유형에 사용할 수 있습니다.

    - **사용자 지정 문자열**: 각 메시지에 사용자 지정 정적 문자열을 추가 합니다. 아무 키나 입력 하 고 임의의 문자열 값을 입력 합니다.
    - **속성**: 현재 장치에서 보고 한 속성 또는 클라우드 속성 값을 각 메시지에 추가 합니다. 아무 키나 입력 하 고 장치 또는 클라우드 속성을 선택 합니다. 내보낸 메시지가 지정 된 속성이 없는 장치에서 가져온 경우 내보낸 메시지는 보강을 받지 않습니다.

1. 새 대상을 추가 하거나 이미 만든 대상을 추가 합니다. **새 항목 만들기** 링크를 선택 하 고 다음 정보를 추가 합니다.

    - **대상 이름**: IoT Central의 대상 표시 이름입니다.
    - **대상 유형**: 대상 유형을 선택 합니다. 대상을 아직 설정 하지 않은 경우 [내보내기 대상 설정](#set-up-export-destination)을 참조 하세요.
    - Azure Event Hubs Azure Service Bus 큐 또는 토픽에서 리소스에 대 한 연결 문자열을 붙여 넣습니다.
    - Azure Blob Storage의 경우 리소스에 대 한 연결 문자열을 붙여넣고 대/소문자를 구분 하는 컨테이너 이름을 입력 합니다.
    - Webhook에 대해 웹 후크 끝점에 대 한 콜백 URL을 붙여넣습니다.
    - **만들기**를 선택합니다.

1. **+ 대상** 을 선택 하 고 드롭다운에서 대상을 선택 합니다. 단일 내보내기에 최대 5 개의 대상을 추가할 수 있습니다.

1. 내보내기 설정을 완료 한 후 **저장**을 선택 합니다. 몇 분 후에 데이터가 대상에 표시 됩니다.

## <a name="export-contents-and-format"></a>콘텐츠 내보내기 및 형식

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage 대상

데이터는 분당 한 번 내보내집니다. 각 파일은 이전 내보내기 이후의 변경 내용 일괄 처리를 포함 합니다. 내보낸 데이터는 JSON 형식으로 저장 됩니다. 저장소 계정에서 내보낸 데이터에 대 한 기본 경로는 다음과 같습니다.

- 원격 분석: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 속성 변경: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Azure Portal에서 내보낸 파일을 찾아보려면 해당 파일로 이동 하 여 **Blob 편집**을 선택 합니다.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs 및 Azure Service Bus 대상

데이터는 거의 실시간으로 내보내집니다. 데이터는 메시지 본문에 있고 JSON 형식으로 u t f-8로 인코딩됩니다.

메시지의 주석 또는 시스템 속성 모음에는 `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` `iotcentral-message-type` 메시지 본문의 해당 필드와 값이 동일한,, 및 필드가 포함 됩니다.

### <a name="webhook-destination"></a>Webhook 대상

웹 후크 대상의 경우 데이터를 거의 실시간으로 내보낼 수 있습니다. 메시지 본문의 데이터는 Event Hubs 및 Service Bus와 동일한 형식입니다.

### <a name="telemetry-format"></a>원격 분석 형식

내보낸 각 메시지에는 장치에서 메시지 본문으로 보낸 전체 메시지의 정규화 된 형식이 포함 되어 있습니다. 메시지는 JSON 형식이 며 u t f-8로 인코딩됩니다. 각 메시지에 포함 되는 정보는 다음과 같습니다.

- `applicationId`: IoT Central 응용 프로그램의 ID입니다.
- `messageSource`: 메시지의 소스 `telemetry` 입니다.
- `deviceId`: 원격 분석 메시지를 보낸 장치의 ID입니다.
- `schema`: 페이로드 스키마의 이름 및 버전입니다.
- `templateId`: 장치와 연결 된 장치 템플릿의 ID입니다.
- `enrichments`: 내보내기에 설정 된 모든 강화.
- `messageProperties`: 장치에서 메시지와 함께 보낸 추가 속성입니다. 이러한 속성을 *응용 프로그램 속성*이 라고도 합니다. [IoT Hub 문서에서 자세히 알아보세요](../../iot-hub/iot-hub-devguide-messages-construct.md).

Event Hubs 및 Service Bus의 경우 장치에서 메시지를 받은 후 새 메시지를 빠르게 내보냅니다 IoT Central.

Blob storage의 경우 메시지는 일괄 처리 되 고 분당 한 번 내보냅니다.

다음 예에서는 내보낸 원격 분석 메시지를 보여 줍니다.

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>속성 변경 형식

각 메시지 또는 레코드는 장치 또는 클라우드 속성에 대 한 변경 내용을 나타냅니다. 장치 속성의 경우 보고 된 값의 변경 내용만 별도 메시지로 내보내집니다. 내보낸 메시지의 정보는 다음과 같습니다.

- `applicationId`: IoT Central 응용 프로그램의 ID입니다.
- `messageSource`: 메시지의 소스 `properties` 입니다.
- `messageType`: `cloudPropertyChange` , 또는 중 하나 `devicePropertyDesiredChange` `devicePropertyReportedChange` 입니다.
- `deviceId`: 원격 분석 메시지를 보낸 장치의 ID입니다.
- `schema`: 페이로드 스키마의 이름 및 버전입니다.
- `templateId`: 장치와 연결 된 장치 템플릿의 ID입니다.
- `enrichments`: 내보내기에 설정 된 모든 강화.

Event Hubs 및 Service Bus의 경우 IoT Central 새 메시지 데이터를 이벤트 허브 또는 Service Bus 큐 또는 토픽에 거의 실시간으로 내보냅니다.

Blob storage의 경우 메시지는 일괄 처리 되 고 분당 한 번 내보냅니다.

다음 예제에서는 Azure Blob Storage에서 받은 내보낸 속성 변경 메시지를 보여 줍니다.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-preview-data-export"></a>레거시 데이터 내보내기 및 미리 보기 데이터 내보내기 비교

다음 표에서는 [레거시 데이터 내보내기](howto-export-data-legacy.md) 및 미리 보기 데이터 내보내기 기능 간의 차이점을 보여 줍니다.

| 기능  | 레거시 데이터 내보내기 | 새 데이터 내보내기 |
| :------------- | :---------- | :----------- |
| 사용 가능한 데이터 형식 | 원격 분석, 장치, 장치 템플릿 | 원격 분석, 속성 변경 |
| Filtering | 없음 | 내보내는 데이터 형식에 따라 달라 집니다. 원격 분석, 원격 분석을 통한 필터링, 메시지 속성, 속성 값 |
| 강화 | 없음 | 사용자 지정 문자열 또는 장치에서 속성 값을 사용 하 여 보강 |
| Destinations | Azure Event Hubs, Azure Service Bus 큐 및 토픽 Azure Blob Storage | 레거시 데이터 내보내기 및 webhook와 동일 합니다.|
| 지원되는 애플리케이션 버전 | V2, V3 | V3만 |
| 주목할 만한 제한 | 앱 당 내보내기 5 개, 내보내기 당 하나의 대상 | 10 개의 내보내기-앱 당 대상 연결 |

## <a name="next-steps"></a>다음 단계

이제 새 데이터 내보내기를 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Central에서 분석을 사용 하는 방법을](./howto-create-analytics.md) 배우는 것입니다.
