---
title: Azure IoT Central에서 규칙 및 작업 구성 | Microsoft Docs
description: 이 자습서에서는 Azure IoT Central 애플리케이션에서 원격 분석 기반 규칙 및 작업을 구성하는 방법을 알려줍니다.
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d8a5ca6285624720e23a4986917ab5e715f6ebfa
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768016"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>자습서: Azure IoT Central에서 디바이스에 대한 규칙 및 작업 구성(새 UI 디자인)

*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*

이 자습서에서는 연결된 공조 디바이스의 온도가 90&deg; F를 초과하는 경우 이메일을 보내는 규칙을 만듭니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 원격 분석 기반 규칙 만들기
> * 작업 추가

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>필수 조건

시작하기 전에 [애플리케이션에서 새 장치 유형 정의](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) 자습서를 완료해야 작업할 **연결된 공조 장치** 템플릿을 만들 수 있습니다.

## <a name="create-a-telemetry-based-rule"></a>원격 분석 기반 규칙 만들기

1. 애플리케이션에 새 원격 분석 기반 규칙을 추가하려면 왼쪽 탐색 메뉴에서 **디바이스 템플릿**을 선택합니다.

    ![디바이스 템플릿 페이지](media/tutorial-configure-rules-experimental/templatespage1.png)

    이전 자습서에서 만든 **연결된 공조 장치(1.0.0)** 디바이스 템플릿이 보입니다.

2. 디바이스 템플릿을 사용자 지정하려면 이전 자습서에서 만든 **연결된 공조 장치** 템플릿을 클릭합니다.

3. **규칙** 보기에서 원격 분석 기반 규칙을 추가하려면 **규칙**을 선택하고, **+ 새 규칙**을 클릭한 다음, **원격 분석**을 선택합니다.

    ![규칙 보기](media/tutorial-configure-rules-experimental/newrule.png)

5. 규칙을 정의하려면 다음 표의 정보를 사용합니다.

    | 설정                                      | 값                             |
    | -------------------------------------------- | ------------------------------    |
    | Name                                         | 공조 장치 온도 경고 |
    | 이 템플릿의 모든 디바이스에 규칙 사용 | 다른                                |
    | 조건                                    | 온도가 90보다 큼    |
    | 집계                                  | 없음                              |

    ![온도 규칙 조건](media/tutorial-configure-rules-experimental/temperaturerule.png)

    그런 다음 **Save**를 클릭합니다.

## <a name="add-an-action"></a>작업 추가

규칙을 정의하는 경우 규칙 조건이 맞으면 실행할 작업을 정의합니다. 이 자습서에서는 이메일 알림을 보내는 작업을 사용하여 규칙을 만듭니다.

1. **작업**을 추가하려면 먼저 규칙을 **저장**하고, **원격 분석 규칙 구성** 패널을 아래로 스크롤합니다. **작업** 옆의 **+** 을 선택한 다음, **이메일**을 선택합니다.

    ![온도 규칙 작업](media/tutorial-configure-rules-experimental/addaction.png)

2. 작업을 정의하려면 다음 표의 정보를 사용합니다.

    | 설정   | 값                          |
    | --------- | ------------------------------ |
    | 받는 사람        | 이메일 주소             |
    | 메모     | 공조 장치 온도가 임계값을 초과했습니다. |

    > [!NOTE]
    > 이메일 알림을 받으려면 이메일 주소가 [애플리케이션의 사용자 ID](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)여야 하며, 사용자는 최소 한 번 이상 애플리케이션에 로그인했어야 합니다.

    ![온도 작업](media/tutorial-configure-rules-experimental/temperatureaction.png)

3. **저장**을 클릭합니다. **규칙** 페이지에 규칙이 나열됩니다.

## <a name="test-the-rule"></a>규칙 테스트

규칙은 저장 한 직후 발효됩니다. 규칙에서 정의된 조건이 충족되면 애플리케이션은 작업에서 지정된 이메일 주소로 메시지를 보냅니다.

![이메일 작업](media/tutorial-configure-rules-experimental/email.png)

> [!NOTE]
> 테스트를 완료한 후에는 받은 편지함에 경고가 수신되지 않도록 규칙을 해제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 원격 분석 기반 규칙 만들기
> * 작업 추가

임계값 기반 규칙을 정의했으니, 그 다음 단계는 [운영자 보기 사용자 지정](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)입니다.

Azure IoT Central에서 다양한 유형의 규칙 및 규칙 정의를 매개 변수화하는 방법에 대한 자세한 내용은 다음을 참조하세요.
* [원격 분석 규칙을 만들고 알림을 설정합니다](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [이벤트 규칙을 만들고 알림을 설정합니다](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

<!-- Next tutorials in the sequence -->
