---
title: Azure Digital Twins를 사용한 장치 연결 및 원격 분석 수신 | Microsoft Docs
description: Azure Digital Twins를 사용한 장치 온보딩 개요
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323926"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>장치 연결 및 원격 분석 수신

장치 및 센서가 보낸 원격 분석 데이터는 모든 IoT 솔루션의 백본을 형성합니다. 따라서 이러한 별도의 리소스를 나타내고 위치의 컨텍스트 내에서 이 리소스를 관리하는 것은 IoT 앱 개발의 주요 관심사입니다. Azure Digital Twins는 공간 인텔리전스 그래프를 사용하여 장치와 센서를 통합해 IoT 솔루션 개발을 단순화합니다.

시작하려면 공간 그래프의 루트에서 `IoTHub` 리소스를 생성하여 루트 공간 아래의 모든 장치가 메시지를 전송하도록 허용해야 합니다. IoT Hub가 만들어지고 센서가 있는 장치가 Digital Twins 인스턴스 내에 등록되면 장치는 [Azure IoT 장치 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks)를 통해 Digital Twins 서비스에 데이터 전송을 시작할 수 있습니다.

장치 온보딩에 대한 단계별 가이드는 [Digital Twins 배포 및 구성 자습서](tutorial-facilities-setup.md)에서 찾을 수 있습니다. 간략히 보면 단계는 다음과 같습니다.

- [Azure Portal](https://portal.azure.com)에서 Azure Digital Twins 인스턴스 배포
- 그래프에 공간 만들기
- `IoTHub` 리소스를 만들어 그래프의 공간에 할당
- 그래프에서 장치 및 센서를 생성하여 위의 단계에서 생성된 공간에 할당
- 조건에 따라 원격 분석 메시지를 필터링하는 검사기 만들기
- 원격 분석 메시지의 사용자 지정 처리를 위해 [**사용자 정의 함수**](concepts-user-defined-functions.md)를 만들어 그래프의 공간에 할당
- 사용자 정의 함수가 그래프 데이터에 액세스하도록 허용하는 역할 할당
- Digital Twins 관리 API에서 IoT Hub 장치 연결 문자열 가져오기
- Azure IoT 장치 SDK를 사용하여 장치에서 장치 연결 문자열 구성

아래에서는 Digital Twins 관리 API에서 IoT Hub 장치 연결 문자열을 가져오는 방법 및 IoT Hub 원격 분석 메시지 형식을 사용하여 센서 기반 원격 분석을 전송하는 방법을 알아봅니다. Digital Twins에서는 수신되는 각 원격 분석 정보가 공간 그래프 내에서 센서와 연결되어야 데이터가 적절한 공간 컨텍스트 내에서 처리되고 라우팅될 수 있습니다.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>관리 API에서 IoT Hub 장치 연결 문자열 가져오기

IoT Hub 장치 연결 문자열을 가져오려면 `includes=ConnectionString` 매개 변수를 사용하여 장치 API에서 GET 호출을 수행합니다. 장치 GUID 또는 하드웨어 ID로 필터링하여 지정 장치를 찾을 수 있습니다.

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| 사용자 지정 특성 이름 | 다음 항목으로 교체 |
| --- | --- |
| `yourManagementApiUrl` | 관리 API의 전체 URL 경로 |
| `yourDeviceGuid` | 장치 ID |
| `yourDeviceHardwareId` | 장치 하드웨어 ID |

응답 페이로드에서 장치의 `connectionString` 속성을 복사합니다. 이 속성은 Azure IoT 장치 SDK를 호출하여 Azure Digital Twins에 데이터를 전송할 때 사용할 것입니다.

## <a name="device-to-cloud-message"></a>장치-클라우드 메시지

장치의 메시지 형식과 페이로드를 솔루션 요구 사항에 맞게 사용자 지정할 수 있습니다. [Azure IoT 장치 클라이언트 메시지 클래스 Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___)에서 지원하는 바이트 배열 또는 스트림으로 직렬화할 수 있는 모든 데이터 계약을 사용할 수 있습니다. 해당 사용자 정의 함수에서 데이터 계약을 디코드하는 경우, 메시지는 선택한 사용자 지정 이진 형식일 수 있습니다. 장치-클라우드 메시지의 유일한 요구 사항은 메시지가 처리 엔진으로 적절히 라우팅되도록 하기 위해 속성 집합(아래 참조)을 유지 관리하는 것입니다.

### <a name="telemetry-properties"></a>원격 분석 속성

`Message`의 페이로드 콘텐츠는 최대 256kb 크기의 임의 데이터일 수 있지만, 예상되는 [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet)에 대한 몇 가지 요구 사항이 있습니다. 아래에서 설명하는 단계는 시스템에서 지원하는 필수 및 선택적 속성을 반영합니다.

| 속성 이름 | 값 | 필수 | 설명 |
|---|---|---|---|
| DigitalTwins-Telemetry | 1.0 | 예 | 시스템에 메시지를 식별하는 상수 값입니다. |
| DigitalTwins-SensorHardwareId | `string(72)` | 예 | `Message`를 전송하는 센서의 고유 식별자입니다. 이 값은 시스템에서 처리하려면 개체의 `HardwareId` 속성과 일치해야 합니다. 예를 들어 `00FF0643BE88-CO2` |
| CreationTimeUtc | `string` | no | 페이로드의 샘플링 시간을 식별하는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 형식의 날짜 문자열입니다. 예를 들어 `2018-09-20T07:35:00.8587882-07:00` |
| CorrelationId | `string` | no | 시스템에서 이벤트를 추적하는 데 사용할 수 있는 `uuid`입니다. 예를 들어 `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>Digital Twins로 메시지 보내기

Digital Twins 서비스에 메시지를 보내려면 DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) 또는 [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) 호출을 사용합니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins의 데이터 처리 및 사용자 정의 함수 기능에 대해 알아보려면 [Azure Digital Twins data processing and user-defined functions](concepts-user-defined-functions.md)(Azure Digital Twins 데이터 처리 및 사용자 정의 함수)를 참조하세요.

