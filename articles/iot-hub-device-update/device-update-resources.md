---
title: Azure IoT Hub 리소스의 장치 업데이트 이해 | Microsoft Docs
description: Azure IoT Hub 리소스의 장치 업데이트 이해
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663646"
---
# <a name="device-update-resources"></a>장치 업데이트 리소스

IoT Hub의 장치 업데이트를 사용하려면 장치 업데이트 계정 및 인스턴스 리소스를 만들어야 합니다. 

## <a name="device-update-account"></a>장치 업데이트 계정

장치 업데이트 계정은 Azure 구독 내에서 만드는 리소스입니다. 장치 업데이트 계정 수준에서 장치 업데이트 계정을 만들 지역을 선택할 수 있습니다. 장치 업데이트에 액세스할 사용자에게 부여할 권한을 설정할 수도 있습니다.


## <a name="device-update-instance"></a>장치 업데이트 인스턴스
계정을 만든 후에는 장치 업데이트 인스턴스를 만들어야 합니다. 인스턴스는 특정 IoT hub와 연결된 업데이트 및 배포가 포함된 논리적 컨테이너입니다. 장치 업데이트는 IoT hub를 장치 디렉터리로 사용하고 장치와의 통신 채널을 사용합니다. 

공개 미리 보기 중에는 구독마다 두 개의 장치 업데이트 계정을 만들 수 있습니다. 또한 계정마다 두 개의 장치 업데이트 인스턴스를 만들 수 있습니다.

## <a name="configuring-device-update-linked-iot-hub"></a>장치 업데이트 연결된 IoT Hub 구성 

장치 업데이트는 IoT Hub에서 변경 알림을 받을 수 있도록 "기본 제공" Event Hub와 통합됩니다. 인스턴스 내에서 "IoT Hub 구성" 단추를 클릭하면 IoT 장치와 통신하는 데 필요한 메시지 경로와 액세스 정책이 구성됩니다. 

장치 업데이트에 대해 다음 메시지 경로가 구성됩니다.

|   경로 이름    | 라우팅 쿼리  | Description  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |디지털 쌍 변경 이벤트 수신 대기  |
|  DeviceUpdate.DeviceLifeCycle | opType = 'deleteDeviceIdentity'  | 삭제된 장치 수신 대기 |
|  DeviceUpdate.TelemetryModelInformation | iothub-interface-id = "urn:azureiot:ModelDiscovery:ModelInformation:1 | 새 장치 유형 수신 대기 |
|  DeviceUpdate.DeviceTwinEvents| (opType = 'updateTwin' OR opType = 'replaceTwin') 및 IS_DEFINED($body.tags.ADUGroup) | 새 장치 업데이트 그룹 수신 대기 |

## <a name="next-steps"></a>다음 단계

[장치 업데이트 리소스 만들기](./create-device-update-account.md)
