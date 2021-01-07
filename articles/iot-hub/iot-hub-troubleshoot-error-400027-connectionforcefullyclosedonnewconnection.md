---
title: 문제 해결 Azure IoT Hub 오류 400027 ConnectionForcefullyClosedOnNewConnection
description: 400027 오류를 해결 하는 방법 이해 ConnectionForcefullyClosedOnNewConnection
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
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506331"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

이 문서에서는 **400027 ConnectionForcefullyClosedOnNewConnection** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

.NET SDK 및 MQTT 전송 유형을 사용 하 여 장치 **에서 Connectionstatuschangereason** 으로 **Communication_Error** 연결을 끊습니다.

장치-클라우드 쌍 작업 (예: 보고 된 속성 읽기 또는 패치) 또는 직접 메서드 호출이 실패 하 고 오류 코드 **400027** 이 발생 합니다.

## <a name="cause"></a>원인

다른 클라이언트가 동일한 자격 증명을 사용 하 여 IoT Hub에 대 한 새 연결을 만들어 IoT Hub 이전 연결을 닫았습니다. IoT Hub는 동일한 자격 증명 집합을 사용 하 여 두 개 이상의 클라이언트에 연결할 수 없습니다.

## <a name="solution"></a>해결 방법

각 클라이언트가 자체 id를 사용 하 여 IoT Hub에 연결 하는지 확인 합니다.