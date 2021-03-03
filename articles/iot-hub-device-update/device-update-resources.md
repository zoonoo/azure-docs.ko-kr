---
title: Azure IoT Hub 리소스에 대 한 장치 업데이트 이해 | Microsoft Docs
description: Azure IoT Hub 리소스에 대 한 장치 업데이트 이해
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663646"
---
# <a name="device-update-resources"></a>장치 업데이트 리소스

IoT Hub에 대 한 장치 업데이트를 사용 하려면 장치 업데이트 계정 및 인스턴스 리소스를 만들어야 합니다. 

## <a name="device-update-account"></a>장치 업데이트 계정

장치 업데이트 계정은 Azure 구독 내에서 생성 되는 리소스입니다. 장치 업데이트 계정 수준에서 장치 업데이트 계정을 만들 지역을 선택할 수 있습니다. 장치 업데이트에 대 한 액세스 권한이 있는 사용자에 게 권한을 부여 하는 권한을 설정할 수도 있습니다.


## <a name="device-update-instance"></a>장치 업데이트 인스턴스
계정을 만든 후에는 장치 업데이트 인스턴스를 만들어야 합니다. 인스턴스는 특정 IoT hub와 연결 된 업데이트 및 배포를 포함 하는 논리적 컨테이너입니다. 장치 업데이트는 IoT hub를 장치 디렉터리로 사용 하 고 장치와 통신 채널을 사용 합니다. 

공개 미리 보기 중에는 구독 마다 두 개의 장치 업데이트 계정을 만들 수 있습니다. 또한 계정 마다 두 개의 장치 업데이트 인스턴스를 만들 수 있습니다.

## <a name="configuring-device-update-linked-iot-hub"></a>장치 업데이트 연결 IoT Hub 구성 

장치 업데이트는 IoT Hub에서 변경 알림을 받도록 하기 위해 "기본 제공" 이벤트 허브와 통합 됩니다. 인스턴스 내에서 "IoT Hub 구성" 단추를 클릭 하면 IoT 장치와 통신 하는 데 필요한 메시지 경로 및 액세스 정책이 구성 됩니다. 

다음 메시지 경로는 장치 업데이트에 대해 구성 됩니다.

|   경로 이름    | 라우팅 쿼리  | 설명  |
| :--------- | :---- |:---- |
|  DeviceUpdate | true |디지털 쌍 변경 이벤트를 수신 대기 합니다.  |
|  DeviceUpdate. Deviceupdate 주기 | opType = ' deleteDeviceIdentity '  | 삭제 된 장치를 수신 대기 합니다. |
|  DeviceUpdate | iothub-id = "urn: azureiot: ModelDiscovery: Modeldiscovery: 1 | 새 장치 유형을 수신 대기 합니다. |
|  DeviceUpdate. DeviceTwinEvents| (opType = ' updateTwin' ' 또는 opType = ' replaceTwin') 및 IS_DEFINED ($body) | 새 장치 업데이트 그룹을 수신 대기 합니다. |

## <a name="next-steps"></a>다음 단계

[장치 업데이트 리소스 만들기](./create-device-update-account.md)
