---
title: Azure IoT Central의 장치 템플릿 이란? Microsoft Docs
description: Azure IoT Central 장치 템플릿을 사용 하면 응용 프로그램에 연결 된 장치의 동작을 지정할 수 있습니다. 장치 템플릿은 장치에서 구현 해야 하는 원격 분석, 속성 및 명령을 지정 합니다. 또한 장치 템플릿은 운영자가 사용 하는 양식 및 대시보드와 같은 IoT Central에서 장치에 대 한 UI를 정의 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: e82a377d62184c8ae1d2e8f076b228e36005887a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992678"
---
# <a name="what-are-device-templates"></a>디바이스 템플릿이란?

_이 문서는 장치 개발자 및 솔루션 빌더에 적용 됩니다._

Azure IoT Central의 장치 템플릿은 응용 프로그램에 연결 하는 장치 유형에 대 한 특성 및 동작을 정의 하는 청사진입니다. 예를 들어 장치 템플릿은 IoT Central 올바른 단위 및 데이터 형식을 사용 하는 시각화를 만들 수 있도록 장치에서 보내는 원격 분석을 정의 합니다.

솔루션 빌더는 IoT Central 응용 프로그램에 장치 템플릿을 추가 합니다. 장치 개발자는 장치 템플릿에 정의 된 동작을 구현 하는 장치 코드를 작성 합니다.

장치 템플릿에는 다음 섹션이 포함 됩니다.

- _장치 모델_ 입니다. 장치 템플릿의이 부분은 장치가 응용 프로그램과 상호 작용 하는 방법을 정의 합니다. 장치 개발자는 모델에 정의 된 동작을 구현 합니다.
    - _기본 구성 요소_ 입니다. 모든 장치 모델에는 기본 구성 요소가 있습니다. 기본 구성 요소 인터페이스는 장치 모델과 관련 된 기능을 설명 합니다.
    - _구성 요소_. 장치 모델에는 장치 기능을 설명 하는 기본 구성 요소 외에도 구성 요소가 포함 될 수 있습니다. 각 구성 요소는 구성 요소의 기능을 설명 하는 인터페이스를 포함 합니다. 구성 요소 인터페이스는 다른 장치 모델에서 재사용할 수 있습니다. 예를 들어 여러 전화 장치 모델에서 동일한 카메라 인터페이스를 사용할 수 있습니다.
    - _상속 된 인터페이스_. 장치 모델은 기본 구성 요소의 기능을 확장 하는 하나 이상의 인터페이스를 포함 합니다.
- _클라우드 속성_. 이 장치 템플릿 파트를 통해 솔루션 개발자는 저장할 장치 메타 데이터를 지정할 수 있습니다. 클라우드 속성은 장치와 동기화 되지 않으며 응용 프로그램에만 존재 합니다. 클라우드 속성은 장치 개발자가 장치 모델을 구현 하기 위해 작성 하는 코드에 영향을 주지 않습니다.
- _사용자 지정_. 이 장치 템플릿 파트를 사용 하면 솔루션 개발자가 장치 모델의 일부 정의를 재정의할 수 있습니다. 사용자 지정은 솔루션 개발자가 속성의 표시 이름 변경 또는 원격 분석 값을 표시 하는 데 사용 된 색과 같은 응용 프로그램에서 값을 처리 하는 방법을 조정 하려는 경우에 유용 합니다. 사용자 지정은 장치 개발자가 장치 모델을 구현 하기 위해 작성 하는 코드에 영향을 주지 않습니다.
- _뷰입니다_. 이 장치 템플릿 파트를 통해 솔루션 개발자는 장치에서 데이터를 보는 시각화를 정의 하 고 장치를 관리 하 고 제어할 수 있습니다. 이 보기에는 장치 모델, 클라우드 속성 및 사용자 지정이 사용 됩니다. 보기는 장치 개발자가 장치 모델을 구현 하기 위해 작성 하는 코드에 영향을 주지 않습니다.

## <a name="device-models"></a>디바이스 모델

장치 모델은 장치가 IoT Central 응용 프로그램과 상호 작용 하는 방식을 정의 합니다. 장치 개발자는 장치가 장치를 모니터링 하 고 관리할 수 IoT Central 있도록 장치 모델에 정의 된 동작을 구현 하는지 확인 해야 합니다. 장치 모델은 하나 이상의 _인터페이스로_ 구성 되며 각 인터페이스는 _원격 분석_ 유형, _장치 속성_ 및 _명령의_ 컬렉션을 정의할 수 있습니다. 솔루션 개발자는 장치 모델을 정의 하는 JSON 파일을 장치 템플릿으로 가져오거나 IoT Central의 웹 UI를 사용 하 여 장치 모델을 만들거나 편집할 수 있습니다. 웹 UI를 사용 하 여 만든 장치 모델을 변경 하려면 [장치 템플릿 버전을](./howto-version-device-template.md)지정 해야 합니다.

솔루션 개발자는 장치 모델을 포함 하는 JSON 파일을 내보낼 수도 있습니다. 장치 개발자는이 JSON 문서를 사용 하 여 장치가 IoT Central 응용 프로그램과 통신 하는 방법을 이해할 수 있습니다.

장치 모델을 정의 하는 JSON 파일은 [DTDL (디지털 쌍 정의 언어) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)를 사용 합니다. JSON 파일에는 별도의 파일이 아닌 인라인으로 정의 된 인터페이스를 포함 하는 장치 모델을 포함 하는 IoT Central 필요 합니다.

일반적인 IoT 장치는 다음과 같이 구성 됩니다.

- 사용자 지정 파트-장치를 고유 하 게 만드는 항목입니다.
- 모든 장치에 공통적인 작업 인 표준 부분

이러한 부분은 장치 모델에서 _인터페이스_ 라고 합니다. 인터페이스는 장치가 구현 하는 각 파트에 대 한 세부 정보를 정의 합니다. 인터페이스는 장치 모델에서 재사용할 수 있습니다. DTDL에서 구성 요소는 별도의 DTDL 파일에 정의 된 인터페이스를 참조 합니다.

다음 예제에서는 온도 컨트롤러 장치에 대 한 장치 모델의 개요를 보여 줍니다. 기본 구성 요소에는, 및에 대 한 정의가 포함 되어 `workingSet` `serialNumber` `reboot` 있습니다. 장치 모델에는 및 인터페이스도 포함 됩니다 `thermostat` `deviceInformation` .

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

인터페이스에는 다음과 같은 몇 가지 필수 필드가 있습니다.

- `@id`: 단순한 단일 리소스 이름 형식의 고유 ID입니다.
- `@type`:이 개체가 인터페이스 임을 선언 합니다.
- `@context`: 인터페이스에 사용 되는 DTDL 버전을 지정 합니다.
- `contents`: 장치를 구성 하는 속성, 원격 분석 및 명령을 나열 합니다. 이 기능은 여러 인터페이스에서 정의 될 수 있습니다.

표시 이름 및 설명과 같은 추가 정보를 기능 모델에 추가 하는 데 사용할 수 있는 몇 가지 선택적 필드가 있습니다.

Implements 섹션에서 인터페이스 목록의 각 항목에는 다음이 포함 됩니다.

- `name`: 인터페이스의 프로그래밍 이름입니다.
- `schema`: 기능 모델이 구현 하는 인터페이스입니다.

## <a name="interfaces"></a>인터페이스

DTDL을 사용 하면 장치의 기능을 설명할 수 있습니다. 관련 기능은 인터페이스로 그룹화 됩니다. 인터페이스는 장치에서 구현 하는 속성, 원격 분석 및 명령을 설명 합니다.

- `Properties`. 속성은 장치의 상태를 나타내는 데이터 필드입니다. 속성을 사용 하 여 냉각수 펌프의 꺼짐 상태와 같은 장치의 내구성이 있는 상태를 나타냅니다. 속성은 장치의 펌웨어 버전 같은 기본 장치 속성을 나타낼 수도 있습니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 장치만 읽기 전용 속성의 값을 업데이트할 수 있습니다. 운영자는 장치에 보낼 쓰기 가능 속성의 값을 설정할 수 있습니다.
- `Telemetry`. 원격 분석 필드는 센서의 측정값을 나타냅니다. 장치가 센서를 사용 하는 경우에는 센서 데이터를 포함 하는 원격 분석 이벤트를 전송 해야 합니다.
- `Commands`. 명령은 장치의 사용자가 장치에서 실행할 수 있는 메서드를 나타냅니다. 예를 들면 reset 명령 또는 팬을 전환 하는 명령입니다.

다음 예제에서는 자동 온도 조절기 인터페이스 정의를 보여 줍니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
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
  ]
}
```

이 예제에서는 두 개의 속성 (읽기 전용 및 쓰기 가능한 하나), 원격 분석 유형 및 명령을 보여 줍니다. 최소 필드 설명에는 다음이 포함 됩니다.

- `@type` 기능 유형을 지정 하려면, `Telemetry` `Property` 또는 `Command` 입니다.  일부 경우에는 IoT Central에서 값을 처리 하는 방법에 대 한 몇 가지 가정을 수행할 수 있도록 의미 체계 형식이 포함 되어 있습니다.
- `name` 원격 분석 값의 경우
- `schema` 원격 분석 또는 속성의 데이터 형식을 지정 합니다. 이 값은 double, integer, boolean 또는 string과 같은 기본 형식일 수 있습니다. 복합 개체 형식, 배열 및 맵도 지원 됩니다.

표시 이름, 설명 등의 선택적 필드를 사용 하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

## <a name="properties"></a>속성

기본적으로 속성은 읽기 전용입니다. 읽기 전용 속성은 장치가 IoT Central 응용 프로그램에 대 한 속성 값 업데이트를 보고 하는 것을 의미 합니다. IoT Central 응용 프로그램에서 읽기 전용 속성 값을 설정할 수 없습니다.

인터페이스에서 속성을 쓰기 가능으로 표시할 수도 있습니다. 장치는 IoT Central 응용 프로그램에서 쓰기 가능한 속성에 대 한 업데이트를 수신 하 고, 응용 프로그램에 대 한 속성 값 업데이트를 보고할 수 있습니다.

장치를 연결 하 여 속성 값을 설정할 필요가 없습니다. 업데이트 된 값은 다음에 장치를 응용 프로그램에 연결할 때 전송 됩니다. 이 동작은 읽기 전용 및 쓰기 가능 속성 모두에 적용 됩니다.

장치에서 원격 분석을 전송 하는 데 속성을 사용 하지 마세요. 예를 들어와 같은 읽기 전용 속성은 `temperatureSetting=80` 장치 온도가 80로 설정 되 고 장치가이 온도에 도달 하거나이 온도를 유지 하려고 하는 것을 의미 합니다.

쓰기 가능한 속성의 경우 장치 응용 프로그램은 필요한 상태 코드, 버전 및 설명을 반환 하 여 속성 값을 받아서 적용 했는지 여부를 나타냅니다.

## <a name="telemetry"></a>원격 분석

IoT Central를 사용 하 여 대시보드 및 차트에 대 한 원격 분석을 볼 수 있으며 임계값에 도달 하면 규칙을 사용 하 여 작업을 트리거할 수 있습니다 IoT Central는 데이터 형식, 단위 및 표시 이름과 같은 장치 모델의 정보를 사용 하 여 원격 분석 값을 표시 하는 방법을 결정 합니다.

IoT Central 데이터 내보내기 기능을 사용 하 여 저장소 또는 Event Hubs 같은 다른 대상으로 원격 분석을 스트리밍할 수 있습니다.

## <a name="commands"></a>명령

명령은 기본적으로 30 초 이내에 실행 해야 하며, 명령이 도착할 때 장치를 연결 해야 합니다. 장치가 시간 내에 응답 하거나 장치가 연결 되지 않은 경우 명령이 실패 합니다.

명령에 요청 매개 변수를 사용할 수 있으며 응답을 반환할 수 있습니다.

### <a name="offline-commands"></a>오프 라인 명령

장치가 현재 오프 라인 상태인 경우 장치 템플릿의 명령에 대해 **오프 라인으로 큐** 옵션을 사용 하도록 설정 하 여 큐 명령을 선택할 수 있습니다.

오프 라인 명령은 솔루션에서 장치에 대 한 단방향 알림입니다. 오프 라인 명령은 요청 매개 변수를 포함할 수 있지만 응답을 반환 하지 않습니다.

> [!NOTE]
> 이 옵션은 IoT Central 웹 UI 에서만 사용할 수 있습니다. 이 설정은 장치 템플릿에서 모델 또는 인터페이스를 내보내는 경우에는 포함 되지 않습니다.

## <a name="cloud-properties"></a>클라우드 속성

클라우드 속성은 장치 템플릿의 일부 이지만 장치 모델의 일부가 아닙니다. 클라우드 속성을 사용 하면 솔루션 개발자가 IoT Central 응용 프로그램에 저장할 장치 메타 데이터를 지정할 수 있습니다. 클라우드 속성은 장치 개발자가 장치 모델을 구현 하기 위해 작성 하는 코드에 영향을 주지 않습니다.

솔루션 개발자는 장치 속성과 함께 대시보드 및 보기에 클라우드 속성을 추가 하 여 운영자가 응용 프로그램에 연결 된 장치를 관리할 수 있도록 합니다. 또한 솔루션 개발자는 규칙 정의의 일부로 클라우드 속성을 사용 하 여 연산자가 편집할 수 있는 임계값을 만들 수 있습니다.

## <a name="customizations"></a>사용자 지정

사용자 지정은 장치 템플릿의 일부 이지만 장치 모델의 일부가 아닙니다. 사용자 지정을 통해 솔루션 개발자는 장치 모델의 일부 정의를 개선 하거나 재정의할 수 있습니다. 예를 들어, 솔루션 개발자는 원격 분석 유형 또는 속성의 표시 이름을 변경할 수 있습니다. 솔루션 개발자는 사용자 지정을 사용 하 여 문자열 장치 속성의 최소 또는 최대 길이와 같은 유효성 검사를 추가할 수도 있습니다.

사용자 지정은 장치 개발자가 장치 모델을 구현 하기 위해 작성 하는 코드에 영향을 줄 수 있습니다. 예를 들어 사용자 지정은 원격 분석을 위해 최소 및 최대 문자열 길이 또는 최소 및 최대 숫자 값을 설정할 수 있습니다.

## <a name="views"></a>보기

솔루션 개발자는 운영자가 연결 된 장치를 모니터링 하 고 관리할 수 있는 뷰를 만듭니다. 보기는 장치 템플릿의 일부 이므로 뷰가 특정 장치 유형과 연결 됩니다. 보기에는 다음이 포함 될 수 있습니다.

- 원격 분석을 그리는 차트입니다.
- 읽기 전용 장치 속성을 표시 하는 타일입니다.
- 연산자가 쓰기 가능한 장치 속성을 편집할 수 있도록 하는 타일입니다.
- 타일을 사용 하 여 운영자가 클라우드 속성을 편집할 수 있습니다.
- 페이로드를 필요로 하는 명령을 포함 하 여 연산자를 호출할 수 있는 타일입니다.
- 레이블, 이미지 또는 markdown 텍스트를 표시 하는 타일입니다.

보기에 추가할 수 있는 원격 분석, 속성 및 명령은 장치 모델, 클라우드 속성 및 장치 템플릿의 사용자 지정에 따라 결정 됩니다.

## <a name="next-steps"></a>다음 단계

장치 개발자로 서 이제 장치 템플릿에 대해 알아보았습니다. 다음 단계는 [원격 분석, 속성 및 명령 페이로드](./concepts-telemetry-properties-commands.md) 를 읽어 장치가 IoT Central와 교환 하는 데이터에 대 한 자세한 내용을 확인 하는 것입니다.

솔루션 개발자로 서 다음 단계는 [Azure IoT Central 응용 프로그램에서 새 IoT 장치 유형 정의](./howto-set-up-template.md) 를 읽어 장치 템플릿을 만드는 방법에 대 한 자세한 내용을 확인 하는 것입니다.
