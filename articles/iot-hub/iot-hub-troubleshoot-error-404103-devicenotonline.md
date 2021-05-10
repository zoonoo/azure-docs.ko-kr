---
title: 문제 해결 Azure IoT Hub 오류 404103 DeviceNotOnline
description: 오류 404103 DeviceNotOnline을 해결하는 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: ed4341c1c8df588bf735bdc9c531c2165514d610
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061267"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

이 문서에서는 **404103 DeviceNotOnline** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

디바이스가 온라인 상태인 경우에도 디바이스에 대한 직접 메서드가 실패하고 **404103 DeviceNotOnline** 오류가 표시됩니다. 

## <a name="cause"></a>원인

디바이스가 온라인 상태이지만 여전히 오류가 발생하는 경우 디바이스에 직접 메서드 콜백이 등록되어 있지 않기 때문일 수 있습니다.

## <a name="solution"></a>솔루션

직접 메서드 콜백에 대한 디바이스를 올바르게 구성하려면 [디바이스에서 직접 메서드 처리](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)를 참조하세요.