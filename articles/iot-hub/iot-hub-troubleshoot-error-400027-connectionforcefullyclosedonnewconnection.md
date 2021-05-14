---
title: 문제 해결 Azure IoT Hub 오류 400027 ConnectionForcefullyClosedOnNewConnection
description: 오류 400027 ConnectionForcefullyClosedOnNewConnection 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061369"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

이 문서에서는 **400027 ConnectionForcefullyClosedOnNewConnection** 오류에 관한 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

.NET SDK 및 MQTT 전송 유형을 사용하여 **ConnectionStatusChangeReason** 으로 인해 **Communication_Error** 와 디바이스의 연결이 끊어집니다.

디바이스-클라우드 쌍 작업(예: 보고된 속성 읽기 또는 패치) 또는 직접 메서드 호출이 실패하고 오류 코드 **400027** 이 표시됩니다.

## <a name="cause"></a>원인

다른 클라이언트가 동일한 자격 증명을 사용하여 IoT Hub에 대한 새 연결을 만들었기 때문에 IoT Hub의 이전 연결을 닫았습니다. IoT Hub에서는 동일한 자격 증명 집합을 사용하여 둘 이상의 클라이언트를 연결할 수 없습니다.

## <a name="solution"></a>솔루션

각 클라이언트가 고유의 ID를 사용하여 IoT Hub에 연결되는지 확인합니다.