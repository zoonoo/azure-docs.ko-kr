---
title: 자습서 - Azure IoT Central 애플리케이션에서 디바이스 그룹 사용 | Microsoft Docs
description: 자습서 - 디바이스 그룹을 사용하여 Azure IoT Central 애플리케이션의 디바이스에서 원격 분석을 분석하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da7c1c0268f04b183ba48491bd5f0d0b01e15b41
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113224112"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>자습서: 디바이스 그룹을 사용하여 디바이스 원격 분석 수행

이 문서에서는 디바이스 그룹을 사용하여 Azure IoT Central 애플리케이션에서 디바이스 원격 분석을 분석하는 방법을 설명합니다.

디바이스 그룹은 지정된 특정 기준이 일치하기 때문에 함께 그룹화된 디바이스 목록입니다. 디바이스 그룹은 디바이스를 더 작은 논리 그룹으로 그룹화하여 디바이스를 대규모로 관리하고 시각화하고 분석하는 데 도움을 줍니다. 예를 들어 기술자가 자신의 책임 하에 있는 디바이스를 찾을 수 있도록 시애틀의 모든 공조 디바이스를 나열하는 디바이스 그룹을 만들 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 디바이스 그룹 만들기
> * 디바이스 그룹을 사용하여 디바이스 원격 분석 수행

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="add-and-customize-a-device-template"></a>디바이스 템플릿 추가 및 사용자 지정

디바이스 카탈로그에서 디바이스 템플릿을 추가합니다. 이 자습서에서는 **ESP32-Azure IoT Kit** 디바이스 템플릿을 사용합니다.

1. 새 디바이스 템플릿을 추가하려면 **디바이스 템플릿** 페이지에서 **+ 새로 만들기** 를 선택합니다.

1. **유형 선택** 페이지의 **미리 구성된 디바이스 템플릿 사용** 섹션에서 **ESP32-Azure IoT Kit** 타일을 찾을 때까지 아래로 스크롤합니다.

1. **ESP32-Azure IoT Kit** 타일, **다음: 검토** 를 차례로 선택합니다.

1. **검토** 페이지에서 **만들기** 를 선택합니다.

생성한 템플릿 이름은 **센서 컨트롤러** 입니다. 모델에는 **센서 조절기**, **SensorTemp** 및 **디바이스 정보 인터페이스** 와 같은 구성 요소가 포함되어 있습니다. 구성 요소는 ESP32 디바이스의 기능을 정의합니다. 기능에는 원격 분석, 속성 및 명령이 포함됩니다.

두 개의 클라우드 속성을 **센서 컨트롤러** 디바이스 템플릿에 추가합니다.

1. **클라우드 속성**, **+ 클라우드 속성 추가** 를 차례로 선택합니다. 다음 표의 정보를 사용하여 디바이스 템플릿에 두 가지 클라우드 속성을 추가합니다.

    | 표시 이름      | 의미 체계 유형 | 스키마 |
    | ----------------- | ------------- | ------ |
    | 마지막 서비스 날짜 | None          | Date   |
    | 고객 이름     | None          | String |

1. **저장** 을 선택하여 변경 내용을 저장합니다.

디바이스 템플릿에 새 양식을 추가하여 디바이스를 관리합니다.

1. **보기** 노드를 선택하고 **디바이스 및 클라우드 데이터 편집** 타일을 선택하여 새 보기를 추가합니다.

1. 양식 이름을 **디바이스 관리** 로 변경합니다.

1. **고객 이름** 및 **마지막 서비스 날짜** 클라우드 속성과 **대상 온도** 속성을 선택합니다. 그런 다음, **섹션 추가** 를 선택합니다.

1. **저장** 을 선택하여 새 양식을 저장합니다.

이제 디바이스 템플릿을 게시합니다.

## <a name="create-simulated-devices"></a>시뮬레이션된 디바이스 만들기

디바이스 그룹을 만들기 전에 이 자습서에서 사용할 **센서 컨트롤러** 디바이스 템플릿을 기반으로 시뮬레이션된 디바이스를 5개 이상 추가합니다.

:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="5개의 시뮬레이션된 센서 컨트롤러 디바이스를 보여주는 스크린샷":::

시뮬레이션된 센서 디바이스 중 4개에 대해 **디바이스 관리** 보기를 사용하여 고객 이름을 *Contoso* 로 설정하고 **저장** 을 선택합니다.

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="고객 이름 클라우드 속성을 설정하는 방법을 보여주는 스크린샷":::

## <a name="create-a-device-group"></a>디바이스 그룹 만들기

1. 왼쪽 창에서 **디바이스 그룹** 을 선택하여 디바이스 그룹 페이지로 이동합니다.

1. **+새로 만들기** 를 선택합니다.

1. 디바이스 그룹에 *Contoso 디바이스* 이름을 지정합니다. 설명을 추가할 수도 있습니다. 한 디바이스 그룹에는 단일 디바이스 템플릿의 디바이스만 포함될 수 있습니다. 이 그룹에 사용할 **센서 컨트롤러** 디바이스 템플릿을 선택합니다.

1. **Contoso** 에 속하는 디바이스만 포함하도록 디바이스 그룹을 사용자 지정하려면 **+ 필터** 를 선택합니다. **고객 이름** 속성을 선택하고 **Equals** 비교 연산자를 선택한 다음, 값으로 **Contoso** 를 선택합니다. 여러 필터를 추가할 수 있으며 **모든** 필터 조건을 충족하는 디바이스는 디바이스 그룹에 배치됩니다. 애플리케이션에 대한 액세스 권한을 가진 사람이라면 누구든지 생성된 디바이스 그룹에 액세스할 수 있으며, 따라서 누구든지 디바이스 그룹을 보고, 수정하고, 삭제할 수 있습니다.

    > [!TIP]
    > 디바이스 그룹은 동적 쿼리입니다. 디바이스 목록을 볼 때마다 목록의 디바이스가 달라질 수 있습니다. 현재 쿼리 조건을 충족하는 디바이스에 따라 목록이 달라집니다.

1. **저장** 을 선택합니다.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="디바이스 그룹 쿼리 구성을 보여주는 스크린샷":::

> [!NOTE]
> Azure IoT Edge 디바이스의 경우 Azure IoT Edge 템플릿을 선택하여 디바이스 그룹을 만듭니다.

## <a name="analytics"></a>분석

**Analytics** 를 디바이스 그룹과 함께 사용하여 그룹의 디바이스에서 원격 분석을 분석할 수 있습니다. 예를 들어 모든 Contoso 환경 센서가 보고하는 평균 온도를 그릴 수 있습니다.

디바이스 그룹에 대한 원격 분석을 분석하려면 다음을 수행합니다.

1. 왼쪽 창에서 **분석** 을 선택합니다.

1. 만든 **Contoso 디바이스** 디바이스 그룹을 선택합니다. 그런 다음, **온도** 와 **습도** 원격 분석 유형을 모두 추가합니다.

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="분석을 위해 선택된 원격 분석 유형을 보여주는 스크린샷":::

    원격 분석 유형 옆에 있는 줄임표 아이콘을 사용하여 집계 유형을 선택합니다. 기본값은 **평균** 입니다. **그룹 기준** 을 사용하여 집계 데이터가 표시되는 방식을 변경합니다. 예를 들어 디바이스 ID로 분할하는 경우 **분석** 을 선택하면 각 디바이스에 대한 플롯이 표시됩니다.

1. **분석** 을 선택하여 평균 원격 분석 값을 확인합니다.

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="모든 Contoso 디바이스에 대한 평균 값을 보여주는 스크린샷":::

    보기를 사용자 지정하고, 표시된 기간을 변경하고, 데이터를 CSV로 내보내거나 테이블로 볼 수 있습니다.

    :::image type="content" source="media/tutorial-use-device-groups/export-data.png" alt-text="Contoso 디바이스에 대한 데이터를 내보내는 방법을 보여주는 스크린샷":::

분석에 대한 자세한 정보를 알아보려면 [분석을 사용하여 디바이스 데이터를 분석하는 방법](howto-create-analytics.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 디바이스 그룹을 사용하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [원격 분석 규칙을 만드는 방법](tutorial-create-telemetry-rules.md)
