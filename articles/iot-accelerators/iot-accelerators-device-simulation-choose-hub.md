---
title: 장치 시뮬레이션 솔루션에서 기존 IoT Hub 사용 - Azure | Microsoft Docs
description: 이 문서에서는 기존 IoT Hub를 사용하도록 장치 시뮬레이션 솔루션 가속기를 구성하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967514"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>장치 시뮬레이션 솔루션 가속기에서 기존 IoT Hub 사용

장치 시뮬레이션 솔루션 가속기를 프로비전할 경우 시뮬레이션에서 사용할 솔루션 가속기의 리소스 그룹에 IoT Hub를 배포하도록 선택할 수 있습니다.

선택적 IoT Hub를 배포하도록 선택하지 않은 경우 실행하는 시뮬레이션에 대해 고유한 허브를 사용해야 합니다. 선택적 IoT Hub를 배포하도록 선택한 경우 이 선택적 허브 또는 사용자 고유의 허브를 사용하도록 선택할 수 있습니다.

IoT Hub가 없는 경우에는 [Azure Portal](https://portal.azure.com)에서 항상 새로 만들 수 있습니다.

기존 IoT hub를 사용하려면 **iothubowner** 공유 액세스 정책에 대한 연결 문자열이 필요합니다. 이 연결 문자열은 [Azure Portal](https://portal.azure.com)에서 가져올 수 있습니다.

1. 포털의 허브 구성 페이지에서 **공유 액세스 정책**을 클릭합니다.
1. **iothubowner**를 클릭합니다.
1. 기본 또는 보조 연결 문자열을 복사합니다.

[![연결 문자열 가져오기](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

시뮬레이션을 구성할 때 복사한 연결 문자열을 사용합니다.

[![시뮬레이션 구성](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 시뮬레이션에서 기존 IoT hub를 사용하는 방법을 알아보았습니다. 다음으로, 시뮬레이션에 대한 [사용자 지정 장치 모델을 구성](iot-accelerators-device-simulation-custom-model.md)하는 방법을 알아볼 수 있습니다.
