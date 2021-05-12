---
title: Azure IoT Hub 구성 파일을 위한 디바이스 업데이트 이해 | Microsoft Docs
description: Azure IoT Hub 구성 파일을 위한 디바이스 업데이트를 이해합니다.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662945"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>IoT Hub 구성 파일에 대한 디바이스 업데이트

"adu-conf.txt"는 다음 구성을 관리하기 위해 만들 수 있는 선택적 파일입니다.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4 deviceProperties ["manufacturer"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4 deviceProperties ["manufacturer"]
* DeviceInformation. 제조업체
* DeviceInformation. 모델
* 디바이스 연결 문자열 (디바이스 업데이트 에이전트에서 알 수 없는 경우).

## <a name="purpose"></a>목적
디바이스 업데이트 에이전트는 먼저 [인터페이스 계층](device-update-agent-overview.md#the-interface-layer)에 사용할 디바이스에서 `manufacturer` 및 `model` 값을 가져오려고 시도합니다. 이 작업이 실패하면 디바이스 업데이트 에이전트는 다음에 "adu-conf.txt" 파일을 찾고 여기에서 값을 사용합니다. 두 시도가 모두 실패하면 디바이스 업데이트 에이전트는 [기본값](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt)을 사용합니다.

[ADU Core 인터페이스](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) 및 [디바이스 정보 인터페이스](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)에 대해 자세히 알아보세요.

## <a name="file-location"></a>파일 위치

Linux 시스템 내의 파티션 또는 `adu`이라는 디스크에서 다음 필드를 사용하여 "adu-conf.txt"라는 텍스트 파일을 만듭니다.

## <a name="list-of-fields"></a>필드 목록

|이름|Description|
|-----------|--------------------|
|연결_문자열|디바이스에서 IoT Hub에 연결하는 데 사용할 수 있는 미리 프로 비전된 연결 문자열입니다. 참고: [Azure IoT ID 서비스](https://azure.github.io/iot-identity-service/)를 통해 디바이스 업데이트 에이전트를 프로비전하는 경우에는 필요하지 않습니다|
|aduc_manufacturer|`AzureDeviceUpdateCore:4.ClientMetadata:4`업데이트 배포를 대상으로 하는 디바이스를 분류하기 위해 인터페이스에서 보고됩니다.|
|aduc_model|`AzureDeviceUpdateCore:4.ClientMetadata:4`업데이트 배포를 대상으로 하는 디바이스를 분류하기 위해 인터페이스에서 보고됩니다.|
|제조업체|디바이스 업데이트 에이전트를 `DeviceInformation` 인터페이스의 일부로 보고합니다.|
|model|디바이스 업데이트 에이전트를 `DeviceInformation` 인터페이스의 일부로 보고합니다.|

## <a name="example-adu-conftxt-file-contents"></a>예제 "adu-conf.txt" 파일 내용

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
