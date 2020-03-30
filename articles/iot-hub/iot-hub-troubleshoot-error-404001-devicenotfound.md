---
title: Azure IoT Hub 오류 404001 장치Not
description: 오류 404001 장치를 수정하는 방법을 이해Not
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960829"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

이 문서에서는 **404001 DeviceNotFound** 오류의 원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

C2D 메시지, 쌍 업데이트 또는 직접 메서드와 같은 클라우드-장치(C2D) 통신 중에 오류 **404001 DeviceNotFound**오류로 작업이 실패합니다.

## <a name="cause"></a>원인

IoT Hub에서 장치를 찾을 수 없기 때문에 작업이 실패했습니다. 장치가 등록되지 않았거나 비활성화되지 않았습니다.

## <a name="solution"></a>해결 방법

사용한 장치 ID를 등록한 다음 다시 시도하십시오.