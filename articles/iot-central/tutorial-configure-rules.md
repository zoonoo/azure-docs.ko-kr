---
title: Azure IoT Central에서 규칙 및 작업 구성 | Microsoft Docs
description: 이 자습서에서는 Azure IoT Central 애플리케이션에서 원격 분석 기반 규칙 및 작업을 구성하는 방법을 알려줍니다.
author: ankitscribbles
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7bdfa1659a0c34b3456ed3e52c72cafafc8be034
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993644"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>자습서: Azure IoT Central에서 디바이스에 대한 규칙 및 작업 구성

*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*

이 자습서에서는 연결된 공조 디바이스의 온도가 90&deg; F를 초과하는 경우 이메일을 보내는 규칙을 만듭니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 원격 분석 기반 규칙 만들기
> * 작업 추가

## <a name="prerequisites"></a>필수 조건

시작하기 전에 [응용 프로그램에서 새 디바이스 유형 정의](tutorial-define-device-type.md) 자습서를 완료해야 작업할 **연결된 공조 디바이스** 템플릿을 만들 수 있습니다.

## <a name="create-a-telemetry-based-rule"></a>원격 분석 기반 규칙 만들기

1. 애플리케이션에 새 원격 분석 기반 규칙을 추가하려면 왼쪽 탐색 메뉴에서 **Device Explorer**를 선택합니다.

    ![Device Explorer 페이지](media/tutorial-configure-rules/explorerpage1.png)

    이전 자습서에서 만든 **연결된 공조 디바이스(1.0.0)** 템플릿 및 **연결된 공조 디바이스-1**을 확인할 수 있습니다.

2. 연결된 공조 디바이스의 사용자 지정을 시작하려면 이전 자습서에서 만든 디바이스를 선택합니다.

    ![연결된 공조 장치 페이지](media/tutorial-configure-rules/builderdevicelist1.png)

3. **규칙** 보기에 규칙을 추가하려면 **규칙**을 선택한 후 **템플릿 편집**을 클릭합니다.

    ![규칙 보기](media/tutorial-configure-rules/builderedittemplate.png)

4. 임계값 기반 원격 분석 규칙을 만들려면 **새 규칙**, **원격 분석**을 차례로 클릭합니다.

    ![템플릿 편집](media/tutorial-configure-rules/buildernewrule.png)

5. 규칙을 정의하려면 다음 표의 정보를 사용합니다.

    | 설정                                      | 값                             |
    | -------------------------------------------- | ------------------------------    |
    | 이름                                         | 공조 장치 온도 경고 |
    | 이 템플릿의 모든 디바이스에 규칙 사용 | 다른                                |
    | 이 디바이스에서 규칙 사용                   | 다른                                |
    | 조건                                    | 온도가 90보다 큼    |
    | 집계                                  | 없음                              |

    ![온도 규칙 조건](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>작업 추가

규칙을 정의하는 경우 규칙 조건이 맞으면 실행할 작업을 정의합니다. 이 자습서에서는 규칙이 트리거한 알림으로 이메일을 보내는 작업을 추가합니다.

1. **작업**을 추가하려면 먼저 규칙을 **저장**하고, **원격 분석 규칙 구성** 패널에서 아래로 스크롤하여 **작업** 옆의 **+** 을 선택한 다음, **메일**을 선택합니다.

    ![온도 규칙 작업](media/tutorial-configure-rules/builderaddaction1.png)

2. 작업을 정의하려면 다음 표의 정보를 사용합니다.

    | 설정   | 값                          |
    | --------- | ------------------------------ |
    | 받는 사람        | 이메일 주소             |
    | 메모     | 공조 장치 온도가 임계값을 초과했습니다. |

    > [!NOTE]
    > 이메일 알림을 받으려면 이메일 주소가 [애플리케이션의 사용자 ID](howto-administer.md)여야 하며, 사용자는 최소 한 번 이상 애플리케이션에 로그인했어야 합니다.

    ![애플리케이션 작성기 온도 작업](media/tutorial-configure-rules/buildertemperatureaction.png)

3. **저장**을 선택합니다. **규칙** 페이지에 규칙이 나열됩니다.

    ![애플리케이션 작성기 규칙](media/tutorial-configure-rules/builderrules1.png)

4. **완료**를 선택하여 **템플릿 편집** 모드를 종료합니다.
 

## <a name="test-the-rule"></a>규칙 테스트

규칙은 저장 한 직후 발효됩니다. 규칙에서 정의된 조건이 충족되면 애플리케이션은 작업에서 지정된 이메일 주소로 메시지를 보냅니다.

![이메일 작업](media/tutorial-configure-rules/email.png)

> [!NOTE]
> 테스트를 완료한 뒤 받은 편지함에서 경고를 받지 않게 규칙을 해제합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 원격 분석 기반 규칙 만들기
> * 작업 추가

임계값 기반 규칙을 정의했으므로 제안된 다음 단계는 [연산자 뷰 사용자 지정](tutorial-customize-operator.md)입니다.

Azure IoT Central에서 다양한 유형의 규칙 및 규칙 정의를 매개 변수화하는 방법에 대한 자세한 내용은 다음을 참조하세요.
* [원격 분석 규칙을 만들고 알림을 설정합니다](howto-create-telemetry-rules.md).
* [이벤트 규칙을 만들고 알림을 설정합니다](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->
