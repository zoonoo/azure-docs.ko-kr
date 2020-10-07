---
title: Azure IoT Central의 장치 템플릿 이란? Microsoft Docs
description: Azure IoT Central 장치 템플릿을 사용 하면 응용 프로그램에 연결 된 장치의 동작을 지정할 수 있습니다. 장치 템플릿은 장치에서 구현 해야 하는 원격 분석, 속성 및 명령을 지정 합니다. 또한 장치 템플릿은 운영자가 사용 하는 양식 및 대시보드와 같은 IoT Central에서 장치에 대 한 UI를 정의 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 75317b5c6af2d0ce89d2db32f4343d9cc73a1a81
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91813171"
---
# <a name="what-are-device-templates"></a>디바이스 템플릿이란?

_이 문서는 장치 개발자 및 솔루션 빌더에 적용 됩니다._

Azure IoT Central의 장치 템플릿은 응용 프로그램에 연결 하는 장치 유형에 대 한 특성 및 동작을 정의 하는 청사진입니다. 예를 들어 장치 템플릿은 IoT Central 올바른 단위 및 데이터 형식을 사용 하는 시각화를 만들 수 있도록 장치에서 보내는 원격 분석을 정의 합니다.

솔루션 빌더는 IoT Central 응용 프로그램에 장치 템플릿을 추가 합니다. 장치 개발자는 장치 템플릿에 정의 된 동작을 구현 하는 장치 코드를 작성 합니다.

장치 템플릿에는 다음 섹션이 포함 됩니다.

- _DCM (장치 기능 모델)_ 입니다. 장치 템플릿의이 부분은 장치가 응용 프로그램과 상호 작용 하는 방법을 정의 합니다. 장치 개발자는 DCM에 정의 된 동작을 구현 합니다.
    - _인터페이스_. DCM은 장치에서 구현 해야 하는 원격 분석, 속성 및 명령을 정의 하는 하나 이상의 인터페이스를 포함 합니다.
- _클라우드 속성_. 이 장치 템플릿 파트를 통해 솔루션 개발자는 저장할 장치 메타 데이터를 지정할 수 있습니다. 클라우드 속성은 장치와 동기화 되지 않으며 응용 프로그램에만 존재 합니다. 클라우드 속성은 장치 개발자가 DCM을 구현 하기 위해 작성 하는 코드에 영향을 주지 않습니다.
- _사용자 지정_. 이 장치 템플릿 부분에서는 솔루션 개발자가 DCM의 일부 정의를 재정의할 수 있습니다. 사용자 지정은 솔루션 개발자가 속성의 표시 이름 변경 또는 원격 분석 값을 표시 하는 데 사용 된 색과 같은 응용 프로그램에서 값을 처리 하는 방법을 조정 하려는 경우에 유용 합니다. 사용자 지정은 장치 개발자가 DCM을 구현 하기 위해 작성 하는 코드에 영향을 주지 않습니다.
- _뷰입니다_. 이 장치 템플릿 파트를 통해 솔루션 개발자는 장치에서 데이터를 보는 시각화를 정의 하 고 장치를 관리 하 고 제어할 수 있습니다. 보기는 DCM, 클라우드 속성 및 사용자 지정을 사용 합니다. 보기는 장치 개발자가 DCM을 구현 하기 위해 작성 하는 코드에 영향을 주지 않습니다.

## <a name="device-capability-models"></a>디바이스 기능 모델

DCM은 장치가 IoT Central 응용 프로그램과 상호 작용 하는 방식을 정의 합니다. 장치 개발자는 장치가 장치를 모니터링 하 고 관리할 수 IoT Central 있도록 DCM에 정의 된 동작을 구현 하는지 확인 해야 합니다. DCM은 하나 이상의 _인터페이스로_구성 되며 각 인터페이스는 _원격 분석_ 유형, _장치 속성_및 _명령의_컬렉션을 정의할 수 있습니다. 솔루션 개발자는 DCM을 정의 하는 JSON 파일을 장치 템플릿으로 가져오거나 IoT Central의 웹 UI를 사용 하 여 DCM을 만들거나 편집할 수 있습니다. 웹 UI를 사용 하 여 만든 DCM을 변경 하려면 [장치 템플릿 버전을](./howto-version-device-template.md)지정 해야 합니다.

솔루션 개발자는 DCM이 포함 된 JSON 파일을 내보낼 수도 있습니다. 장치 개발자는이 JSON 문서를 사용 하 여 장치가 IoT Central 응용 프로그램과 통신 하는 방법을 이해할 수 있습니다.

DCM을 정의 하는 JSON 파일은 [DTDL (디지털 쌍 정의 언어) V1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)을 사용 합니다. IoT Central JSON 파일에는 별도의 파일이 아니라 인라인으로 정의 된 인터페이스가 있는 DCM이 포함 될 것으로 예상 됩니다.

일반적인 IoT 장치는 다음과 같이 구성 됩니다.

- 사용자 지정 파트-장치를 고유 하 게 만드는 항목입니다.
- 모든 장치에 공통적인 작업 인 표준 부분

이러한 파트는 DCM에서 _인터페이스_ 라고 합니다. 인터페이스는 장치가 구현 하는 각 파트에 대 한 세부 정보를 정의 합니다. 인터페이스는 DCMs에서 재사용할 수 있습니다.

다음 예제에서는 두 개의 인터페이스가 있는 환경 센서 장치에 대 한 장치 기능 모델의 개요를 보여 줍니다.

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

기능 모델에는 다음과 같은 몇 가지 필수 필드가 있습니다.

- `@id`: 단순한 단일 리소스 이름 형식의 고유 ID입니다.
- `@type`:이 개체가 기능 모델 임을 선언 합니다.
- `@context`: 기능 모델에 사용 되는 DTDL 버전을 지정 합니다.
- `implements`: 장치가 구현 하는 인터페이스를 나열 합니다.

Implements 섹션에서 인터페이스 목록의 각 항목에는 다음이 포함 됩니다.

- `name`: 인터페이스의 프로그래밍 이름입니다.
- `schema`: 기능 모델이 구현 하는 인터페이스입니다.

인터페이스에는 다음과 같은 몇 가지 필수 필드가 있습니다.

- `@id`: 단순한 단일 리소스 이름 형식의 고유 ID입니다.
- `@type`:이 개체가 인터페이스 임을 선언 합니다.
- `@context`: 인터페이스에 사용 되는 DTDL 버전을 지정 합니다.
- `contents`: 장치를 구성 하는 속성, 원격 분석 및 명령을 나열 합니다.

표시 이름 및 설명과 같은 추가 정보를 기능 모델에 추가 하는 데 사용할 수 있는 몇 가지 선택적 필드가 있습니다.

## <a name="interfaces"></a>인터페이스

DTDL을 사용 하면 장치의 기능을 설명할 수 있습니다. 관련 기능은 인터페이스로 그룹화 됩니다. 인터페이스는 장치에서 구현 하는 속성, 원격 분석 및 명령을 설명 합니다.

- `Properties`. 속성은 장치의 상태를 나타내는 데이터 필드입니다. 속성을 사용 하 여 냉각수 펌프의 꺼짐 상태와 같은 장치의 내구성이 있는 상태를 나타냅니다. 속성은 장치의 펌웨어 버전 같은 기본 장치 속성을 나타낼 수도 있습니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 장치만 읽기 전용 속성의 값을 업데이트할 수 있습니다. 운영자는 장치에 보낼 쓰기 가능 속성의 값을 설정할 수 있습니다.
- `Telemetry`. 원격 분석 필드는 센서의 측정값을 나타냅니다. 장치가 센서를 사용 하는 경우에는 센서 데이터를 포함 하는 원격 분석 이벤트를 전송 해야 합니다.
- `Commands`. 명령은 장치의 사용자가 장치에서 실행할 수 있는 메서드를 나타냅니다. 예를 들면 reset 명령 또는 팬을 전환 하는 명령입니다.

다음 예제에서는 환경 센서 인터페이스 정의를 보여 줍니다.

```json
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
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

이 예제에서는 두 개의 속성 (읽기 전용 및 쓰기 가능한 하나), 원격 분석 유형 및 두 개의 명령을 보여 줍니다. 최소 필드 설명에는 다음이 포함 됩니다.

- `@type` 기능 유형을 지정 하려면, `Telemetry` `Property` 또는 `Command` 입니다.  일부 경우에는 IoT Central에서 값을 처리 하는 방법에 대 한 몇 가지 가정을 수행할 수 있도록 의미 체계 형식이 포함 되어 있습니다.
- `name` 원격 분석 값의 경우
- `schema` 원격 분석 또는 속성의 데이터 형식을 지정 합니다. 이 값은 double, integer, boolean 또는 string과 같은 기본 형식일 수 있습니다. 복합 개체 형식, 배열 및 맵도 지원 됩니다.
- `commandType` 명령을 처리 하는 방법을 지정 합니다.

표시 이름, 설명 등의 선택적 필드를 사용 하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

## <a name="properties"></a>속성

기본적으로 속성은 읽기 전용입니다. 읽기 전용 속성은 장치가 IoT Central 응용 프로그램에 대 한 속성 값 업데이트를 보고 하는 것을 의미 합니다. IoT Central 응용 프로그램에서 읽기 전용 속성 값을 설정할 수 없습니다.

인터페이스에서 속성을 쓰기 가능으로 표시할 수도 있습니다. 장치는 IoT Central 응용 프로그램에서 쓰기 가능한 속성에 대 한 업데이트를 수신 하 고, 응용 프로그램에 대 한 속성 값 업데이트를 보고할 수 있습니다.

장치를 연결 하 여 속성 값을 설정할 필요가 없습니다. 업데이트 된 값은 다음에 장치를 응용 프로그램에 연결할 때 전송 됩니다. 이 동작은 읽기 전용 및 쓰기 가능 속성 모두에 적용 됩니다.

장치에서 원격 분석을 전송 하는 데 속성을 사용 하지 마세요. 예를 들어와 같은 읽기 전용 속성은 `temperatureSetting=80` 장치 온도가 80로 설정 되 고 장치가이 온도에 도달 하거나이 온도를 유지 하려고 하는 것을 의미 합니다.

쓰기 가능한 속성의 경우 장치 응용 프로그램은 필요한 상태 코드, 버전 및 설명을 반환 하 여 속성 값을 받아서 적용 했는지 여부를 나타냅니다.

## <a name="telemetry"></a>원격 분석

IoT Central를 사용 하 여 대시보드 및 차트에 대 한 원격 분석을 볼 수 있으며 임계값에 도달 하면 규칙을 사용 하 여 작업을 트리거할 수 있습니다 IoT Central는 데이터 형식, 단위 및 표시 이름과 같은 DCM의 정보를 사용 하 여 원격 분석 값을 표시 하는 방법을 결정 합니다.

IoT Central 데이터 내보내기 기능을 사용 하 여 저장소 또는 Event Hubs 같은 다른 대상으로 원격 분석을 스트리밍할 수 있습니다.

## <a name="commands"></a>명령

명령은 동기 또는 비동기 명령입니다. 동기 명령은 기본적으로 30 초 이내에 실행 해야 하며, 명령이 도착할 때 장치를 연결 해야 합니다. 장치가 시간 내에 응답 하거나 장치가 연결 되지 않은 경우 명령이 실패 합니다.

장기 실행 작업에는 비동기 명령을 사용 합니다. 장치는 원격 분석 메시지를 사용 하 여 진행률 정보를 보냅니다. 이러한 진행 메시지의 헤더 속성은 다음과 같습니다.

- `iothub-command-name`: 명령 이름입니다 (예:) `UpdateFirmware` .
- `iothub-command-request-id`: 서버 쪽에서 생성 되어 초기 호출에서 장치로 전송 되는 요청 ID입니다.
- `iothub-interface-id`:이 명령이 정의 된 인터페이스의 ID (예:)입니다 `urn:example:AssetTracker:1` .
 `iothub-interface-name`:이 인터페이스의 인스턴스 이름입니다 (예:) `myAssetTracker` .
- `iothub-command-statuscode`: 장치에서 반환 된 상태 코드 (예:) `202` 입니다.

## <a name="cloud-properties"></a>클라우드 속성

클라우드 속성은 장치 템플릿의 일부 이지만 DCM의 일부가 아닙니다. 클라우드 속성을 사용 하면 솔루션 개발자가 IoT Central 응용 프로그램에 저장할 장치 메타 데이터를 지정할 수 있습니다. 클라우드 속성은 장치 개발자가 DCM을 구현 하기 위해 작성 하는 코드에 영향을 주지 않습니다.

솔루션 개발자는 장치 속성과 함께 대시보드 및 보기에 클라우드 속성을 추가 하 여 운영자가 응용 프로그램에 연결 된 장치를 관리할 수 있도록 합니다. 또한 솔루션 개발자는 규칙 정의의 일부로 클라우드 속성을 사용 하 여 연산자가 편집할 수 있는 임계값을 만들 수 있습니다.

## <a name="customizations"></a>사용자 지정

사용자 지정은 장치 템플릿의 일부 이지만 DCM의 일부가 아닙니다. 사용자 지정을 통해 솔루션 개발자가 DCM의 일부 정의를 개선 하거나 재정의할 수 있습니다. 예를 들어, 솔루션 개발자는 원격 분석 유형 또는 속성의 표시 이름을 변경할 수 있습니다. 솔루션 개발자는 사용자 지정을 사용 하 여 문자열 장치 속성의 최소 또는 최대 길이와 같은 유효성 검사를 추가할 수도 있습니다.

사용자 지정은 장치 개발자가 DCM을 구현 하기 위해 작성 하는 코드에 영향을 줄 수 있습니다. 예를 들어 사용자 지정은 원격 분석을 위해 최소 및 최대 문자열 길이 또는 최소 및 최대 숫자 값을 설정할 수 있습니다.

## <a name="views"></a>뷰

솔루션 개발자는 운영자가 연결 된 장치를 모니터링 하 고 관리할 수 있는 뷰를 만듭니다. 보기는 장치 템플릿의 일부 이므로 뷰가 특정 장치 유형과 연결 됩니다. 보기에는 다음이 포함 될 수 있습니다.

- 원격 분석을 그리는 차트입니다.
- 읽기 전용 장치 속성을 표시 하는 타일입니다.
- 연산자가 쓰기 가능한 장치 속성을 편집할 수 있도록 하는 타일입니다.
- 타일을 사용 하 여 운영자가 클라우드 속성을 편집할 수 있습니다.
- 페이로드를 필요로 하는 명령을 포함 하 여 연산자를 호출할 수 있는 타일입니다.
- 레이블, 이미지 또는 markdown 텍스트를 표시 하는 타일입니다.

보기에 추가할 수 있는 원격 분석, 속성 및 명령은 DCM, 클라우드 속성 및 장치 템플릿의 사용자 지정에 따라 결정 됩니다.

## <a name="next-steps"></a>다음 단계

장치 개발자로 서 이제 장치 템플릿에 대해 알아보았습니다. 다음 단계는 [원격 분석, 속성 및 명령 페이로드](./concepts-telemetry-properties-commands.md) 를 읽어 장치가 IoT Central와 교환 하는 데이터에 대 한 자세한 내용을 확인 하는 것입니다.

솔루션 개발자로 서 다음 단계는 [Azure IoT Central 응용 프로그램에서 새 IoT 장치 유형 정의](./howto-set-up-template.md) 를 읽어 장치 템플릿을 만드는 방법에 대 한 자세한 내용을 확인 하는 것입니다.
