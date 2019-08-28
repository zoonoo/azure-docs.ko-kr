---
title: Azure IoT Central에서 규칙 및 작업 구성 | Microsoft Docs
description: 이 자습서에서는 Azure IoT Central 애플리케이션에서 원격 분석 기반 규칙 및 작업을 구성하는 방법을 알려줍니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878834"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>자습서: Azure IoT Central에서 디바이스에 대한 규칙 및 작업 구성(미리 보기 기능)

*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

이 자습서에서는 환경 센서 디바이스의 온도가 90&deg; F를 초과하는 경우 이메일을 보내는 규칙을 만듭니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 원격 분석 기반 규칙 만들기
> * 작업 추가

## <a name="prerequisites"></a>필수 조건

시작하기 전에 [애플리케이션에서 새 디바이스 유형 정의](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 자습서를 완료해야 작업할 **환경 센서** 디바이스 템플릿을 만들 수 있습니다.

## <a name="create-a-telemetry-based-rule"></a>원격 분석 기반 규칙 만들기

1. 애플리케이션에 새 원격 분석 기반 규칙을 추가하려면 왼쪽 탐색 메뉴에서 **규칙**을 선택합니다.

1. 새 규칙을 만들려면 **+ 새로 만들기**를 선택합니다. 그런 다음, **원격 분석**을 선택합니다.

1. **환경 온도**를 규칙 이름으로 입력합니다.

1. **범위** 섹션에서 **환경 센서**를 디바이스 템플릿으로 선택합니다. 이 규칙이 적용되는 디바이스 범위입니다. **+ 필터**를 사용하여 더 많은 필터 조건을 추가할 수 있습니다.

1. **조건** 섹션에서 규칙을 트리거하는 항목을 정의합니다. 다음 정보를 사용하여 온도 원격 분석을 기반으로 하는 조건을 정의합니다.

    | 필드                                        | 값                             |
    | -------------------------------------------- | ------------------------------    |
    | 측정                                  | 온도                       |
    | 연산자                                     | 다음보다 큼                   |
    | 값                                        | 90                                |

    조건을 더 추가하려면 **+ 조건**을 선택합니다.

    ![규칙 조건 만들기](./media/tutorial-configure-rules-pnp/condition.png)

1. 규칙이 트리거될 때 실행할 작업을 추가하려면 **+ 작업**을 선택하고 **이메일**을 선택합니다.

1. 작업을 정의하려면 다음 표의 정보를 사용합니다.

    | 설정   | 값                                             |
    | --------- | ------------------------------------------------- |
    | 받는 사람        | 이메일 주소                                |
    | 메모     | 환경 온도가 임계값을 초과했습니다. |

    > [!NOTE]
    > 이메일 알림을 받으려면 이메일 주소가 [애플리케이션의 사용자 ID](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)여야 하며, 사용자는 최소 한 번 이상 애플리케이션에 로그인했어야 합니다.

    ![규칙 동작 만들기](./media/tutorial-configure-rules-pnp/action.png)

1. **저장**을 선택합니다. **규칙** 페이지에 규칙이 나열됩니다.

## <a name="test-the-rule"></a>규칙 테스트

규칙은 저장 한 직후 발효됩니다. 규칙에서 정의된 조건이 충족되면 애플리케이션은 작업에서 지정된 이메일 주소로 메시지를 보냅니다.

> [!NOTE]
> 테스트를 완료한 후에는 받은 편지함에 경고가 수신되지 않도록 규칙을 해제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

* 원격 분석 기반 규칙 만들기
* 작업 추가

임계값 기반 규칙을 정의했으니, 그 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [이벤트 규칙을 만들고 알림을 설정합니다](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
