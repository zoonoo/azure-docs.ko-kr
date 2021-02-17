---
title: IoT 에이전트 기반 솔루션을 위해 Defender에 온보드
description: Azure IoT Hub에서 Defender for IoT 보안 서비스를 등록하고 활성화하는 방법에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809136"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>IoT 에이전트 기반 솔루션을 위해 Defender에 온보드

이 문서에서는 기존 IoT Hub에서 Defender for IoT 서비스를 사용하도록 설정하는 방법을 설명합니다. 현재 IoT Hub가 없는 경우 시작하려면 [Azure Portal을 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-through-portal.md)를 참조하세요.

Defender for IoT의 IoT Hub를 통해 IoT 보안을 관리할 수 있습니다. IoT Hub에 있는 관리 포털에서 다음을 수행할 수 있습니다. 

- IoT Hub 보안을 관리합니다.

- IoT Hub 원격 분석에 기반하여 에이전트를 설치하지 않고 IoT 디바이스의 보안 기본 관리 

- 마이크로 에이전트 기반의 IoT 디바이스의 보안에 대한 고급 관리

> [!NOTE]
> Defender for IoT는 현재 표준 계층 IoT Hub만 지원합니다.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>IoT Hub에서 Defender for IoT에 온보딩

모든 새 IoT 허브의 경우 Defender for IoT는 기본적으로 **On** 으로 설정됩니다. IoT Hub 만들기 프로세스 중에 Defender for IoT가 **On** 으로 전환되었는지 확인할 수 있습니다.

설정/해제가 **On** 으로 설정되었는지 확인하려면 다음을 수행합니다.

1. Azure Portal로 이동합니다.

1. Azure 서비스 목록에서 **IoT Hub** 를 선택합니다.

1. **만들기** 를 선택합니다.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="상단 도구 모음에서 만들기 단추를 선택합니다." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. **관리** 탭을 선택하고, **Defender for IoT** 설정/해제가 **On** 으로 설정되었는지 확인합니다.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Defender for IoT 설정/해제가 켜짐으로 설정되었는지 확인합니다.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>기존 IoT Hub에 Defender for IoT 온보딩

디바이스 ID 관리, 디바이스-클라우드 및 클라우드-디바이스 통신 패턴을 모니터링하고 다음을 수행하여 서비스를 시작할 수 있습니다. 

1. IoT Hub로 이동합니다. 

1.  **보안 개요** 메뉴를 선택합니다. 

1. IoT 솔루션 보안을 클릭하고 온보딩 양식을 완료합니다. 


## <a name="next-steps"></a>다음 단계

솔루션을 구성하려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Defender IoT 마이크로 에이전트 모듈 쌍 생성(미리 보기)](quickstart-create-micro-agent-module-twin.md)
