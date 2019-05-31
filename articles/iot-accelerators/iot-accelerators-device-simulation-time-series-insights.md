---
title: Time Series Insights로 디바이스 시뮬레이션 원격 분석 시각화 - Azure | Microsoft Docs
description: 디바이스 시뮬레이션 솔루션 가속기에서 생성된 원격 분석을 탐색하고 분석하도록 Time Series Insights 환경을 구성하는 방법을 알아봅니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5d20adc11e0d679e12fd060e719593a50180db8e
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834809"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Time Series Insights를 사용하여 디바이스 시뮬레이션 솔루션 가속기에서 보낸 원격 분석 시각화

디바이스 시뮬레이션 솔루션 가속기를 사용하여 IoT 솔루션을 테스트하도록 시뮬레이션된 디바이스에서 원격 분석을 생성할 수 있습니다. 이 방법 가이드에서는 Time Series Insights 환경을 사용하여 시뮬레이션된 원격 분석을 시각화 및 분석하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 수행하려면 활성 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 방법 가이드의 단계에서는 Azure 구독에 디바이스 시뮬레이션 솔루션 가속기를 배포했다고 가정합니다. 솔루션 가속기를 배포하지 않은 경우 [클라우드 기반 디바이스 시뮬레이션 솔루션 배포 및 실행](quickstart-device-simulation-deploy.md) 빠른 시작의 단계를 수행합니다.

이 문서에서는 솔루션 가속기의 이름이 **contoso-simulation**이라고 가정합니다. **contoso-simulation**을 다음 단계를 완료할 때 솔루션 가속기의 이름으로 바꿉니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>소비자 그룹 만들기

Time Series Insights에 원격 분석을 스트리밍하도록 전용 소비자 그룹을 IoT 허브에 만들어야 합니다. Time Series Insights의 이벤트 원본에는 IoT Hub 소비자 그룹의 단독 사용이 있어야 합니다.

다음 단계는 Azure Cloud Shell에서 Azure CLI를 사용하여 소비자 그룹을 만듭니다.

1. IoT 허브는 디바이스 시뮬레이션 솔루션 가속기를 배포할 때 생성되는 몇 가지 리소스 중 하나입니다. 다음 명령을 실행하고 IoT 허브의 이름을 찾습니다. 솔루션 가속기의 이름을 사용해야 합니다.

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT 허브는 **Microsoft.Devices/IotHubs** 리소스 유형입니다.

1. **devicesimulationtsi**라는 소비자 그룹을 허브에 추가합니다. 다음 명령에서 허브 및 솔루션 가속기의 이름을 사용합니다.

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    이제 Azure Cloud Shell을 종료할 수 있습니다.

## <a name="create-a-new-time-series-insights-environment"></a>새로운 Time Series Insights 환경을 만듭니다.

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md)는 클라우드에서 IoT 규모 시계열 데이터를 관리하기 위한 완전 관리형 분석, 저장 및 가상화 서비스입니다. 새로운 Time Series Insights 환경을 만들려면:

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **리소스 만들기** > **사물 인터넷** > **Time Series Insights**를 선택합니다.

    ![새로운 Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. 솔루션 가속기와 동일한 리소스 그룹에 Time Series Insights 환경을 만들려면 다음 표의 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 환경 이름 | 다음 스크린샷에서는 **Contoso-TSI**라는 이름을 사용합니다. 이 단계를 완료할 때는 자신만의 고유한 이름을 선택합니다. |
    | 구독 | 드롭다운에서 Azure 구독을 선택합니다. |
    | 리소스 그룹 | **contoso-simulation** 솔루션 가속기의 이름을 사용합니다. |
    | Location | 이 예제에서는 **미국 동부**를 사용합니다. 디바이스 시뮬레이션 가속기와 동일한 지역에 환경을 만듭니다. |
    | SKU |**S1** |
    | 용량 | **1** |

    ![Time Series Insights 만들기](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > 솔루션 가속기와 동일한 리소스 그룹에 Time Series Insights 환경을 추가하는 것은 솔루션 가속기를 삭제할 때 삭제됨을 의미합니다.

1. **만들기**를 클릭합니다. 환경을 만드는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-event-source"></a>이벤트 원본 만들기

IoT 허브에 연결할 새 이벤트 원본을 만듭니다. 이전 단계에서 만든 소비자 그룹을 사용합니다. Time Series Insights 이벤트 원본은 다른 서비스에서 사용하고 있지 않은 전용 소비자 그룹이 있어야 합니다.

1. Azure Portal에서 새 시계열 환경으로 이동합니다.

1. 왼쪽에서 **이벤트 원본**을 클릭합니다.

    ![이벤트 원본 보기](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. **추가**를 클릭합니다.

    ![이벤트 원본 추가](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. IoT Hub를 새 이벤트 원본으로 구성하려면 다음 표에 있는 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이벤트 원본 이름 | 다음 스크린샷에서는 **contoso-iot-hub**라는 이름을 사용합니다. 이 단계를 완료할 때는 자신만의 고유한 이름을 사용합니다. |
    | 원본 | **IoT Hub** |
    | 가져오기 옵션 | **사용 가능한 구독에서 IoT Hub 사용** |
    | 구독 ID | 드롭다운에서 Azure 구독을 선택합니다. |
    | IoT Hub 이름 | **contoso-simulation7d894** 디바이스 시뮬레이션 솔루션 가속기에서 IoT 허브의 이름을 사용합니다. |
    | IoT 허브 정책 이름 | **iothubowner** |
    | IoT Hub 정책 키 | 이 필드는 자동으로 채워집니다. |
    | IoT Hub 소비자 그룹 | **devicesimulationtsi** |
    | 이벤트 직렬화 형식 | **JSON** |
    | 시간 간격 속성 이름 | 비워 둠 |

    ![이벤트 원본 만들기](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. **만들기**를 클릭합니다.

> [!NOTE]
> Time Series Insights 탐색기에 [추가 사용자 액세스 권한을 부여](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access)할 수 있습니다.

## <a name="start-a-simulation"></a>시뮬레이션 시작

Time Series Insights 탐색기를 사용하기 전에 일부 원격 분석을 생성하도록 디바이스 시뮬레이션 솔루션 가속기를 구성합니다. 다음 스크린샷에서는 10개의 냉각기 디바이스를 사용하여 실행 중인 시뮬레이션을 보여 줍니다.

![디바이스 시뮬레이션 실행](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights 탐색기

Time Series Insights 탐색기는 원격 분석을 시각화하는 데 사용할 수 있는 웹앱입니다.

1. Azure Portal에서 Time Series Insights **개요** 탭을 선택합니다.

1. Time Series Insights 탐색기 웹앱을 열려면 **환경으로 이동**을 클릭합니다.

    ![Time Series Insights 탐색기](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. 시간 선택 패널의 빠른 시간 메뉴에서 **지난 30분**을 선택하고 **검색**을 클릭합니다.

    ![Time Series Insights 탐색기 검색](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. 왼쪽의 용어 패널에서 **측정값**으로 **온도** 및 **분할 기준** 값으로 **iothub-connection-device-id**를 선택합니다.

    ![Time Series Insights 탐색기 쿼리](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. 차트를 마우스 오른쪽 단추로 클릭하고 **이벤트 탐색**을 선택합니다.

    ![Time Series Insights 탐색기 이벤트](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. 이벤트 데이터는 그리드로 다음을 보여줍니다.

    ![Time Series Insights 탐색기 테이블](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. 큐브 뷰 보기 단추를 클릭합니다.

    ![Time Series Insights 탐색기 큐브 뷰](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. **+** 를 클릭하여 큐브 뷰에 새 쿼리를 추가합니다.

    ![Time Series Insights 탐색기 쿼리 추가](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. 시간 범위로 **지난 30분**, **측정값**으로 **습도** 및 **분할 기준** 값으로 **iothub-connection-device-id**를 선택합니다.

    ![Time Series Insights 탐색기 쿼리](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. 큐브 뷰 보기 단추를 클릭하여 디바이스 원격 분석 대시보드를 봅니다.

    ![Time Series Insights 탐색기 대시보드](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>리소스 정리

더 탐색하려면 솔루션 가속기를 배포된 대로 둡니다.

솔루션 가속기가 더 이상 필요하지 않은 경우 선택한 다음, **솔루션 삭제**를 클릭하여 [프로비전된 솔루션](https://www.azureiotsolutions.com/Accelerators#dashboard) 페이지에서 삭제합니다.

솔루션 가속기의 리소스 그룹에 Time Series Insights 환경을 추가한 경우 솔루션 가속기를 삭제할 때 자동으로 삭제됩니다. 그렇지 않은 경우 Azure Portal에서 Time Series Insights 환경을 수동으로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

Time Series Insights 탐색기에서 데이터를 탐색하고 쿼리하는 방법에 대해 자세히 알아보려면 [Azure Time Series Insights 탐색기](../time-series-insights/time-series-insights-explorer.md)를 참조하세요.
