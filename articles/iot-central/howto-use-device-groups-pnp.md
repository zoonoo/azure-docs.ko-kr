---
title: Azure IoT Central 응용 프로그램에서 장치 그룹 사용 | Microsoft Docs
description: 운영자로 서 Azure IoT Central 응용 프로그램에서 장치 그룹을 사용 하는 방법을 설명 합니다.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 07f1df101442e8ae43b26cebc60c8452d475d0f3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879748"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Azure IoT Central 응용 프로그램에서 장치 그룹 사용 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

이 문서에서는 Azure IoT Central 응용 프로그램에서 장치 그룹을 사용 하는 방법을 설명 합니다.

장치 그룹은 지정 된 조건과 일치 하므로 함께 그룹화 되는 장치 목록입니다. 장치 그룹을 통해 장치를 더 작은 논리 그룹으로 그룹화 하 여 규모에 따라 장치를 관리 하 고, 시각화 하 고, 분석할 수 있습니다. 예를 들어 시애틀에 있는 모든 대공 공조 장치를 나열 하는 장치 그룹을 만들어 엔지니어가 담당 하는 장치를 찾을 수 있습니다. 이 문서에서는 장치 그룹을 만들고 구성 하는 방법을 보여 줍니다.

## <a name="create-a-device-group"></a>디바이스 그룹 만들기

장치 그룹을 만들려면 다음을 수행 합니다.

1. 왼쪽 탐색 메뉴에서 **장치 그룹** 을 선택 합니다.

1. **+ 새로 만들기**를 선택 합니다.

    ![새 장치 그룹](media/howto-use-device-groups-pnp/image1.png)

1. 장치 그룹에 전체 응용 프로그램에서 고유한 이름을 지정 합니다. 설명을 추가할 수도 있습니다. 장치 그룹은 단일 장치 템플릿의 장치만 포함할 수 있습니다. 이 그룹에 사용할 장치 템플릿을 선택 합니다.

1. 속성, 비교 연산자 및 값을 선택 하 여 장치 그룹에 대 한 장치를 식별 하는 쿼리를 만듭니다. **모든** 조건을 충족 하는 여러 쿼리 및 장치를 장치 그룹에 추가할 수 있습니다. 만든 장치 그룹은 응용 프로그램에 액세스할 수 있는 모든 사용자가 액세스할 수 있으므로 누구나 장치 그룹을 보거나 수정 하거나 삭제할 수 있습니다.

    ![장치 그룹 쿼리](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > 장치 그룹이 동적 쿼리입니다. 디바이스 목록을 볼 때마다 목록의 디바이스가 달라질 수 있습니다. 현재 쿼리 조건을 충족하는 디바이스에 따라 목록이 달라집니다.

1. **저장**을 선택합니다.

## <a name="analytics"></a>분석

장치 그룹의 분석은 왼쪽 탐색 메뉴의 기본 분석 탭과 동일 합니다. 분석에 대한 자세한 내용은 [분석을 만드는 방법](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 문서에서 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에서 장치 그룹을 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [원격 분석 규칙을 만드는 방법](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
