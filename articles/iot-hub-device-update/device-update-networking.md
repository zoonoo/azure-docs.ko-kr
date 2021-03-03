---
title: IoT Hub 네트워크 요구 사항에 대 한 장치 업데이트 | Microsoft Docs
description: IoT Hub에 대 한 장치 업데이트는 다양 한 용도로 다양 한 네트워크 포트를 사용 합니다.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679583"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>IoT Hub에 대 한 장치 업데이트에 사용 되는 포트
ADU는 다양 한 용도로 다양 한 네트워크 포트를 사용 합니다.

## <a name="default-ports"></a>기본 포트

목적|포트 번호 |
---|---
Networks/CDNs에서 다운로드  | 80 (HTTP 프로토콜)
MCC/CDNs에서 다운로드 | 80 (HTTP 프로토콜)
Azure IoT Hub에 대 한 ADU Agent 연결  | 8883 (MQTT 프로토콜)

## <a name="use-azure-iot-hub-supported-protocols"></a>지원 되는 Azure IoT Hub 프로토콜 사용
ADU 에이전트는 지원 되는 Azure IoT Hub 프로토콜을 사용 하도록 수정할 수 있습니다.

지원 되는 프로토콜의 현재 목록에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) .
