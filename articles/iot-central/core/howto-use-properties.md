---
title: Azure IoT Central 솔루션에서 속성을 사용 하는 방법
description: Azure IoT Central 솔루션에서 읽기 전용 및 쓰기 가능한 속성을 사용 하는 방법
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346967"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>Azure IoT Central 솔루션에서 속성을 사용 하는 방법

이 문서에서는 Azure IoT Central 응용 프로그램에서 장치 템플릿에 정의 된 장치 속성을 사용 하는 방법을 보여 줍니다.

속성은 특정 시간 값을 나타냅니다. 예를 들어 디바이스에서 속성을 사용하여 도달하려는 목표 온도를 보고할 수 있습니다. 속성을 사용 하 여 장치와 IoT Central 응용 프로그램 간의 상태를 동기화 할 수도 있습니다.  IoT Central에서 쓰기 가능한 속성을 설정할 수 있습니다.

IoT Central 응용 프로그램에서 클라우드 속성을 정의할 수도 있습니다. 클라우드 속성 값은 장치와 교환 되지 않으며이 문서의 범위를 벗어났습니다.

## <a name="define-your-properties"></a>속성 정의

속성은 장치의 상태를 나타내는 데이터 필드입니다. 속성을 사용 하 여 장치의 꺼짐 상태와 같은 장치의 내구성이 있는 상태를 나타냅니다. 속성은 장치의 소프트웨어 버전 같은 기본 장치 속성을 나타낼 수도 있습니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다.

다음 스크린샷은 Azure IoT Central 응용 프로그램의 속성 정의를 보여 줍니다.

![속성 정의](./media/howto-use-properties/property-definition.png)

다음 표에는 속성 기능의 구성 설정이 나와 있습니다.

| 필드           | Description                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 표시 이름    | 대시보드와 양식에 사용되는 속성 값의 표시 이름입니다.                                                                                                                                                              |
| Name            | 속성의 이름입니다. IoT Central은 표시 이름에서 이 필드의 값을 생성하지만, 필요한 경우 사용자 고유의 값을 선택할 수 있습니다. 이 필드는 영숫자여야 합니다.                                                 |
| 기능 유형 | 속성입니다.                                                                                                                                                                                                                          |
| 의미 체계 유형   | 속성의 의미 체계 유형입니다(예: 온도, 상태 또는 이벤트). 의미 체계 유형을 선택하면 다음 필드 중에서 사용 가능한 필드가 결정됩니다.                                                                       |
| 스키마          | 속성 데이터 형식입니다(예: double, string 또는 vector). 사용 가능한 선택 항목은 의미 체계 유형에 따라 결정됩니다. 이벤트 및 상태 의미 체계 유형에는 스키마를 사용할 수 없습니다.                                               |
| 쓰기 가능       | 쓰기 가능하지 않은 속성인 경우 디바이스에서 속성 값을 IoT Central에 보고할 수 있습니다. 쓰기 가능한 속성인 경우 디바이스에서 속성 값을 IoT Central에 보고할 수 있으며, IoT Central에서 속성 업데이트를 디바이스에 보낼 수 있습니다. |
| 심각도        | 이벤트 의미 체계 유형에만 사용할 수 있습니다. 심각도는 **오류**, **정보** 또는 **경고**입니다.                                                                                                                         |
| 상태 값    | 상태 의미 체계 유형에만 사용할 수 있습니다. 가능한 상태 값을 정의하며, 각각에는 표시 이름, 이름, 열거형 형식 및 값이 있습니다.                                                                                   |
| 단위            | **입니다**, 또는 C와 같은 속성 값의 단위 **%** 입니다. ** &deg; **                                                                                                                                                              |
| 표시 단위    | 대시보드와 양식에서 사용할 표시 단위입니다.                                                                                                                                                                                    |
| 주석         | 속성 기능에 대한 주석입니다.                                                                                                                                                                                        |
| Description     | 속성 기능에 대한 설명입니다.                                                                                                                                                                                          |

속성은 아래와 같이 장치 템플릿의 인터페이스에서 정의할 수도 있습니다.

``` json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

이 예제에서는 다음과 같이 5 개의 속성을 보여 줍니다. 이러한 속성은 UI의 속성 정의와 관련이 있을 수 있습니다.

* `@type` 기능 유형을 지정 하려면 다음을 수행 합니다. `Property`
* `name` 속성 값에 대 한입니다.
* `schema` 속성의 데이터 형식을 지정 합니다. 이 값은 double, integer, boolean 또는 string과 같은 기본 형식일 수 있습니다. 복합 개체 형식, 배열 및 맵도 지원 됩니다.
* `writable` 기본적으로 속성은 읽기 전용입니다. 이 필드를 사용 하 여 속성을 쓰기 가능으로 표시할 수 있습니다.

표시 이름, 설명 등의 선택적 필드를 사용 하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

속성을 만들 때 개체, 열거형 등의 복잡 한 **스키마** 형식을 지정할 수 있습니다.

![기능 추가](./media/howto-use-properties/property.png)

**개체**등의 복잡 한 스키마를 선택 하는 경우 개체도 정의 해야 합니다.

![개체 정의](./media/howto-use-properties/object.png)

다음 코드에서는 개체 속성 형식의 정의를 보여 줍니다. 이 개체에는 문자열과 정수 형식의 두 필드가 있습니다.

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>읽기 전용 속성 구현

기본적으로 속성은 읽기 전용입니다. 읽기 전용 속성은 장치가 IoT Central 응용 프로그램에 대 한 속성 값 업데이트를 보고 하는 것을 의미 합니다. IoT Central 응용 프로그램에서 읽기 전용 속성 값을 설정할 수 없습니다.

IoT Central은 디바이스 쌍을 사용하여 디바이스와 IoT Central 애플리케이션 간에 속성 값을 동기화합니다. 디바이스 속성 값은 디바이스 쌍 reported 속성을 사용합니다. 자세한 내용은 [장치](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins) 쌍을 참조 하세요.

장치 기능 모델의 다음 코드 조각은 읽기 전용 속성 유형에 대 한 정의를 보여 줍니다.

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

장치에서 IoT Central 읽기 전용 속성을 보냅니다. 속성은 JSON 페이로드로 전송 됩니다. 자세한 내용은 [페이로드](./concepts-telemetry-properties-commands.md)를 참조 하십시오.

Azure IoT 장치 SDK를 사용 하 여 IoT Central 응용 프로그램에 속성 업데이트를 보낼 수 있습니다.

아래 함수를 사용 하 여 장치 쌍 속성을 Azure IoT Central 응용 프로그램으로 보낼 수 있습니다.

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

이 문서에서는 간소화를 위해 Node.js를 사용 합니다. 장치 응용 프로그램 예제에 대 한 전체 정보를 보려면 [azure IoT Central 응용 프로그램에 클라이언트 응용 프로그램 만들기 및 연결 (Node.js)](tutorial-connect-device-nodejs.md) 을 참조 하 고, [클라이언트 응용 프로그램을 만들어 Python (azure IoT Central 응용 프로그램) 자습서에 연결](tutorial-connect-device-python.md) 합니다.

Azure IoT Central 응용 프로그램의 다음 보기는 속성을 보여 줍니다. 보기에서 자동으로 장치 모델 속성을 _읽기 전용 장치 속성_으로 만드는 것을 볼 수 있습니다.

![읽기 전용 속성 보기](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>쓰기 가능한 속성 구현

쓰기 가능한 속성은 양식에서 IoT Central 응용 프로그램의 연산자에 의해 설정 됩니다. IoT Central는 속성을 장치에 보냅니다. IoT Central 장치에서 승인이 필요 합니다. 

장치 기능 모델의 다음 코드 조각은 쓰기 가능한 속성 형식의 정의를 보여 줍니다.

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

``` json
{ "Brightness Level": 2 }
```

장치에서 응답 하는 쓰기 가능한 속성을 정의 하 고 처리 하기 위해 다음 코드를 사용할 수 있습니다.

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

응답 메시지에는 및 필드가 포함 되어야 합니다 `ac` `av` . `ad` 필드는 선택 사항입니다. 예제는 다음 코드 조각을 참조 하세요.

* `ac` 는 다음 표의 값을 사용 하는 숫자 필드입니다.

* `av` 장치에 전송 된 버전 번호입니다.

* `ad` 는 옵션 문자열 설명입니다.

| 값 | 레이블 | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | 속성 변경 작업을 성공적으로 완료 했습니다. |
| `'ac': 202`  디스크나 `'ac': 201` | Pending | 속성 변경 작업이 보류 중이거나 진행 중입니다. |
| `'ac': 4xx` | Error | 요청 된 속성 변경이 잘못 되었거나 오류가 발생 했습니다. |
| `'ac': 5xx` | Error | 장치에서 요청 된 변경을 처리 하는 동안 예기치 않은 오류가 발생 했습니다. |


자세한 내용은 [장치](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)쌍을 참조 하세요.

운영자가 IoT Central 애플리케이션에서 쓰기 가능 속성을 설정하면 애플리케이션에서는 디바이스 쌍 desired 속성을 사용하여 디바이스에 값을 보냅니다. 그러면 디바이스는 디바이스 쌍 reported 속성속성을 사용하여 응답합니다. IoT Central 보고 된 속성 값을 받으면 **수락 됨**상태를 사용 하 여 속성 뷰를 업데이트 합니다.

다음 뷰는 쓰기 가능한 속성을 보여 줍니다. 값을 입력 하 고 **저장할**때 초기 상태는 **보류 중**이 고, 장치에서 변경 내용을 수락 하면 상태가 **수락 됨**으로 변경 됩니다.

![보류 상태](./media/howto-use-properties/status-pending.png)

![수락 된 속성](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 속성을 사용 하는 방법을 배웠으므로 이제 [페이로드](concepts-telemetry-properties-commands.md) 를 보고 [클라이언트 응용 프로그램을 만들어 azure IoT Central 응용 프로그램 (Node.js)에 연결할](tutorial-connect-device-nodejs.md)수 있습니다.
