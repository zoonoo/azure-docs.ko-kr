---
title: Azure IoT Hub 오류 504101 게이트웨이 시간 정 문제 해결
description: 오류 504101 게이트웨이 타임아웃을 수정하는 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960673"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

이 문서에서는 **504101 GatewayTimeout** 오류의 원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

IoT Hub에서 장치에 직접 메서드를 호출하려고 할 때 오류 **504101 GatewayTimeout.**

## <a name="cause"></a>원인

### <a name="cause-1"></a>원인 1

IoT Hub에 오류가 발생하여 타이밍이 바오기 전에 직접 메서드가 완료되었는지 확인할 수 없습니다.

### <a name="cause-2"></a>원인 2

이전 버전의 Azure IoT C# SDK(<1.19.0)를 사용하는 경우 버그로 인해 장치와 IoT Hub 간의 AMQP 링크를 자동으로 삭제할 수 있습니다.

## <a name="solution"></a>해결 방법

### <a name="solution-1"></a>해결 방법 1

다시 시도합니다.

### <a name="solution-2"></a>해결 방법 2

Azure IOT C# SDK의 최신 버전으로 업그레이드합니다.