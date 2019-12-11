---
title: Azure IoT Central 응용 프로그램에서 장치 그룹 사용 | Microsoft Docs
description: 운영자로 서 장치 그룹을 사용 하 여 Azure IoT Central 응용 프로그램의 장치에서 원격 분석을 분석 하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 981046f9417876a68c215c6d01a63c7f0a634464
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976216"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>자습서: 장치 그룹을 사용 하 여 장치 원격 분석 분석 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 문서에서는 장치 그룹을 사용 하 여 Azure IoT Central 응용 프로그램에서 장치 원격 분석을 분석 하는 방법을 설명 합니다.

장치 그룹은 지정 된 조건과 일치 하므로 함께 그룹화 되는 장치 목록입니다. 장치 그룹을 통해 장치를 더 작은 논리 그룹으로 그룹화 하 여 규모에 따라 장치를 관리 하 고, 시각화 하 고, 분석할 수 있습니다. 예를 들어 시애틀에 있는 모든 대공 공조 장치를 나열 하는 장치 그룹을 만들어 엔지니어가 담당 하는 장치를 찾을 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 디바이스 그룹 만들기
> * 장치 그룹을 사용 하 여 장치 원격 분석 분석

## <a name="prerequisites"></a>전제 조건

시작하기 전에 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md) 및 [IoT Central 애플리케이션에 시뮬레이션된 디바이스 추가](./quick-create-pnp-device.md) 빠른 시작을 완료하여 작업할 **환경 센서** 디바이스 템플릿을 만들어야 합니다.

## <a name="create-simulated-devices"></a>시뮬레이션 된 장치 만들기

장치 그룹을 만들기 전에이 자습서에서 사용할 수 있도록 **환경 센서** 장치 템플릿에서 시뮬레이션 된 장치를 5 개 이상 추가 합니다.

![시뮬레이션 된 5 개의 환경 센서 장치](./media/tutorial-use-device-groups/simulated-devices.png)

네 가지 환경 센서 장치에 대해 **환경 센서 속성** 보기를 사용 하 여 고객 이름을 **Contoso**로 설정 합니다.

![고객 이름을 Contoso로 설정](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>디바이스 그룹 만들기

장치 그룹을 만들려면 다음을 수행 합니다.

1. 왼쪽 창에서 **장치 그룹** 을 선택 합니다.

1. **+새로 만들기**를 선택합니다.

    ![새 장치 그룹](media/tutorial-use-device-groups/image1.png)

1. 장치 그룹에 **Contoso 장치와**같은 이름을 지정 합니다. 설명을 추가할 수도 있습니다. 장치 그룹은 단일 장치 템플릿의 장치만 포함할 수 있습니다. 이 그룹에 사용할 **환경 센서** 장치 템플릿을 선택 합니다.

1. **Customer Name** 속성, **Equals** 비교 연산자 및 **contoso** 를 값으로 선택 하 여 장치 그룹에 대 한 **contoso** 에 속한 장치를 식별 하는 쿼리를 만듭니다. **모든** 조건을 충족 하는 여러 쿼리 및 장치를 장치 그룹에 추가할 수 있습니다. 만든 장치 그룹은 응용 프로그램에 액세스할 수 있는 모든 사용자가 액세스할 수 있으므로 누구나 장치 그룹을 보거나 수정 하거나 삭제할 수 있습니다.

    ![장치 그룹 쿼리](media/tutorial-use-device-groups/image2.png)

    > [!NOTE]
    > 장치 그룹이 동적 쿼리입니다. 디바이스 목록을 볼 때마다 목록의 디바이스가 달라질 수 있습니다. 현재 쿼리 조건을 충족하는 디바이스에 따라 목록이 달라집니다.

1. **저장**을 선택합니다.

> [!NOTE]
> Azure IoT Edge 장치에 대해 Azure IoT Edge 템플릿을 선택 하 여 장치 그룹을 만듭니다.

## <a name="analytics"></a>분석기능

장치 그룹에 **분석** 을 사용 하 여 그룹의 장치에서 원격 분석을 분석할 수 있습니다. 예를 들어 모든 Contoso 환경 센서가 보고 하는 평균 온도를 그릴 수 있습니다.

장치 그룹에 대 한 원격 분석을 분석 하려면:

1. 왼쪽 창에서 **분석** 을 선택 합니다.

1. 만든 **Contoso 장치** 장치 그룹을 선택 합니다. 그런 다음 **온도** 및 **습도** 원격 분석 유형을 추가 합니다.

    ![분석 만들기](./media/tutorial-use-device-groups/create-analysis.png)

    원격 분석 유형 옆의 기어 휠 아이콘을 사용 하 여 집계 유형을 선택 합니다. 기본값은 **Average**입니다. **Split by** 를 사용 하 여 집계 데이터가 표시 되는 방식을 변경 합니다. 예를 들어 장치 ID로 분할 하는 경우 **분석**을 선택 하면 각 장치에 대 한 플롯이 표시 됩니다.

1. **분석** 을 선택 하 여 평균 원격 분석 값을 확인 합니다.

    ![분석 보기](./media/tutorial-use-device-groups/view-analysis.png)

    뷰를 사용자 지정 하 고, 표시 된 기간을 변경 하 고, 데이터를 내보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에서 장치 그룹을 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [원격 분석 규칙을 만드는 방법](tutorial-create-telemetry-rules.md)
