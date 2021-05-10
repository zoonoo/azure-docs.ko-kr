---
title: 문제 해결 Azure IoT Hub 오류 409001 DeviceAlreadyExists
description: 오류 409001 DeviceAlreadyExists 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b075519fff2c7c328778d770ef68e9751922807b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061131"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

이 문서에서는 **409001 DeviceAlreadyExists** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

IoT Hub에서 디바이스를 등록하려고 하면 요청이 실패하고 **409001 DeviceAlreadyExists** 오류가 표시됩니다.

## <a name="cause"></a>원인

IoT 허브에는 이미 동일한 디바이스 ID를 가진 디바이스가 있습니다. 

## <a name="solution"></a>솔루션

다른 디바이스 ID를 사용하여 다시 시도해 보세요.