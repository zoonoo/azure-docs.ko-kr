---
title: 문제 해결 Azure IoT Hub 오류 409001 DeviceAlreadyExists
description: 409001 오류를 해결 하는 방법 이해 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960790"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

이 문서에서는 **409001 DeviceAlreadyExists** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

IoT Hub에서 장치를 등록 하려고 하면 **409001 DeviceAlreadyExists**오류가 발생 하 여 요청이 실패 합니다.

## <a name="cause"></a>원인

IoT hub에 장치 ID가 같은 장치가 이미 있습니다. 

## <a name="solution"></a>솔루션

다른 장치 ID를 사용 하 고 다시 시도 하세요.