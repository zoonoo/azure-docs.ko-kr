---
title: Azure IoT Central에서 디바이스 모니터링 | Microsoft Docs
description: 운영자로서 Azure IoT Central 애플리케이션을 사용하여 디바이스를 모니터링합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878784"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>자습서: Azure IoT Central을 사용하여 디바이스 모니터링(미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

이 자습서에서는 운영자로서 Microsoft Azure IoT Central 애플리케이션을 사용하여 디바이스를 모니터링하고 설정을 변경하는 방법을 알려줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 알림 수신
> * 문제 조사
> * 문제 해결

## <a name="prerequisites"></a>필수 조건

시작하기 전에 작성기는 세 가지 작성기 자습서를 완료하여 Azure IoT Central 애플리케이션을 만들어야 합니다.

* [새 디바이스 유형 정의](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [디바이스 추가](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [디바이스에 대한 규칙 및 작업 구성](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>알림 수신

Azure IoT Central은 디바이스에 대한 알림을 이메일 메시지로 보냅니다. 작성기는 연결된 환경 센서 디바이스에서 온도가 임계값을 초과하는 경우 알림을 보내기 위해 규칙을 추가했습니다. 작성기가 알림을 수신하도록 선택한 계정에 전송되는 이메일을 확인합니다.

[디바이스에 대한 규칙 및 작업 구성](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 자습서의 끝에 수신한 이메일 메시지를 엽니다. 이메일에서 디바이스에 대한 링크를 선택합니다.

![경고 알림 이메일](media/tutorial-monitor-devices-pnp/email.png)

이전 자습서에서 만든 디바이스를 시뮬레이션한 환경 센서에 대한 **대시보드** 보기가 브라우저에서 열립니다.

![알림 이메일 메시지를 트리거한 디바이스](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>문제 조사

운영자로서 **대시보드**, **환경 센서 속성** 및 **명령** 페이지에서 디바이스에 대한 정보를 볼 수 있습니다. 빌더는 **대시보드** 및 **환경 센서 속성** 페이지를 사용자 지정하여 연결된 환경 센서 디바이스에 대한 중요한 정보를 표시합니다.

디바이스에 대한 정보를 보려면 **대시보드** 보기를 선택합니다.

대시보드의 차트에는 디바이스 온도 플롯이 표시됩니다. **디바이스 속성** 타일에서 디바이스에 대한 현재 대상 온도를 볼 수 있습니다. 대상 온도가 너무 높다고 판단합니다.

## <a name="remediate-an-issue"></a>문제 해결

디바이스를 변경하려면 **환경 센서 속성** 페이지를 사용합니다.

1. **환경 센서 속성**을 선택합니다. **밝기 수준** 10으로 변경합니다. **저장**을 선택하여 디바이스를 업데이트합니다. 디바이스가 설정 변경을 확인하면 속성의 상태가 **동기화됨**으로 변경됩니다.

    ![업데이트 설정](media/tutorial-monitor-devices-pnp/change-settings.png)

2. **대시보드**를 선택하여 새 설정 값을 확인합니다.

    ![업데이트된 디바이스 대시보드](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

* 알림 수신
* 문제 조사
* 문제 해결

이제 디바이스를 모니터링했으므로 제안하는 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [디바이스에 대한 규칙 및 작업을 구성합니다](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).