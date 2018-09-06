---
title: 장치 시뮬레이션 솔루션의 시뮬레이션된 장치 동작 - Azure | Microsoft Docs
description: 이 문서에서는 JavaScript를 사용하여 장치 시뮬레이션 솔루션의 시뮬레이션된 장치 동작을 정의하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 43edbc653ddbd55aab5e722071de1f2cf4bcd1c4
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344519"
---
# <a name="implement-the-device-model-behavior"></a>장치 모델 동작 구현

[장치 모델 스키마 이해](iot-accelerators-device-simulation-device-schema.md) 문서에서는 시뮬레이션된 장치 모델을 정의하는 스키마에 대해 설명했습니다. 해당 문서에서는 시뮬레이션된 장치의 동작을 구현하는 다음 두 가지 유형의 JavaScript 파일을 참조했습니다.

- **상태**: 장치의 내부 상태를 업데이트하기 위해 고정 간격으로 실행되는 JavaScript 파일입니다.
- **메서드**: 솔루션이 장치에서 메서드를 호출할 때 실행되는 JavaScript 파일입니다.

이 문서에서는 다음 방법을 설명합니다.

>[!div class="checklist"]
> * 시뮬레이션된 장치의 상태 제어
> * 시뮬레이션된 장치가 원격 연결된 IoT Hub의 메서드 호출에 응답하는 방법 정의
> * 스크립트 디버그

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 고유한 사용자 지정 시뮬레이션된 장치 모델의 동작을 정의하는 방법을 설명했습니다. 이 문서에서는 다음을 수행하는 방법을 보여 주었습니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 시뮬레이션된 장치의 상태 제어
> * 시뮬레이션된 장치가 원격 연결된 IoT Hub의 메서드 호출에 응답하는 방법 정의
> * 스크립트 디버그

이제 시뮬레이션된 장치의 동작을 지정하는 방법을 배웠으므로 다음 단계에서는 [시뮬레이션된 장치를 만드는](iot-accelerators-device-simulation-create-simulated-device.md) 방법을 알아보는 것이 좋습니다.

장치 시뮬레이션 솔루션에 대한 자세한 개발자 정보는 [개발자 참조 가이드](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide)를 참조합니다.
