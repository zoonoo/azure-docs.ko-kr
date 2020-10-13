---
title: 자습서 - Azure IoT Central 애플리케이션에서 디바이스 그룹 사용 | Microsoft Docs
description: 자습서 - 운영자로서, 디바이스 그룹을 사용하여 Azure IoT Central 애플리케이션의 디바이스에서 원격 분석을 분석하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 3192a9f121d4380a3e681747596fc91997662bf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967945"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>자습서: 디바이스 그룹을 사용하여 디바이스 원격 분석 수행

이 문서에서는 운영자로서 디바이스 그룹을 사용하여 Azure IoT Central 애플리케이션에서 디바이스 원격 분석을 분석하는 방법을 설명합니다.

디바이스 그룹은 지정된 특정 기준이 일치하기 때문에 함께 그룹화된 디바이스 목록입니다. 디바이스 그룹은 디바이스를 더 작은 논리 그룹으로 그룹화하여 디바이스를 대규모로 관리하고 시각화하고 분석하는 데 도움을 줍니다. 예를 들어 기술자가 자신의 책임 하에 있는 디바이스를 찾을 수 있도록 시애틀의 모든 공조 디바이스를 나열하는 디바이스 그룹을 만들 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 디바이스 그룹 만들기
> * 디바이스 그룹을 사용하여 디바이스 원격 분석 수행

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md) 및 [IoT Central 애플리케이션에 시뮬레이션된 디바이스 추가](./quick-create-simulated-device.md) 빠른 시작을 완료하여 작업할 **MXChip IoT DevKit** 디바이스 템플릿을 만들어야 합니다.

## <a name="create-simulated-devices"></a>시뮬레이션된 디바이스 만들기

디바이스 그룹을 만들기 전에 이 자습서에서 사용할 **MXChip IoT DevKit** 디바이스 템플릿에서 시뮬레이션된 디바이스를 5개 이상 추가합니다.

![5개의 시뮬레이션된 센서 디바이스](./media/tutorial-use-device-groups/simulated-devices.png)

시뮬레이션된 센서 디바이스 중 4개는 **디바이스 관리** 보기를 사용하여 *Contoso*에 대한 고객 이름을 설정합니다.

![고객 이름을 Contoso로 설정](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>디바이스 그룹 만들기

디바이스 그룹을 만들려면 다음을 수행합니다.

1. 왼쪽 창에서 **디바이스 그룹**을 선택합니다.

1. **+** 를 선택합니다.

    ![새 디바이스 그룹](media/tutorial-use-device-groups/image1.png)

1. 디바이스 그룹에 *Contoso 디바이스* 이름을 지정합니다. 설명을 추가할 수도 있습니다. 한 디바이스 그룹에는 단일 디바이스 템플릿의 디바이스만 포함될 수 있습니다. 이 그룹에 사용할 **MXChip IoT DevKit** 디바이스 템플릿을 선택합니다.

1. **Contoso**에 속하는 디바이스만 포함하도록 디바이스 그룹을 사용자 지정하려면 **+ 필터**를 선택합니다. **고객 이름** 속성을 선택하고 **Equals** 비교 연산자를 선택한 다음, 값으로 **Contoso**를 선택합니다. 여러 필터를 추가할 수 있으며 **모든** 필터 조건을 충족하는 디바이스는 디바이스 그룹에 배치됩니다. 애플리케이션에 대한 액세스 권한을 가진 사람이라면 누구든지 생성된 디바이스 그룹에 액세스할 수 있으며, 따라서 누구든지 디바이스 그룹을 보고, 수정하고, 삭제할 수 있습니다.

    ![디바이스 그룹 쿼리](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > 디바이스 그룹은 동적 쿼리입니다. 디바이스 목록을 볼 때마다 목록의 디바이스가 달라질 수 있습니다. 현재 쿼리 조건을 충족하는 디바이스에 따라 목록이 달라집니다.

1. **저장**을 선택합니다.

> [!NOTE]
> Azure IoT Edge 디바이스의 경우 Azure IoT Edge 템플릿을 선택하여 디바이스 그룹을 만듭니다.

## <a name="analytics"></a>분석

**Analytics**를 디바이스 그룹과 함께 사용하여 그룹의 디바이스에서 원격 분석을 분석할 수 있습니다. 예를 들어 모든 Contoso 환경 센서가 보고하는 평균 온도를 그릴 수 있습니다.

디바이스 그룹에 대한 원격 분석을 분석하려면 다음을 수행합니다.

1. 왼쪽 창에서 **분석**을 선택합니다.

1. 만든 **Contoso 디바이스** 디바이스 그룹을 선택합니다. 그런 다음, **온도**와 **습도** 원격 분석 유형을 모두 추가합니다.

    ![분석 만들기](./media/tutorial-use-device-groups/create-analysis.png)

    원격 분석 유형 옆의 기어 휠 아이콘을 사용하여 집계 유형을 선택합니다. 기본값은 **평균**입니다. **분할 기준**을 사용하여 집계 데이터가 표시되는 방식을 변경합니다. 예를 들어 디바이스 ID로 분할하는 경우 **분석**을 선택하면 각 디바이스에 대한 플롯이 표시됩니다.

1. **분석**을 선택하여 평균 원격 분석 값을 확인합니다.

    ![분석 보기](./media/tutorial-use-device-groups/view-analysis.png)

    뷰를 사용자 지정하고, 표시된 기간을 변경하고, 데이터를 내보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 디바이스 그룹을 사용하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [원격 분석 규칙을 만드는 방법](tutorial-create-telemetry-rules.md)
