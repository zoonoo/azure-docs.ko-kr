---
title: Azure IoT Central 질문과 대답 | Microsoft Docs
description: Azure IoT Central 질문과 대답(FAQ)
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796043"
---
# <a name="frequently-asked-questions-for-iot-central"></a>IoT Central에 대한 질문과 대답

**디바이스가 내 IoT Central 애플리케이션에 연결하지 않는 경우 자격 증명 문제를 어떻게 확인할까요?**

[디바이스의 데이터가 Azure IoT Central에 표시되지 않는 이유 문제 해결](troubleshoot-connection.md)에는 디바이스의 연결 문제를 진단하는 단계가 포함되어 있습니다.

**고객 지원팀에 티켓을 어떻게 제출하나요?**

지원이 필요한 경우, [Azure 지원 티켓](https://portal.azure.com/#create/Microsoft.Support)을 제출할 수 있습니다.

기타 지원 옵션을 비롯한 자세한 내용은 [Azure IoT 지원 및 도움말 옵션](../../iot-fundamentals/iot-support-help.md)을 참조하세요.

**디바이스의 차단을 어떻게 해제할까요?**

디바이스가 차단되면 데이터를 IoT Central 애플리케이션에 보낼 수 없습니다. 차단된 디바이스의 상태는 애플리케이션의 **디바이스** 페이지에서 **차단** 으로 설정됩니다. 운영자가 데이터 보내기를 다시 시작하려면 먼저 디바이스를 차단 해제해야 합니다.

:::image type="content" source="media/howto-faq/blocked.png" alt-text="차단된 디바이스를 보여 주는 스크린샷":::

운영자가 디바이스의 차단을 해제하면 상태가 이전 값인 **등록됨** 또는 **프로비전됨** 으로 돌아갑니다.

**디바이스를 승인하려면 어떻게 해야 할까요??**

**디바이스** 페이지에서 디바이스 상태가 **승인을 기다리는 중** 이면 **자동 승인** 옵션이 사용하지 않도록 설정된 것입니다.

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="자동 승인 옵션이 사용하지 않도록 설정된 애플리케이션을 보여 주는 스크린샷.":::

운영자는 데이터 보내기를 시작하기 전에 디바이스를 명시적으로 승인해야 합니다. **디바이스** 페이지에서 수동으로 등록되지 않았지만 유효한 자격 증명을 사용하여 연결된 디바이스의 디바이스 상태는 **승인을 기다리는 중** 입니다. 운영자는 **승인** 단추를 사용하여 **디바이스** 페이지에서 이러한 디바이스를 승인할 수 있습니다.

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="디바이스를 승인하는 방법을 보여 주는 스크린샷":::

**디바이스를 디바이스 템플릿과 어떻게 연결하나요?**

디바이스 상태가 **연결 안 됨** 이면 IoT Central에 연결하는 디바이스에 연결된 디바이스 템플릿이 없음을 의미입니다. 이 상황은 일반적으로 다음 시나리오에서 발생합니다.

- 디바이스 템플릿을 지정하지 않고 **디바이스** 페이지에서 **가져오기** 를 사용하여 디바이스 세트가 추가됩니다.
- 디바이스 템플릿을 지정하지 않고 **디바이스** 페이지에서 디바이스가 수동으로 등록되었습니다. 그런 다음, 디바이스가 유효한 자격 증명을 사용하여 연결되었습니다.  

운영자는 **마이그레이션** 단추를 사용하여 **디바이스** 페이지에서 디바이스를 디바이스 템플릿에 연결할 수 있습니다. 자세히 알아보려면 [Azure IoT Central 애플리케이션에서 디바이스 관리 > 템플릿으로 디바이스 마이그레이션](howto-manage-devices.md)을 참조하세요.

**IoT Hub에 대한 자세한 내용은 어디서 확인할 수 있나요?**

Azure IoT Central은 디바이스 연결을 가능하게 하는 클라우드 게이트웨이로 Azure IoT Hub를 사용합니다. IoT Hub의 기능은 다음과 같습니다.

- 클라우드에서 대규모 데이터 수집.
- 디바이스 관리.
- 디바이스 연결 보안.

IoT Hub에 대한 자세한 내용은 [Azure IoT Hub](../../iot-hub/index.yml)를 참조하세요.

**DPS(Device Provisioning Service)에 대한 자세한 내용은 어디서 확인할 수 있나요?**

Azure IoT Central은 DPS를 사용하여 디바이스가 애플리케이션에 연결할 수 있도록 합니다. 디바이스를 IoT Central에 연결하는 데 DPS가 수행하는 역할에 대한 자세한 내용은 [Azure IoT Central에 연결](concepts-get-connected.md)을 참조하세요. DPS에 대한 자세한 내용은 [Azure IoT Hub Device Provisioning Service로 디바이스 프로비전](../../iot-dps/about-iot-dps.md)을 참조하세요.