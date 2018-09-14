---
title: IT 원격 모니터링의 장치 시뮬레이션 - Azure | Microsoft Docs
description: 이 방법 가이드는 원격 모니터링 솔루션 가속기에서 장치 시뮬레이터를 사용하는 방법을 보여줍니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/16/2018
ms.topic: conceptual
ms.openlocfilehash: 451e31cdf1d2c0ee690a99551c8079fdea800c3d
ms.sourcegitcommit: e45b2aa85063d33853560ec4bc867f230c1c18ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43371416"
---
# <a name="create-and-test-a-new-simulated-device"></a>시뮬레이트된 새 장치 만들기 및 테스트

원격 모니터링 솔루션 가속기를 사용하여 시뮬레이트된 자체 장치를 정의할 수 있습니다. 이 문서에서는 시뮬레이트된 새 전구 장치를 정의한 후 로컬로 테스트하는 방법을 보여 줍니다. 솔루션 가속기에는 냉각기 및 트럭과 같은 시뮬레이트된 장치가 포함됩니다. 그러나 실제 장치를 배포하기 전에 IoT 솔루션을 테스트하기 위해 시뮬레이트된 자체 장치를 정의할 수 있습니다.

이 방법 가이드에서는 장치 시뮬레이션 마이크로 서비스를 사용자 지정하는 방법을 보여 줍니다. 이 마이크로 서비스는 원격 모니터링 솔루션 가속기의 일부입니다. 장치 시뮬레이션 기능을 표시하기 위해 이 방법 가이드에서는 Contoso IoT 응용 프로그램에서 두 가지 시나리오를 사용합니다.

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>다음 단계

이 가이드에서는 사용자 지정 시뮬레이트된 장치 유형을 만들고, 장치 시뮬레이션 마이크로 서비스를 로컬로 실행하여 테스트하는 방법을 알아보았습니다.

다음 단계로, 사용자 지정 시뮬레이트된 장치 유형을 [원격 모니터링 솔루션 가속기](iot-accelerators-remote-monitoring-deploy-simulated-device.md)에 배포하는 방법을 알아보는 것이 좋습니다.
