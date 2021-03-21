---
title: Azure IoT Hub 구성 파일에 대 한 장치 업데이트 이해 | Microsoft Docs
description: Azure IoT Hub 구성 파일에 대 한 장치 업데이트를 이해 합니다.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101662945"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>IoT Hub 구성 파일에 대 한 장치 업데이트

"adu-conf.txt"는 다음 구성을 관리 하기 위해 만들 수 있는 선택적 파일입니다.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4 deviceProperties ["manufacturer"]
* AzureDeviceUpdateCore: 4Clientmetadata: 4 deviceProperties ["model"]
* DeviceInformation. 제조업체
* DeviceInformation. 모델
* 장치 연결 문자열 (장치 업데이트 에이전트에서 알 수 없는 경우).

## <a name="purpose"></a>용도
장치 업데이트 에이전트는 먼저 `manufacturer` `model` 장치에서 [인터페이스 계층](device-update-agent-overview.md#the-interface-layer)에 사용할 및 값을 가져오려고 시도 합니다. 이 작업이 실패 하면 장치 업데이트 에이전트는 다음에 "adu-conf.txt" 파일을 찾고 여기에서 값을 사용 합니다. 두 시도가 모두 실패 하면 장치 업데이트 에이전트는 [기본값](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) 을 사용 합니다.

[ADU Core 인터페이스](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) 및 [장치 정보 인터페이스](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)에 대해 자세히 알아보세요.

## <a name="file-location"></a>파일 위치

Linux 시스템 내의 라는 파티션 또는 디스크에서 `adu` 다음 필드를 사용 하 여 "adu-conf.txt" 라는 텍스트 파일을 만듭니다.

## <a name="list-of-fields"></a>필드 목록

|Name|설명|
|-----------|--------------------|
|connection_string|장치에서 IoT Hub에 연결 하는 데 사용할 수 있는 미리 프로 비전 된 연결 문자열입니다. 참고: [Azure IoT Id 서비스](https://azure.github.io/iot-identity-service/) 를 통해 장치 업데이트 에이전트를 프로 비전 하는 경우에는 필요 하지 않습니다.|
|aduc_manufacturer|`AzureDeviceUpdateCore:4.ClientMetadata:4`업데이트 배포를 대상으로 하는 장치를 분류 하기 위해 인터페이스에서 보고 됩니다.|
|aduc_model|`AzureDeviceUpdateCore:4.ClientMetadata:4`업데이트 배포를 대상으로 하는 장치를 분류 하기 위해 인터페이스에서 보고 됩니다.|
|제조업체|장치 업데이트 에이전트에서 인터페이스의 일부로 보고 `DeviceInformation` 합니다.|
|model|장치 업데이트 에이전트에서 인터페이스의 일부로 보고 `DeviceInformation` 합니다.|

## <a name="example-adu-conftxt-file-contents"></a>예제 "adu-conf.txt" 파일 내용

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
