---
title: 문제 해결 Azure IoT Hub 오류 404001 DeviceNotFound
description: 404001 오류를 해결 하는 방법 이해 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960829"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

이 문서에서는 **404001 DeviceNotFound** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

C2D 메시지, 쌍 업데이트 또는 직접 메서드와 같은 C2D (클라우드-장치) 통신 중에는 오류 **404001 DeviceNotFound**와 함께 작업이 실패 합니다.

## <a name="cause"></a>원인

IoT Hub에서 장치를 찾을 수 없어서 작업에 실패 했습니다. 장치가 등록 되지 않았거나 사용 하지 않도록 설정 되어 있습니다.

## <a name="solution"></a>솔루션

사용한 장치 ID를 등록 한 후 다시 시도 하세요.