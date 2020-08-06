---
title: Azure Portal을 사용하여 직접 메서드를 호출하는 방법
description: 이 문서는 직접 메서드를 호출하는 Azure Portal을 사용하는 개요입니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 9d784e1697dfbcbfec509c1a51c9b832b533c97b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830753"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Azure Portal을 사용하여 직접 메서드를 호출하는 방법

IoT Hub를 사용하면 클라우드의 에지 디바이스에서 [직접 메서드](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules)를 호출할 수 있습니다. LVA(Live Video Analytics on IoT Edge) 모듈은 라이브 비디오를 분석하기 위해 여러 워크플로를 정의하고, 배포하고, 인스턴스화하는 데 사용할 수 있는 여러 [직접 메서드](./direct-methods.md)를 제공합니다.

이 문서에서는 Azure Portal을 통해 IoT Edge 모듈에 대해 Live Video Analytics에서 직접 메서드 호출을 호출하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 에지 디바이스에서 실행 중인 IoT Edge 모듈의 Live Video Analytics이 있으며, 이는 [빠른 시작: Live Video Analytics on IoT Edge](./get-started-detect-motion-emit-events-quickstart.md)에 설명된 메서드를 사용하거나 [포털](./deploy-iot-edge-device.md)을 사용합니다.

* [Live Video Analytics](./overview.md) 및 [미디어 그래프 개념](./media-graph-concept.md)를 이해하고 있습니다.

## <a name="invoking-direct-methods-via-azure-portal"></a>Azure Portal을 통해 직접 메서드 호출

LVA 모듈에 의해 노출된 각 [직접 메서드](./direct-methods.md)는 Azure Portal을 통해 호출할 수 있습니다. 아래 단계에서는 하나의 직접 메서드에 대한 세부 정보를 제공합니다. 비슷한 단계를 사용하여 다른 직접 메서드를 호출할 수 있습니다. 그러나 각 직접 메서드에는 특정 JSON 본문이 필요합니다.

`GraphTopologyList` 메서드 호출을 사용하여 IoT Edge 모듈의 Live Video Analytics에 현재 배포된 모든 그래프 토폴로지의 목록을 검색합니다. 다음 단계를 사용하여 이 직접 메서드를 호출합니다.

1. Azure Portal에 로그인
1. 포털 홈페이지에서 관련 리소스 그룹을 찾아 IoT Hub을 찾거나 IoT Hub를 알고 있는 경우 선택합니다.
    ![포털 홈 페이지의 리소스 그룹](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. IoT Hub 페이지에서 자동 디바이스 관리 아래에 있는 IoT Edge를 선택하여 여러 디바이스 ID를 나열합니다. 디바이스에서 실행되는 모듈을 나열하려면 관련 디바이스 ID를 선택합니다.
    ![IoT Hub 이름](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. IoT Edge 모듈에서 Live Video Analytics를 선택하여 구성 페이지를 표시합니다.<br><br>
    ![IoT Edge 모듈에서 Live Video Analytics를 선택하여 구성 페이지 표시](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. 직접 메서드 메뉴 옵션을 선택합니다. <br><br>
    ![직접 메서드 메뉴 옵션 클릭](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > 현재 페이지에서 볼 수 있듯이 연결 문자열 섹션에 값을 추가해야 할 수도 있습니다. 설정 이름 섹션에서 아무것도 숨기거나 변경할 필요가 없습니다. 공개하는 것도 괜찮습니다.

1. **메서드 이름** 필드에 *GraphTopologyList*를 입력합니다.
1. **페이로드** 필드에 아래 JSON을 복사하여 붙여넣습니다.
    ```json
    {
    "@apiVersion":
    }
    ```
1. 페이지 위쪽의 **메서드 호출** 단추를 선택합니다.<br><br>
    ![메서드 호출 단추](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. **결과** 영역에 상태 200 메시지가 표시됩니다.<br><br>
    ![연결 시간 제한](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>응답

| 조건             | 상태 코드 | 자세한 오류 코드 |
|-----------------------|-------------|---------------------|
| Success               | 200         | 해당 없음                 |
| 일반 사용자 오류   | 400 범위   |                     |
| 일반 서버 오류 | 500 범위   |                     |

## <a name="next-steps"></a>다음 단계

직접 [메서드](./direct-methods.md) 페이지에서 더 많은 직접 메서드를 찾을 수 있습니다.

> [!NOTE]
> 그래프 인스턴스는 특정 토폴로지를 인스턴스화하기 때문에 그래프 인스턴스를 만들기 전에 올바른 토폴로지가 설정되었는지 확인하세요.

[빠른 시작: 모션 내보내기 이벤트 감지](./get-started-detect-motion-emit-events-quickstart.md)는 정확한 직접 메서드 호출 순서를 이해하기 위한 좋은 참고 자료입니다.