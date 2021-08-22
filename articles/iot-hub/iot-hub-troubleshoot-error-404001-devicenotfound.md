---
title: Azure IoT Hub 오류 404001 DeviceNotFound 문제 해결
description: 오류 404001 DeviceNotFound를 해결하는 방법 이해
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: 127f4546d674eb425440a8e0f395ca8ff0a6b6f4
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515532"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

이 문서에서는 **404001 DeviceNotFound** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

C2D 메시지, 트윈 업데이트 또는 직접 메서드와 같은 C2D(클라우드-디바이스) 통신 중에 **404001 DeviceNotFound** 오류로 인해 작업이 실패합니다.

## <a name="cause"></a>원인

IoT Hub에서 디바이스를 찾을 수 없어서 작업에 실패했습니다. 디바이스가 등록되지 않았거나 비활성화되었습니다.

## <a name="solution"></a>해결 방법

사용한 디바이스 ID를 등록한 후, 다시 시도하세요.