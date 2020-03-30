---
title: Azure IoT Hub 오류 409001 장치이미 존재
description: 오류 409001 장치를 수정하는 방법 이해이미 존재
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960790"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

이 문서에서는 **409001 DeviceAlreadyExists** 오류의 원인과 해결에 대해 설명합니다.

## <a name="symptoms"></a>증상

IoT Hub에서 장치를 등록하려고 할 때 **409001 DeviceAlreadyExists**오류로 요청이 실패합니다.

## <a name="cause"></a>원인

IoT 허브에 이미 동일한 장치 ID를 가진 장치가 있습니다. 

## <a name="solution"></a>해결 방법

다른 장치 ID를 사용하고 다시 시도하십시오.