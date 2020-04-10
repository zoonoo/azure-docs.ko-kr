---
title: Azure IoT 중앙 응용 프로그램에서 장치 그룹 사용 | 마이크로 소프트 문서
description: 운영자는 장치 그룹을 사용하여 Azure IoT Central 응용 프로그램의 장치에서 원격 분석을 분석하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 58fc71ab05c34e8acd252e7a1984c55996d1b3a7
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999032"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>자습서: 장치 그룹을 사용하여 장치 원격 분석을 분석합니다.

이 문서에서는 운영자로서 장치 그룹을 사용하여 Azure IoT Central 응용 프로그램에서 장치 원격 분석을 분석하는 방법을 설명합니다.

장치 그룹은 지정된 일부 조건과 일치하기 때문에 함께 그룹화되는 장치 목록입니다. 장치 그룹을 사용하면 장치를 더 작은 논리 그룹으로 그룹화하여 대규모로 장치를 관리, 시각화 및 분석할 수 있습니다. 예를 들어 시애틀의 모든 에어컨 장치를 나열하는 장치 그룹을 만들어 기술자가 담당하는 장치를 찾을 수 있도록 할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 디바이스 그룹 만들기
> * 장치 그룹을 사용하여 장치 원격 분석

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md) 및 [IoT Central 애플리케이션에 시뮬레이션된 디바이스 추가](./quick-create-simulated-device.md) 빠른 시작을 완료하여 작업할 **MXChip IoT DevKit** 디바이스 템플릿을 만들어야 합니다.

## <a name="create-simulated-devices"></a>시뮬레이션된 장치 만들기

장치 그룹을 만들기 전에 이 자습서에서 사용할 **MXChip IoT DevKit** 장치 템플릿에서 시뮬레이션된 장치를 5개 이상 추가합니다.

![5개의 시뮬레이션 센서 장치](./media/tutorial-use-device-groups/simulated-devices.png)

시뮬레이션된 센서 장치 중 4개에 대해 **장치 관리** 보기를 사용하여 고객 이름을 *Contoso로*설정합니다.

![고객 이름을 Contoso로 설정](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>디바이스 그룹 만들기

장치 그룹을 만들려면 다음을 수행합니다.

1. 왼쪽 창에서 **장치 그룹을** 선택합니다.

1. 다음을 선택합니다. **+**

    ![새 장치 그룹](media/tutorial-use-device-groups/image1.png)

1. 장치 그룹에 *Contoso 장치*이름을 지정합니다. 설명을 추가할 수도 있습니다. 장치 그룹에는 단일 장치 템플릿의 장치만 포함될 수 있습니다. 이 그룹에 사용할 **MXChip IoT DevKit** 장치 템플릿을 선택합니다.

1. **Contoso에**속한 장치만 포함하도록 장치 그룹을 사용자 지정하려면 **+ 필터를**선택합니다. 고객 **이름** 속성, **등가** 비교 연산자 및 **Contoso를** 값으로 선택합니다. **모든** 필터 기준을 충족하는 여러 필터 및 장치를 장치 그룹에 추가할 수 있습니다. 사용자가 만든 장치 그룹은 누구나 응용 프로그램에 액세스할 수 있으므로 누구나 장치 그룹을 보거나 수정하거나 삭제할 수 있습니다.

    ![장치 그룹 쿼리](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > 장치 그룹은 동적 쿼리입니다. 디바이스 목록을 볼 때마다 목록의 디바이스가 달라질 수 있습니다. 현재 쿼리 조건을 충족하는 디바이스에 따라 목록이 달라집니다.

1. **저장을 선택합니다.**

> [!NOTE]
> Azure IoT Edge 장치의 경우 Azure IoT Edge 템플릿을 선택하여 장치 그룹을 만듭니다.

## <a name="analytics"></a>분석

장치 그룹과 함께 **애널리틱스를** 사용하여 그룹의 디바이스에서 원격 분석을 분석할 수 있습니다. 예를 들어 모든 Contoso 환경 센서에서 보고한 평균 온도를 플롯할 수 있습니다.

장치 그룹에 대한 원격 분석을 분석하려면 다음을 수행합니다.

1. 왼쪽 창에서 **애널리틱스를** 선택합니다.

1. 만든 **Contoso 장치** 그룹을 선택합니다. 그런 다음 **온도** 및 습도 원격 분석 유형을 모두 **추가합니다.**

    ![분석 만들기](./media/tutorial-use-device-groups/create-analysis.png)

    원격 분석 유형 옆에 있는 기어 휠 아이콘을 사용하여 집계 유형을 선택합니다. 기본값은 **평균**입니다. **분할을** 사용하여 집계 데이터가 표시되는 방식을 변경합니다. 예를 들어 장치 ID로 분할하면 **분석 을**선택하면 각 장치에 대한 플롯이 표시됩니다.

1. 평균 원격 분석 값을 보려면 **분석 옵션을** 선택합니다.

    ![분석 보기](./media/tutorial-use-device-groups/view-analysis.png)

    뷰를 사용자 지정하고 표시된 기간을 변경하고 데이터를 내보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 장치 그룹을 사용하는 방법을 배웠으니 다음 단계가 제안되었습니다.

> [!div class="nextstepaction"]
> [원격 분석 규칙을 만드는 방법](tutorial-create-telemetry-rules.md)
