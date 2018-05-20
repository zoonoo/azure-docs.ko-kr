---
title: Azure Time Series Insights로 원격 모니터링 데이터 시각화 | Microsoft Docs
description: Time Series Insights 환경을 구성하여 원격 모니터링 솔루션의 시계열 데이터를 탐색 및 분석하는 방법에 대해 알아봅니다.
services: ''
suite: iot-suite
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: article
ms.service: iot-suite
ms.openlocfilehash: c7ddac84a16520d6bc4c4eeb845206f84e6edd80
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Time Series Insights로 원격 모니터링 데이터 시각화

연산자는 원격 모니터링 사전 구성 솔루션에서 제공하는 특별한 데이터 시각화를 더욱 확장할 수 있습니다. 솔루션 액셀러레이터는 TSI와의 특별한 통합을 제공합니다. 이 방법에서는 Time Series Insights를 구성하여 장치 원격 분석을 분석하고 잘못된 부분을 감지하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 방법을 완료하려면 다음이 필요합니다.

* [원격 모니터링 사전 구성 솔루션 배포](/iot-suite/iot-suite-remote-monitoring-deploy)

## <a name="create-a-consumer-group"></a>소비자 그룹 만들기

Time Series Insights에 데이터를 스트리밍하는 데 사용할 IoT Hub에서 전용 소비자 그룹을 만들어야 합니다.

> [!NOTE]
> 소비자 그룹은 응용 프로그램에서 Azure IoT Hub의 데이터를 끌어오는 데 사용됩니다. 각 소비자 그룹은 최대 5개의 출력 소비자를 허용합니다. 각 5개의 출력 싱크에 대해 새로운 소비자 그룹을 만들어야 하며, 최대 32개의 소비자 그룹을 만들 수 있습니다.

1. Azure Portal에서 Cloud Shell 단추를 클릭합니다.

1. 다음 명령을 실행하여 새 소비자 그룹을 만듭니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>새로운 Time Series Insights 환경을 만듭니다.

Azure Time Series Insights는 클라우드에서 IoT 규모 시계열 데이터를 관리하기 위한 완전 관리형 분석, 저장 및 가상화 서비스입니다. 확장성 높은 시계열 데이터 저장소를 제공하며 전 세계에서 수십억 개의 이벤트 스트리밍을 몇 초만에 탐색 및 분석할 수 있도록 해줍니다. Time Series Insights를 사용하면 테라바이트 단위의 시계열 데이터를 저장 및 관리하고, 수십억 개의 이벤트를 동시에 탐색 및 시각화하고, 근본 원인 분석을 수행하며, 여러 사이트 및 자산을 비교할 수 있습니다.

1. [Azure Portal](http://portal.azure.com/)에 로그인합니다.

1. **리소스 만들기** > **사물 인터넷** > **Time Series Insights**를 선택합니다.

    ![새로운 Time Series Insights](media/iot-suite-time-series-insights/new-time-series-insights.png)

1. Time Series Insights 환경을 만들려면 다음 표에 있는 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 환경 이름 | 다음 스크린샷에서는 **contorosrmtsi**라는 이름을 사용합니다. 이 단계를 완료할 때는 자신만의 고유한 이름을 선택합니다. |
    | 구독 | 드롭다운에서 Azure 구독을 선택합니다. |
    | 리소스 그룹 | **새로 만듭니다**. **ContosoRM**이라는 이름을 사용합니다. |
    | 위치 | **미국 동부**를 사용합니다. 원격 모니터링 솔루션으로 동일한 지역에 있는 환경을 만듭니다. |
    | SKU |**S1** |
    | 용량 | **1** |
    | 대시보드에 고정 | **예** |

    ![Time Series Insights 만들기](media/iot-suite-time-series-insights/new-time-series-insights-create.png)

1. **만들기**를 클릭합니다. 환경을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="create-event-source"></a>이벤트 원본 만들기

IoT Hub에 연결할 새 이벤트 원본을 만듭니다. 이전 단계에서 만든 소비자 그룹을 사용하고 있는지 확인합니다. Time Series Insights는 각 서비스에 다른 서비스에서 사용하고 있지 않은 전용 소비자 그룹이 있어야 합니다.

1. 새 시계열 환경으로 이동합니다.

1. 왼쪽에서 **이벤트 원본**을 선택합니다.

    ![이벤트 원본 보기](media/iot-suite-time-series-insights/time-series-insights-event-sources.png)

1. **추가**를 클릭합니다.

    ![이벤트 원본 추가](media/iot-suite-time-series-insights/time-series-insights-event-sources-add.png)

1. IoT Hub를 새 이벤트 원본으로 구성하려면 다음 표에 있는 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이벤트 원본 이름 | 다음 스크린샷에서는 **contosorm-iot-hub**라는 이름을 사용합니다. 이 단계를 완료할 때는 자신만의 고유한 이름을 사용합니다. |
    | 원본 | **IoT 허브** |
    | 가져오기 옵션 | **사용 가능한 구독에서 IoT Hub 사용** |
    | 구독 ID | 드롭다운에서 Azure 구독을 선택합니다. |
    | IoT Hub 이름 | **contosorma57a6**입니다. 원격 모니터링 솔루션에서 IoT Hub의 이름을 사용합니다. |
    | IoT Hub 정책 이름 | **iothubowner** 사용되는 정책이 소유자 정책인지 확인합니다. |
    | IoT Hub 정책 키 | 이 필드는 자동으로 채워집니다. |
    | IoT Hub 소비자 그룹 | **timeseriesinsights** |
    | 이벤트 직렬화 형식 | **JSON**     | 타임스탬프 속성 이름 | 비워 둠 |

    ![이벤트 원본 만들기](media/iot-suite-time-series-insights/time-series-insights-event-source-create.png)

1. **만들기**를 클릭합니다.

> [!NOTE]
> Time Series Insights 탐색기에 대한 액세스 권한을 추가 사용자에게 부여해야 하는 경우 다음 단계를 사용하여 [데이터 액세스 권한을 부여](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access#grant-data-access)할 수 있습니다.

## <a name="time-series-insights-explorer"></a>Time Series Insights 탐색기

Time Series Insights 탐색기는 사용자 데이터의 시각화를 만드는 데 유용한 웹앱입니다.

1. **개요** 탭을 선택합니다.

1. **환경으로 이동**을 클릭합니다. 그러면 Time Series Insights 탐색기 웹앱이 열립니다.

    ![Time Series Insights 탐색기](media/iot-suite-time-series-insights/time-series-insights-environment.png)

1. 시간 선택 패널의 빠른 시간 메뉴에서 **지난 12시간**을 선택하고 **검색**을 클릭합니다.

    ![Time Series Insights 탐색기 검색](media/iot-suite-time-series-insights/time-series-insights-search-time.png)

1. 왼쪽의 용어 패널에서 **온도**의 측정 값 및 **iothub-connection-device-id**의 분할 기준 값을 선택합니다.

    ![Time Series Insights 탐색기 쿼리](media/iot-suite-time-series-insights/time-series-insights-query1.png)

1. 차트를 마우스 오른쪽 단추로 클릭하고 **이벤트 탐색**을 선택합니다.

    ![Time Series Insights 탐색기 이벤트](media/iot-suite-time-series-insights/time-series-insights-explore-events.png)

1. 이벤트는 눈금에서 테이블 형식으로 렌더링됩니다.

    ![Time Series Insights 탐색기 테이블](media/iot-suite-time-series-insights/time-series-insights-table.png)

1. 큐브 뷰 단추를 클릭합니다.

    ![Time Series Insights 탐색기 큐브 뷰](media/iot-suite-time-series-insights/time-series-insights-explorer-perspective.png)

1. **추가**를 클릭하여 큐브 뷰에서 새 쿼리를 만듭니다.

    ![Time Series Insights 탐색기 쿼리 추가](media/iot-suite-time-series-insights/time-series-insights-new-query.png)

1. **지난 12시간**의 빠른 시간, **습도**의 측정값 및 **iothub-connection-device-id**의 분할 기준을 선택합니다.

    ![Time Series Insights 탐색기 쿼리](media/iot-suite-time-series-insights/time-series-insights-query2.png)

1. 큐브 뷰 단추를 클릭하여 장치 메트릭의 대시보드를 봅니다.

    ![Time Series Insights 탐색기 대시보드](media/iot-suite-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>다음 단계

Time Series Insights 탐색기에서 데이터를 탐색하고 쿼리하는 방법에 대해 알아보려면 [Azure Time Series Insights 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-dashboard.png)를 참조하세요.
