---
title: Azure IoT Central에서 디바이스 모니터링 | Microsoft Docs
description: 운영자로서 Azure IoT Central 애플리케이션을 사용하여 장치를 모니터링합니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9480565643887b5a9a4d644ba3173b365eaea29c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767776"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>자습서: Azure IoT Central을 사용하여 디바이스 모니터링

이 자습서에서는 운영자로서 Microsoft Azure IoT Central 애플리케이션을 사용하여 장치를 모니터링하고 설정을 변경하는 방법을 알려줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 알림 수신
> * 문제 조사
> * 문제 해결

## <a name="prerequisites"></a>필수 조건

시작하기 전에 작성기는 세 가지 작성기 자습서를 완료하여 Azure IoT Central 애플리케이션을 만들어야 합니다.

* [새 디바이스 유형 정의](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [디바이스에 대한 규칙 및 작업 구성](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [연산자의 뷰 사용자 지정](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>알림 수신

Azure IoT Central은 디바이스에 대한 알림을 이메일 메시지로 보냅니다. 작성기는 연결된 공조 디바이스에서 온도가 임계값을 초과하는 경우 알림을 보내기 위해 규칙을 추가했습니다. 작성기가 알림을 수신하도록 선택한 계정에 전송되는 이메일을 확인합니다.

[디바이스에 대한 규칙 및 작업 구성](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) 자습서의 끝에 수신한 이메일 메시지를 엽니다. 이메일에서 **디바이스를 열려면 여기를 클릭**을 선택합니다.

![경고 알림 이메일](media/tutorial-monitor-devices-experimental/email.png)

이전 자습서에서 만든 디바이스를 시뮬레이션한 **연결된 공조 디바이스-1**에 대한 **디바이스** 페이지가 브라우저에서 열립니다.

![알림 이메일 메시지를 트리거한 디바이스](media/tutorial-monitor-devices-experimental/sourcedevice.png)

## <a name="investigate-an-issue"></a>문제 조사

운영자로서 **측정**, **설정**, **속성**, **규칙** 및 **대시보드** 페이지에서 디바이스에 대한 정보를 볼 수 있습니다. 작성기는 연결된 공조 디바이스에 대한 중요한 정보를 표시하기 위해 **대시보드**를 사용자 지정했습니다.

디바이스에 대한 정보를 보려면 **대시보드** 보기를 선택합니다.

![디바이스 대시보드](media/tutorial-monitor-devices-experimental/initial_screen.png)

대시보드의 차트에는 디바이스 온도 플롯이 표시됩니다. **대상 온도 설정** 타일에서 디바이스에 대한 현재 대상 온도를 볼 수 있습니다. 대상 온도가 너무 높다고 판단합니다.

## <a name="remediate-an-issue"></a>문제 해결

디바이스의 대상 온도를 변경하려면 **설정** 페이지를 사용하세요.

1. **설정**을 선택합니다. **온도 설정**을 75로 변경합니다. **업데이트**를 선택하여 새 대상 온도를 디바이스로 전송합니다. 디바이스가 설정 변경을 확인하면 설정 상태가 **동기화됨**으로 변경됩니다.

    ![업데이트 설정](media/tutorial-monitor-devices-experimental/change_settings.png)

2. **대시보드**를 선택하여 새 설정 값을 확인합니다.

    ![업데이트된 디바이스 대시보드](media/tutorial-monitor-devices-experimental/new_settings.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="nextstepaction"]
> * 알림 수신
> * 문제 조사
> * 문제 해결

이제 디바이스를 모니터링했으므로 제안하는 다음 단계는 [디바이스 추가](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)하는 것입니다.