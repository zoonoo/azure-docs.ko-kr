---
title: Azure IoT Central 솔루션에서 장치 명령을 사용 하는 방법
description: Azure IoT Central 솔루션에서 장치 명령을 사용 하는 방법을 설명 합니다. 이 자습서에서는 장치 개발자가 클라이언트 앱에서 Azure IoT Central 응용 프로그램에 장치 명령을 사용 하는 방법을 보여 줍니다.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 52872175eb799785674c331ad4d687ff8ef427a4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134282"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Azure IoT Central 솔루션에서 명령을 사용 하는 방법

이 방법 가이드에서는 장치 개발자가 장치 템플릿에 정의 된 명령을 사용 하는 방법을 보여 줍니다.

운영자는 IoT Central UI를 사용 하 여 장치에서 명령을 호출할 수 있습니다. 명령은 장치의 동작을 제어 합니다. 예를 들어 운영자는 명령을 호출 하 여 장치를 다시 부팅 하거나 진단 데이터를 수집할 수 있습니다.

디바이스는 다음을 수행할 수 있습니다.

* 즉시 명령에 응답 합니다.
* 명령을 받을 때 IoT Central에 응답 한 다음 *장기 실행 명령이* 완료 되 면 나중에 IoT Central에 게 알립니다.

기본적으로 명령에는 장치가 연결 되 고 장치에 연결할 수 없는 경우 실패할 것으로 간주 됩니다. 장치 템플릿 UI에서 **오프 라인으로 큐** 대기 옵션을 선택 하는 경우 장치가 온라인 상태가 될 때까지 명령이 큐에 대기 될 수 있습니다. 이러한 *오프 라인 명령* 에 대해서는이 문서의 뒷부분에 별도의 섹션에서 설명 합니다.

## <a name="define-your-commands"></a>명령 정의

장치에 작업을 수행 하도록 지시 하는 표준 명령이 장치에 전송 됩니다. 명령에는 추가 정보를 포함 하는 매개 변수가 포함 될 수 있습니다. 예를 들어 장치에서 밸브를 여는 명령에는 밸브가 열리는 정도를 지정 하는 매개 변수가 있을 수 있습니다. 장치에서 명령을 완료 하는 경우에도 명령에서 반환 값을 받을 수 있습니다. 예를 들어 장치에서 일부 진단을 실행 하도록 요청 하는 명령은 진단 보고서를 반환 값으로 받을 수 있습니다.

명령은 장치 템플릿의 일부로 정의 됩니다. 다음 스크린샷은 **자동 온도 조절기** 장치 템플릿의 **Max-Min 보고서 가져오기** 명령 정의를 보여 줍니다. 이 명령에는 요청 및 응답 매개 변수가 모두 있습니다. 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="자동 온도 조절기 장치 템플릿에서 Max Min Report 명령을 표시 하는 스크린샷":::

다음 표에는 명령 기능의 구성 설정이 나와 있습니다.

| 필드             |설명|
|-------------------|-----------|
|표시 이름       |대시보드 및 폼에 사용 되는 명령 값입니다.|
| Name            | 명령 이름입니다. IoT Central은 표시 이름에서 이 필드의 값을 생성하지만, 필요한 경우 사용자 고유의 값을 선택할 수 있습니다. 이 필드는 영숫자여야 합니다. 장치 코드는이 **이름** 값을 사용 합니다.|
| 기능 유형 | 명령입니다.|
| 오프 라인 상태인 경우 큐 | 이 명령을 *오프 라인* 명령으로 만들지 여부를 지정 합니다. |
| 설명     | 명령 기능에 대한 설명입니다.|
| 의견     | 명령 기능에 대한 주석입니다.|
| 요청     | 장치 명령의 페이로드입니다.|
| 응답     | 장치 명령 응답의 페이로드입니다.|

다음 코드 조각은 장치 모델에 있는 명령의 JSON 표현을 보여 줍니다. 이 예제에서 응답 값은 여러 필드를 포함 하는 복잡 한 **개체** 유형입니다.

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> 장치 템플릿 페이지에서 장치 모델을 내보낼 수 있습니다.

다음 필드를 사용 하 여이 명령 정의를 UI의 스크린샷과 연결할 수 있습니다.

* `@type` 기능 유형을 지정 하려면 다음을 수행 합니다. `Command`
* `name` 명령 값에 대 한입니다.

표시 이름, 설명 등의 선택적 필드를 사용 하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

## <a name="standard-commands"></a>표준 명령

이 섹션에서는 장치에서 명령을 수신 하는 즉시 응답 값을 전송 하는 방법을 보여 줍니다.

다음 코드 조각은 장치에서 성공 코드를 즉시 전송 하는 명령에 응답 하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 편의를 위해 Node.js를 사용 합니다. 다른 언어 예제는 [Azure IoT Central 응용 프로그램에 클라이언트 응용 프로그램 만들기 및 연결](tutorial-connect-device.md) 자습서를 참조 하세요.

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

에 대 한 호출은 `onDeviceMethod` 메서드를 설정 합니다 `commandHandler` . 이 명령 처리기는 다음과 같습니다.

1. 명령의 이름을 확인 합니다.
1. 명령의 경우를 `getMaxMinReport` 호출 하 여 `getMaxMinReportObject` 반환 개체에 포함할 값을 검색 합니다.
1. 을 호출 하 여 `sendCommandResponse` 응답을 IoT Central으로 다시 보냅니다. 이 응답에는 `200` 성공 여부를 나타내는 응답 코드가 포함 됩니다.

다음 스크린샷은 IoT Central UI에서 성공적인 명령 응답을 표시 하는 방법을 보여 줍니다.

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="표준 명령에 대 한 명령 페이로드를 보는 방법을 보여 주는 스크린샷":::

## <a name="long-running-commands"></a>장기 실행 명령

이 섹션에서는 장치에서 명령이 완료 확인 전송을 지연 하는 방법을 보여 줍니다.

다음 코드 조각에서는 장치가 장기 실행 명령을 구현할 수 있는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 편의를 위해 Node.js를 사용 합니다. 다른 언어 예제는 [Azure IoT Central 응용 프로그램에 클라이언트 응용 프로그램 만들기 및 연결](tutorial-connect-device.md) 자습서를 참조 하세요.

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

에 대 한 호출은 `onDeviceMethod` 메서드를 설정 합니다 `commandHandler` . 이 명령 처리기는 다음과 같습니다.

1. 명령의 이름을 확인 합니다.
1. 을 호출 하 여 `sendCommandResponse` 응답을 IoT Central으로 다시 보냅니다. 이 응답은 `202` 보류 중인 결과를 나타내는 응답 코드를 포함 합니다.
1. 장기 실행 작업을 완료 합니다.
1. 명령과 이름이 같은 보고 된 속성을 사용 하 여 명령이 완료 되었음을 IoT Central 지시 합니다.

다음 스크린샷은 202 응답 코드를 받을 때 IoT Central UI에 명령 응답이 표시 되는 방법을 보여 줍니다.

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="장치의 즉각적인 응답을 보여 주는 스크린샷":::

다음 스크린샷은 명령이 완료 되었음을 나타내는 속성 업데이트를 받을 때 IoT Central UI를 보여 줍니다.

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="장기 실행 명령이 완료 되었음을 보여 주는 스크린샷":::

## <a name="offline-commands"></a>오프 라인 명령

이 섹션에서는 장치에서 오프 라인 명령을 처리 하는 방법을 보여 줍니다. 장치가 온라인 상태인 경우 수신 되는 즉시 오프 라인 명령을 처리할 수 있습니다. 장치가 오프 라인 상태인 경우 다음에 IoT Central 연결할 때 오프 라인 명령을 처리 합니다. 장치는 오프 라인 명령에 대 한 응답으로 반환 값을 보낼 수 없습니다.

> [!NOTE]
> 이 문서에서는 편의를 위해 Node.js를 사용 합니다.

다음 스크린샷은 **Generatediagnostics** 라는 오프 라인 명령을 보여 줍니다. 요청 매개 변수는 **StartTime** 이라는 Datetime 속성과 **Bank** 라는 정수 열거형 속성을 사용 하는 개체입니다.

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="오프 라인 명령의 UI를 보여 주는 스크린샷":::

다음 코드 조각에서는 클라이언트가 오프 라인 명령을 수신 대기 하 고 메시지 내용을 표시 하는 방법을 보여 줍니다.

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

이전 코드 조각의 출력에는 **StartTime** 및 **Bank** 값을 사용 하는 페이로드가 표시 됩니다. 속성 목록에는 **메서드 이름** 목록 항목의 명령 이름이 포함 됩니다.

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 명령을 사용 하는 방법을 배웠으므로 이제 [페이로드](concepts-telemetry-properties-commands.md) 를 참조 하 여 명령 매개 변수에 대 한 자세한 내용을 알아보고, [클라이언트 응용 프로그램을 만들어 Azure IoT Central 응용 프로그램에 연결](tutorial-connect-device.md) 하 여 여러 언어로 된 전체 코드 샘플을 확인 하세요.
