---
title: '자습서: Azure Time Series Insights 환경 만들기 | Microsoft Docs'
description: 시뮬레이션된 디바이스의 데이터로 채워진 Time Series Insights 환경을 만드는 방법을 알아봅니다.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: b8b46db043113f29f559ad44855d19f0d6ca73c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244152"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>자습서: Azure Time Series Insights 환경 만들기

이 자습서에서는 시뮬레이션된 디바이스의 데이터로 채워진 Azure Time Series Insights 환경을 만드는 프로세스를 안내합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Time Series Insights 환경을 만듭니다.
> * IoT Hub가 포함된 디바이스 시뮬레이션 솔루션을 만듭니다.
> * Time Series Insights 환경을 IoT Hub에 연결합니다.
> * 디바이스 시뮬레이션을 실행하여 데이터를 Time Series Insights 환경으로 스트림합니다.
> * 시뮬레이션된 원격 분석 데이터를 확인합니다.

## <a name="video"></a>비디오

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Azure IoT 솔루션 가속기를 사용하여 데이터를 생성하고 Time Series Insights를 시작하는 방법을 알아보세요. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 또한 Azure 로그인 계정은 구독에 대한 **소유자** 역할의 멤버여야 합니다. 자세한 내용은 [역할 기반 액세스 제어 및 Azure Portal을 사용하여 액세스 관리](/azure/role-based-access-control/role-assignments-portal)를 참조하세요.

## <a name="overview"></a>개요

Time Series Insights 환경은 디바이스 데이터를 수집하고 저장하는 위치입니다. 데이터가 저장되면 [Azure Time Series Insights 탐색기](time-series-quickstart.md) 및 [Time Series Insights 쿼리 API](/rest/api/time-series-insights/ga-query-api)를 사용하여 데이터를 쿼리하고 분석할 수 있습니다. Azure IoT Hub는 모든 디바이스(시뮬레이션된 디바이스 또는 실제 디바이스)에서 Azure 클라우드에 안전하게 연결하고 데이터를 보내는 데 사용하는 연결 지점입니다. [Time Series Insights 개요](time-series-insights-overview.md)에서 설명한 대로 Azure IoT Hub는 데이터를 Time Series Insights 환경으로 스트림하기 위한 이벤트 원본으로도 사용됩니다. 이 자습서에서는 [IoT 솔루션 가속기](/azure/iot-accelerators/)를 사용하여 샘플 원격 분석 데이터를 생성하고 IoT Hub로 스트리밍합니다.

>[!TIP]
> IoT 솔루션 가속기는 사용자 지정 IoT 솔루션의 개발 속도를 높이는 데 사용할 수 있는 미리 구성된 엔터프라이즈급 솔루션을 제공합니다.

## <a name="create-an-environment"></a>환경 만들기

먼저 Time Series Insights 환경을 Azure 구독에 만듭니다.

1. Azure 구독 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다. 
1. **사물 인터넷** 범주를 선택한 다음, **Time Series Insights**를 선택합니다. 

   [![Time Series Insights 환경 리소스 선택](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. **Time Series Insights 환경** 페이지에서 필수 매개 변수를 입력합니다.

   매개 변수|설명
   ---|---
   **환경 이름** | 고유한 Time Series Insights 환경 이름을 선택합니다. 이 이름은 Time Series Insights 탐색기 및 쿼리 API에서 사용합니다.
   **구독** | 구독은 Azure 리소스에 대한 컨테이너입니다. Time Series Insights 환경을 만들 구독을 선택합니다.
   **리소스 그룹** | 리소스 그룹은 Azure 리소스에 대한 컨테이너입니다. Time Series Insights 환경 리소스에 대한 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.
   **위치**: | Time Series Insights 환경에 대한 데이터 센터 지역을 선택합니다. 대역폭 비용과 대기 시간이 추가되지 않도록 Time Series Insights 환경을 다른 IoT 리소스와 동일한 지역에 유지합니다.
   **가격 책정 SKU** | 필요한 처리량을 선택합니다. 최저 비용의 시작 용량을 사용하려면 `S1`을 선택합니다.
   **용량** | 용량은 선택한 SKU와 관련된 입력 속도, 저장 용량 및 비용에 적용된 승수입니다. 생성 후 용량을 변경할 수 있습니다. 최저 비용을 원할 경우 용량 1을 선택합니다.

   완료되면 **만들기**를 선택하여 프로비저닝 프로세스를 시작합니다.

   [![Time Series Insights 환경 리소스 만들기](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. **알림** 패널을 확인하여 배포 완료를 모니터링합니다. 

   [![Time Series Insights 환경 배포 성공](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>디바이스 시뮬레이션 만들기

다음으로, Time Series Insights 환경을 채우기 위한 테스트 데이터를 생성하는 디바이스 시뮬레이션 솔루션을 만듭니다.

1. 별도의 창 또는 탭에서 [azureiotsolutions.com](https://www.azureiotsolutions.com)으로 이동합니다. 동일한 Azure 구독 계정을 사용하여 로그인하고, **디바이스 시뮬레이션** 가속기를 선택합니다.

   [![디바이스 시뮬레이션 가속기 실행](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. **디바이스 시뮬레이션 만들기** 솔루션 페이지에서 필수 매개 변수를 입력합니다.

   매개 변수|설명
   ---|---
   **솔루션 이름** | 이 고유한 값은 새 리소스 그룹을 만드는 데 사용됩니다. 나열된 Azure 리소스가 생성되어 리소스 그룹에 할당됩니다.
   **구독** | 이전 섹션에서 Time Series Insights 환경을 만드는 데 사용한 동일한 구독을 지정합니다.
   **지역** | 이전 섹션에서 Time Series Insights 환경을 만드는 데 사용한 동일한 지역을 지정합니다.
   **선택적 Azure 리소스 배포** | **IoT Hub**를 선택한 상태로 둡니다. 시뮬레이션된 실행 디바이스는 이 허브를 사용하여 데이터를 연결하거나 스트림합니다.

   완료되면 **솔루션 만들기**를 선택하여 솔루션의 Azure 리소스를 프로비저닝합니다. 이 프로세스를 완료하는 데 6-7분이 걸릴 수 있습니다.

   [![디바이스 시뮬레이션 솔루션 프로비전](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. 프로비저닝이 완료되면 새 솔루션 위쪽의 텍스트가 **프로비저닝 중**에서 **준비**로 변경됩니다.

   >[!IMPORTANT]
   > **시작**은 아직 선택하지 마세요! 나중에 다시 돌아올 수 있으므로 이 웹 페이지는 그대로 열어 두세요.

   [![디바이스 시뮬레이션 솔루션 프로비저닝 완료](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. 이제 Azure Portal로 돌아가서 구독에 새로 만든 리소스를 검사합니다. **리소스 그룹** 포털 페이지에서 마지막 단계에 제공된 **솔루션 이름**을 사용하여 새 리소스 그룹이 만들어졌는지 확인합니다. 또한 디바이스 시뮬레이션 솔루션을 지원하기 위해 모든 리소스가 만들어졌는지도 확인합니다.

   [![디바이스 시뮬레이션 솔루션 리소스](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>환경을 IoT Hub에 연결

이 시점에서 각각 사용자 고유의 리소스 그룹에 두 개의 리소스 집합을 만드는 방법에 대해 알아보았습니다.

- 빈 Time Series Insights 환경
- 솔루션 가속기에서 생성한 IoT Hub가 포함된 디바이스 시뮬레이션 솔루션 리소스

시뮬레이션된 디바이스는 디바이스 데이터를 스트리밍하기 위해 IoT Hub에 연결해야 합니다. 데이터를 Time Series Insights 환경으로 전달하려면 IoT Hub와 Time Series Insights 환경의 구성을 모두 변경해야 합니다.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT Hub 구성: 소비자 그룹 정의

IoT Hub는 다른 행위자와 기능을 공유하기 위한 다양한 엔드포인트를 제공합니다. “이벤트” 엔드포인트는 다른 애플리케이션에서 IoT Hub 인스턴스로 스트림할 때 데이터를 사용할 수 있는 방법을 제공합니다. 특히 "소비자 그룹"은 애플리케이션에서 IoT Hub의 데이터를 수신 대기하고 끌어오는 메커니즘을 제공합니다.

다음으로, 디바이스 시뮬레이션 솔루션의 IoT Hub **이벤트 엔드포인트**에서 새 **소비자 그룹** 속성을 정의합니다.

1. Azure Portal에서 디바이스 시뮬레이션 솔루션에 대해 만든 리소스 그룹의 **개요** 페이지로 이동합니다. IoT Hub 리소스를 선택합니다.

   [![디바이스 시뮬레이션 솔루션 리소스 그룹](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   솔루션에 대해 생성된 IoT Hub 리소스의 **이름**을 적어두세요. 나중에 참조할 수 있습니다.

1. 아래로 스크롤하여 **엔드포인트** 페이지를 선택한 다음, **이벤트** 엔드포인트를 선택합니다. 엔드포인트 **속성** 페이지의 “$Default” 소비자 그룹 아래에서 엔드포인트에 대한 고유한 이름을 입력합니다. **저장**을 선택합니다.

   [![디바이스 시뮬레이션 솔루션 IoT Hub 엔드포인트](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>환경 구성: 이벤트 원본 정의

이제 새 IoT Hub **소비자 그룹** 이벤트 엔드포인트를 **이벤트 원본**으로 Time Series Insights 환경에 연결합니다.

1. Time Series Insights 환경에 대해 만든 리소스 그룹의 **개요** 페이지로 이동합니다. Time Series Insights 환경을 선택합니다.

   [![Time Series Insights 환경 리소스 그룹 및 환경](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Time Series Insights 환경 페이지에서 **이벤트 원본**을 선택한 다음, **+ 추가**를 선택합니다.

   [![Time Series Insights 환경 개요](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. **새 이벤트 원본** 페이지에서 필수 매개 변수를 입력합니다.

   매개 변수|설명
   ---|---
   **이벤트 원본 이름** | 이벤트 원본 이름을 지정하는 데 사용되는 고유한 값이 필요합니다.
   **원본** | **IoT Hub**를 선택합니다.
   **가져오기 옵션** | 기본 `Use IoT hub from available subscriptions`를 선택합니다. 이 옵션을 사용하면 사용 가능한 구독이 다음 드롭다운 목록에 채워집니다.
   **구독** | Time Series Insights 환경과 디바이스 시뮬레이션 리소스를 만든 동일한 구독을 선택합니다.
   **IoT Hub 이름** | 앞에서 기록해 둔 IoT Hub의 이름을 기본값으로 적용해야 합니다. 그렇지 않은 경우 올바른 IoT Hub를 선택합니다.
   **IoT Hub 정책 이름** | **iothubowner**를 선택합니다.
   **IoT Hub 소비자 그룹** | 앞에서 만든 IoT Hub 소비자 그룹의 이름을 기본값으로 적용해야 합니다. 그렇지 않은 경우 올바른 소비자 그룹 이름을 선택합니다.
   **이벤트 직렬화 형식** | 기본 `JSON` 값을 유지합니다.
   **타임스탬프 속성 이름** | `timestamp`로 지정합니다.

   완료되면 **만들기**를 선택하여 이벤트 원본을 추가합니다. 리소스 그룹 **개요** 페이지로 돌아가면 Time Series Insights 환경 리소스와 함께 새 "Time Series Insights 이벤트 원본" 리소스가 표시됩니다.

   [![Time Series Insights 환경 새 이벤트 원본](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>디바이스 시뮬레이션을 실행하여 데이터를 스트리밍

이제 모든 구성 작업이 완료되었으므로 Time Series Insights 환경을 시뮬레이션된 디바이스의 샘플 데이터로 채울 시간입니다.

[디바이스 시뮬레이션 만들기](#create-a-device-simulation) 섹션에서 설명한 대로 솔루션을 지원하기 위해 가속기에서 여러 Azure 리소스를 만들었습니다. 앞에서 설명한 IoT Hub와 함께, 시뮬레이션된 장치 원격 분석을 만들고 전송하도록 Azure App Service 웹 애플리케이션이 생성되었습니다.

1. [솔루션 가속기 대시보드](https://www.azureiotsolutions.com/Accelerators#dashboard)로 돌아갑니다. 필요한 경우 이 자습서에서 사용한 것과 동일한 Azure 계정을 사용하여 다시 로그인합니다. 이제 "디바이스 시뮬레이션" 솔루션 아래에서 **시작**을 선택할 수 있습니다.

     [![솔루션 가속기 대시보드](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. 이제 디바이스 시뮬레이션 웹앱이 시작되고, 초기 로드가 완료되는 데 몇 초가 걸릴 수 있습니다. 또한 "로그인 및 프로필 읽기" 권한을 웹 애플리케이션에 부여하는 데 동의하는지 묻는 메시지가 표시됩니다. 이 권한을 통해 애플리케이션은 애플리케이션의 기능을 지원하는 데 필요한 사용자 프로필 정보를 검색할 수 있습니다.

     [![디바이스 시뮬레이션 웹 애플리케이션 동의](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. **시뮬레이션 설정** 페이지가 로드되면 필수 매개 변수를 입력합니다.

   매개 변수|설명
   ---|---
   **대상 IoT Hub** | **미리 프로비저닝된 IoT Hub 사용**을 선택합니다.
   **디바이스 모델** | **냉각기**를 선택합니다.
   **디바이스 수**  | **양**에 `1000`을 입력합니다.
   **원격 분석 빈도** | `10`초를 입력합니다.
   **시뮬레이션 기간** | **종료:** 를 선택하고 `5`분을 입력합니다.

   완료되면 **시뮬레이션 시작**을 선택합니다. 시뮬레이션은 총 5분 동안 실행됩니다. 10초마다 1,000개의 시뮬레이션된 디바이스로부터 데이터를 생성합니다. 

   [![디바이스 시뮬레이션 설정](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. 시뮬레이션이 실행되는 동안 **총 메시지 수**와 **초당 메시지 수** 필드가 약 10초마다 업데이트됩니다. 약 5분 후에 시뮬레이션이 완료되고 **시뮬레이션 설정**으로 돌아갑니다.

   [![디바이스 시뮬레이션 실행 중](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>원격 분석 데이터 확인

이 마지막 섹션에서는 원격 분석 데이터가 생성되어 Time Series Insights 환경에 저장되었는지 확인합니다. 데이터를 확인하려면 원격 분석 데이터를 쿼리하고 분석하는 데 사용되는 Time Series Insights 탐색기를 사용합니다.

1. Time Series Insights 환경의 리소스 그룹 **개요** 페이지로 돌아갑니다. Time Series Insights 환경을 선택합니다.

   [![Time Series Insights 환경 리소스 그룹 및 환경](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Time Series Insights 환경 **개요** 페이지에서 **Time Series Insights 탐색기 URL**을 선택하여 Time Series Insights 탐색기를 엽니다.

   [![Time Series Insights 탐색기](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Time Series Insights 탐색기에서 Azure Portal 계정을 사용하여 로드하고 인증합니다. 초기 보기의 차트 영역에서 Time Series Insights 환경이 시뮬레이션된 원격 분석 데이터로 채워져 있음을 확인할 수 있습니다. 더 좁은 시간 범위를 필터링하려면 왼쪽 위 모서리에서 드롭다운을 선택합니다. 디바이스 시뮬레이션의 기간을 처리할 만큼 충분히 큰 시간 범위를 입력합니다. 그런 다음, 검색 돋보기를 선택합니다.

   [![Time Series Insights 탐색기 시간 범위 필터](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. 시간 범위를 좁히면 차트가 IoT Hub 및 Time Series Insights 환경으로의 데이터 전송에 대한 고유한 버스트로 확대됩니다. 또한 오른쪽 위 모서리의 **스트리밍이 완료되었습니다** 텍스트에는 검색된 총 이벤트 수가 표시됩니다. **간격 크기** 슬라이더를 끌어 차트의 플롯 세분성을 제어할 수도 있습니다.

   [![Time Series Insights 탐색기 필터링된 시간 범위 보기](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. 마지막으로 마우스 왼쪽 단추로 영역을 클릭하여 범위를 필터링할 수도 있습니다. 그런 다음, 마우스 오른쪽 단추를 클릭하고 **이벤트 검색**을 사용하여 테이블 형식 **이벤트** 보기에 이벤트 세부 정보를 표시합니다.

   [![Time Series Insights 탐색기 필터링된 시간 범위 보기 및 이벤트](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 Time Series Insights 환경과 디바이스 시뮬레이션 솔루션을 지원하기 위해 실행되는 몇 가지 Azure 서비스를 만듭니다. 이 자습서 시리즈에서 작업을 중단하거나 연기하려면 불필요한 비용이 발생하지 않도록 모든 리소스를 삭제합니다.

Azure Portal의 왼쪽 메뉴에서 다음을 수행합니다.

1. **리소스 그룹** 아이콘을 선택합니다. Time Series Insights 환경에 대해 만든 리소스 그룹을 선택합니다. 페이지 위쪽에서 **리소스 그룹 삭제**를 선택하고, 리소스 그룹 이름을 입력하고, **삭제**를 선택합니다.

1. **리소스 그룹** 아이콘을 선택합니다. 그런 다음, 디바이스 시뮬레이션 솔루션 가속기에서 만든 리소스 그룹을 선택합니다. 페이지 위쪽에서 **리소스 그룹 삭제**를 선택하고, 리소스 그룹 이름을 입력하고, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Time Series Insights 환경을 만듭니다.
> * IoT Hub가 포함된 디바이스 시뮬레이션 솔루션을 만듭니다.
> * Time Series Insights 환경을 IoT Hub에 연결합니다.
> * 디바이스 시뮬레이션을 실행하여 데이터를 Time Series Insights 환경으로 스트림합니다.
> * 시뮬레이션된 원격 분석 데이터를 확인합니다.

이제 사용자 고유의 Time Series Insights 환경을 만드는 방법을 알아보았으므로 Time Series Insights 환경의 데이터를 사용하는 웹 애플리케이션을 빌드하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Time Series Insights 단일 페이지 웹앱 만들기](tutorial-create-tsi-sample-spa.md)
