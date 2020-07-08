---
title: 문제 해결 Azure IoT Hub 오류 404103 DeviceNotOnline
description: 404103 오류를 해결 하는 방법 이해 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960816"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

이 문서에서는 **404103 DeviceNotOnline** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

장치가 온라인 상태인 경우에도 **404103 DeviceNotOnline** 오류가 발생 하 여 장치에 대 한 직접 메서드가 실패 합니다. 

## <a name="cause"></a>원인

장치가 온라인 상태이 고 여전히 오류가 발생 한 경우 직접 메서드 콜백이 장치에 등록 되지 않았기 때문일 수 있습니다.

## <a name="solution"></a>솔루션

직접 메서드 콜백에 대 한 장치를 올바르게 구성 하려면 [장치에서 직접 메서드 처리](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)를 참조 하세요.