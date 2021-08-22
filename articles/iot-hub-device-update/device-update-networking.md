---
title: IoT Hub 네트워크 요구 사항에 대한 디바이스 업데이트 | Microsoft Docs
description: IoT Hub에 대한 디바이스 업데이트는 다양한 용도로 다양한 네트워크 포트를 사용합니다.
author: vimeht
ms.author: vimeht
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: aeeffa55a4f7f90742616e137328948f3ffa7a5c
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529512"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>IoT Hub에 대한 디바이스 업데이트에 사용되는 포트
ADU는 다양한 용도로 다양한 네트워크 포트를 사용합니다.

## <a name="default-ports"></a>기본 포트

목적|포트 번호 |
---|---
Networks/CDNs에서 다운로드  | 80(HTTP 프로토콜)
MCC/CDNs에서 다운로드 | 80(HTTP 프로토콜)
Azure IoT Hub에 대한 ADU Agent 연결  | 8883(MQTT 프로토콜)

## <a name="use-azure-iot-hub-supported-protocols"></a>Azure IoT Hub가 지원되는 프로토콜 사용
ADU 에이전트는 지원되는 Azure IoT Hub 프로토콜을 사용하도록 수정할 수 있습니다.

지원되는 프로토콜의 현재 목록에 대해 [자세히 알아보세요](../iot-hub/iot-hub-devguide-protocols.md).
