---
title: Azure IoT Hub 오류 404103 DeviceNotOnline 문제 해결
description: 오류 404103 DeviceNotOnline을 수정하는 방법을 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960816"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

이 문서에서는 **404103 DeviceNotOnline** 오류의 원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

장치가 온라인 상태가 되어도 **오류 404103 DeviceNotOnline에** 대한 직접 메서드가 실패합니다. 

## <a name="cause"></a>원인

장치가 온라인 상태입니다 여전히 오류가 발생하더라도 직접 메서드 콜백이 장치에 등록되지 않기 때문일 수 있습니다.

## <a name="solution"></a>해결 방법

직접 메서드 콜백에 맞게 장치를 올바르게 구성하려면 [장치에서 직접 메서드 처리를](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)참조하십시오.