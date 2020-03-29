---
title: 문제 해결 Azure IoT 허브 오류 400027 연결강력하게폐쇄된 NewConnection
description: 오류 400027 연결을 해결하는 방법을 이해포스로폐쇄새로운 연결
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960530"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

이 문서에서는 **400027 ConnectionForcefullyClosedOnNewConnection** 오류에 대한 원인과 해결 방법에 대해 설명합니다.

## <a name="symptoms"></a>증상

장치 대 클라우드 트윈 작업(예: 읽기 또는 패치 보고된 속성) 또는 직접 메서드 호출 오류 코드 **400027으로**실패합니다.

## <a name="cause"></a>원인

다른 클라이언트는 동일한 자격 증명을 사용하여 IoT Hub에 대한 새 연결을 만들었기 때문에 IoT Hub는 이전 연결을 닫았습니다. IoT Hub는 동일한 자격 증명 집합을 사용하여 두 개 이상의 클라이언트가 연결할 수 있도록 허용하지 않습니다.

## <a name="solution"></a>해결 방법

각 클라이언트가 자체 ID를 사용하여 IoT Hub에 연결되도록 합니다.