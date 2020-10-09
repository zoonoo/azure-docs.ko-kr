---
title: Azure IoT Central 질문과 대답 | Microsoft Docs
description: Azure IoT Central FAQ (질문과 대답) 및 답변
author: dominicbetts
ms.author: dobett
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: f9c7412afcc191470902cc256586f9db21f8e78c
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91852146"
---
# <a name="frequently-asked-questions-for-iot-central"></a>IoT Central에 대 한 질문과 대답

**장치가 내 IoT Central 응용 프로그램에 연결 하지 않는 경우 자격 증명 문제를 확인 어떻게 할까요??**

장치의 [데이터가 Azure에 표시 되지 않는 문제를 해결](troubleshoot-connection.md) 하려면 장치에 대 한 연결 문제를 진단 하는 단계가 포함 IoT Central 합니다.

**어떻게 할까요? 파일에 고객이 지원 되는 티켓이 있나요?**

도움이 필요한 경우 [Azure 지원 티켓](https://portal.azure.com/#create/Microsoft.Support)을 만들 수 있습니다.

기타 지원 옵션을 비롯 한 자세한 내용은 [Azure IoT 지원 및 도움말 옵션](../../iot-fundamentals/iot-support-help.md)을 참조 하세요.

**장치의 차단을 해제 어떻게 할까요??**

장치가 차단 되 면 데이터를 IoT Central 응용 프로그램으로 보낼 수 없습니다. 차단 된 장치의 상태는 응용 프로그램의 **장치** 페이지에서 **차단 됨** 으로 설정 됩니다. 운영자는 데이터 보내기를 다시 시작 하기 전에 장치를 차단 해제 해야 합니다.

:::image type="content" source="media/howto-faq/blocked.png" alt-text="차단 된 장치를 보여 주는 스크린샷":::

운영자가 장치를 차단 해제 하면 상태가 이전 값으로 반환 되 고 **등록** 되거나 **프로 비전**됩니다.

**장치를 승인 어떻게 할까요??**

**장치 페이지에서** 장치 상태가 **승인 대기** 중인 경우 **자동 승인** 옵션이 사용 되지 않도록 설정 되어 있음을 의미 합니다.

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="차단 된 장치를 보여 주는 스크린샷":::

운영자는 데이터 보내기를 시작 하기 전에 장치를 명시적으로 승인 해야 합니다. **장치 페이지에** 수동으로 등록 되지 않았지만 유효한 자격 증명으로 연결 된 장치는 **승인 대기 중인**장치 상태를 가집니다. 운영자는 **승인** 단추를 사용 하 여 **장치** 페이지에서 이러한 장치를 승인할 수 있습니다.

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="차단 된 장치를 보여 주는 스크린샷":::

**장치를 장치 템플릿과 연결 어떻게 할까요??**

장치 상태가 **연결 되지 않음 인 경우**IoT Central 연결 하는 장치에 연결 된 장치 템플릿이 없음을 의미 합니다. 이 상황은 일반적으로 다음과 같은 시나리오에서 발생 합니다.

- 장치 템플릿을 지정 하지 않고 **장치 페이지에서** **가져오기** 를 사용 하 여 일련의 장치를 추가 합니다.
- 장치 템플릿을 지정 하지 않고 **장치 페이지에서** 장치를 수동으로 등록 했습니다. 그런 다음 장치는 유효한 자격 증명으로 연결 됩니다.  

운영자는 **마이그레이션** 단추를 사용 하 **여 장치를 장치 페이지에서** 장치 템플릿에 연결할 수 있습니다. 자세히 알아보려면 [Azure IoT Central 응용 프로그램에서 장치 관리를 참조 하 > 장치를 템플릿으로 마이그레이션](howto-manage-devices.md)을 참조 하세요.

**IoT Hub에 대 한 자세한 내용은 어디서 확인할 수 있나요?**

Azure IoT Central은 디바이스 연결을 가능하게 하는 클라우드 게이트웨이로 Azure IoT Hub를 사용합니다. IoT Hub의 기능은 다음과 같습니다.

- 클라우드에서 대규모 데이터 수집.
- 디바이스 관리.
- 디바이스 연결 보안.

IoT Hub에 대한 자세한 내용은 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/)를 참조하세요.

**DPS (장치 프로 비전 서비스)에 대 한 자세한 내용은 어디에서 확인할 수 있나요?**

Azure IoT Central는 DPS를 사용 하 여 장치에서 응용 프로그램에 연결할 수 있도록 합니다. IoT Central에 장치를 연결 하는 데 DPS 역할에 대해 자세히 알아보려면 [Azure IoT Central에 연결](concepts-get-connected.md)을 참조 하세요. DPS에 대해 자세히 알아보려면 [Azure IoT Hub 장치 프로 비전 서비스를 사용 하 여 장치 프로 비전](../../iot-dps/about-iot-dps.md)을 참조 하세요.