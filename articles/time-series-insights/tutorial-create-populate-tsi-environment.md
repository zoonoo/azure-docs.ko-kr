---
title: '자습서: Azure Time Series Insights 환경 만들기 | Microsoft Docs'
description: 시뮬레이션된 디바이스의 데이터로 채워진 Time Series Insights 환경을 만드는 방법에 대해 알아봅니다.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 58b19a039bb6aebcd2687ab80cdd29c5f378645e
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200210"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>자습서: Azure Time Series Insights 환경 만들기

이 자습서에서는 시뮬레이션된 디바이스의 데이터로 채워진 TSI(Time Series Insight) 환경을 만드는 과정을 안내합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * TSI 환경 만들기 
> * IoT Hub를 포함하는 디바이스 시뮬레이션 솔루션 만들기
> * TSI 환경을 IoT Hub에 연결
> * TSI 환경으로 데이터를 스트리밍하도록 디바이스 시뮬레이션 실행
> * 시뮬레이션된 원격 분석 데이터 확인

## <a name="video"></a>비디오

이 비디오에서는 Azure IoT 솔루션 가속기를 사용하여 Time Series Insights를 시작하는 데 사용할 수 있는 데이터를 생성하는 방법을 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다. 

Azure 로그인 계정도 구독에서 소유자 역할의 멤버여야 합니다. 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](/azure/role-based-access-control/role-assignments-portal)를 참조하세요.

## <a name="overview"></a>개요

TSI 환경에서 디바이스 데이터가 수집되어 저장됩니다. TSI 환경에 저장되면 [TSI 탐색기](time-series-quickstart.md) 및 [TSI 쿼리 API](/rest/api/time-series-insights/ga-query-api)를 사용하여 쿼리하고 데이터를 분석할 수 있습니다.

시뮬레이션되거나 물리적인 모든 디바이스와 마찬가지로, IoT Hub는 디바이스가 Azure 클라우드에 안전하게 연결하고 데이터를 전송하기 위해 사용하는 연결 지점입니다. [TSI 개요](time-series-insights-overview.md)에 설명된 대로, IoT Hub는 TSI 환경으로 데이터를 스트리밍하기 위한 이벤트 원본으로도 제공됩니다. 

이 자습서에서는 또한 [IoT 솔루션 가속기](/azure/iot-accelerators/)를 사용하여 샘플 원격 분석 데이터를 생성하여 IoT Hub로 스트리밍합니다. IoT 솔루션 가속기는 사용자 지정 IoT 솔루션 개발 속도를 높일 수 있는 미리 구성된 엔터프라이즈급 솔루션을 제공합니다. 

## <a name="create-a-tsi-environment"></a>TSI 환경 만들기

먼저, Azure 구독에 TSI 환경을 만듭니다.

1. Azure 구독 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.  
2. 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.  
3. **사물 인터넷** 범주를 선택한 다음, **Time Series Insights**를 선택합니다.  
   
   [![Time Series Insights 환경 리소스 선택](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. **Time Series Insights 환경** 페이지에서 필수 매개 변수를 입력합니다.
   
   매개 변수|설명
   ---|---
   **환경 이름** | TSI 환경에 고유한 이름을 선택합니다. 이 이름은 TSI 탐색기 및 쿼리 API에 사용됩니다.
   **구독** | 구독은 Azure 리소스에 대한 컨테이너입니다. TSI 환경을 만들 구독을 선택합니다.
   **리소스 그룹** | 리소스 그룹은 Azure 리소스에 대한 컨테이너입니다. TSI 환경 리소스에 대해 기존 리소스 그룹을 선택하거나 새 항목을 만듭니다.
   **위치**: | TSI 환경에 대한 데이터 센터 지역을 선택합니다. 추가된 대역폭 비용과 대기 시간을 피하려면 다른 IoT 리소스와 동일한 지역에 TSI 환경을 유지하는 것이 가장 좋습니다.
   **가격 책정 SKU** | 필요한 처리량을 선택합니다. 최저 비용의 시작 용량을 사용하려면 S1을 선택합니다.
   **용량** | 용량은 선택한 SKU와 관련된 입력 속도, 저장 용량 및 비용에 적용된 승수입니다.  환경을 만든 후 환경의 용량을 변경할 수 있습니다. 최저 비용을 원할 경우 용량 1을 선택합니다. 

   완료했으면 **만들기**를 클릭하여 프로비전 프로세스를 시작합니다.

   ![Time Series Insights 환경 리소스 만들기](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. **알림** 패널을 확인하여 1분 미만이 소요되는 배포 완료를 모니터링할 수 있습니다.  

   ![Time Series Insights 환경 배포 성공](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>디바이스 시뮬레이션 만들기

다음으로, TSI 환경을 채우기 위한 테스트 데이터를 생성할 디바이스 시뮬레이션 솔루션을 만듭니다.

1. 별도의 창/탭에서 https://www.azureiotsolutions.com으로 이동하고 동일한 Azure 구독 계정을 사용하여 로그인하고 “디바이스 시뮬레이션” 가속기를 선택합니다.

   ![디바이스 시뮬레이션 가속기 실행](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. **디바이스 시뮬레이션 솔루션 만들기** 페이지에서 필수 매개 변수를 입력합니다.

   매개 변수|설명
   ---|---
   **솔루션 이름** | 새 리소스 그룹의 생성에 사용되는 고유 값입니다. 나열된 Azure 리소스가 생성되어 리소스 그룹에 할당됩니다.
   **구독** | 이전 섹션에서 TSI 환경 만들기에 사용했던 동일한 구독을 지정합니다.
   **지역** | 이전 섹션에서 TSI 환경 만들기에 사용했던 동일한 지역을 지정합니다. 
   **선택적 Azure 리소스 배포** | 시뮬레이션된 디바이스가 데이터를 연결/스트리밍하는 데 사용하므로 **IoT Hub**를 선택한 상태로 둡니다.

   완료했으면 **솔루션 만들기**를 클릭하여 솔루션의 Azure 리소스를 프로비전합니다. 이 과정은 완료하는 데 약 6-7분이 소요됩니다.

   ![디바이스 시뮬레이션 솔루션 프로비전](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. 프로비전이 완료되면 새 솔루션 위에 있는 텍스트가 “프로비전 중...”에서 “준비”로 변경됩니다.

   >[!IMPORTANT]
   > 아직 **실행** 단추를 클릭하지 마세요! 단, 나중에 돌아올 수 있도록 이 웹 페이지를 열어 두세요.

   ![디바이스 시뮬레이션 솔루션 프로비전 완료](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. 이제 Azure Portal로 돌아가 구독에서 새로 만든 리소스를 검사합니다. 포털 **리소스 그룹** 페이지에서 마지막 단계에 제공된 **솔루션 이름**을 사용하여 새 리소스 그룹이 만들어진 것을 확인할 수 있습니다. 또한 모든 리소스가 디바이스 시뮬레이션 솔루션을 지원하도록 생성된 것도 확인할 수 있습니다.

   [![디바이스 시뮬레이션 솔루션 리소스](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>TSI 환경을 IoT Hub에 연결 

이 시점에서 각각 사용자 고유의 리소스 그룹에 두 개의 리소스 집합을 만드는 방법에 대해 알아보았습니다. 
- 비어 있는 TSI 환경 
- 솔루션 가속화로 생성된 IoT Hub를 포함한 디바이스 시뮬레이션 솔루션 리소스

시뮬레이션된 디바이스는 디바이스 데이터를 스트리밍하기 위해 IoT Hub에 연결해야 합니다. TSI 환경으로 데이터를 전송하려면 IoT Hub와 TSI 환경의 구성을 모두 변경해야 합니다. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT Hub 구성: 소비자 그룹 정의

IoT Hub는 다른 행위자와 기능을 공유할 수 있는 다양한 엔드포인트를 제공합니다. “이벤트” 엔드포인트는 다른 애플리케이션이 IoT Hub 인스턴스로 스트리밍할 때 데이터를 사용하는 방법을 제공합니다. 특히, “소비자 그룹”은 애플리케이션이 IoT Hub에서 데이터를 수신 대기하고 끌어오는 메커니즘을 제공합니다. 

이제 디바이스 시뮬레이션 솔루션의 IoT Hub “이벤트” 엔드포인트에서 새 “소비자 그룹” 속성을 정의합니다. 

1. Azure Portal에서 디바이스 시뮬레이션 솔루션에 대해 만든 리소스 그룹의 **개요** 페이지로 이동하고 IoT Hub 리소스를 선택합니다.

   [![디바이스 시뮬레이션 솔루션 리소스 그룹](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   나중에 참조할 수 있으므로 해당 리소스에 대해 생성된 IoT Hub 리소스의 **이름**도 기록해 두세요.

2. 아래로 스크롤하고 **엔드포인트** 페이지를 선택한 다음, **이벤트** 엔드포인트를 선택합니다. 엔드포인트 **속성** 페이지에서 “$Default” 소비자 그룹 아래 엔드포인트에 대한 고유한 이름을 입력한 다음, **저장**을 클릭합니다.

   [![디바이스 시뮬레이션 솔루션 IoT Hub 엔드포인트](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>TSI 환경 구성: 이벤트 원본 정의

이제 새 IoT Hub “소비자 그룹” 이벤트 엔드포인트를 “이벤트 원본”으로 TSI 환경에 연결합니다.

1. TSI 환경에 대해 만든 리소스 그룹의 **개요** 페이지로 이동한 다음, TSI 환경을 선택합니다.

   [![TSI 환경 리소스 그룹 및 환경](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. TSI 환경 페이지에서 **이벤트 원본**을 선택한 다음, **+ 추가**를 클릭합니다.

   ![TSI 환경 개요](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. **새 이벤트 원본** 페이지에서 필수 매개 변수를 입력합니다. 

   매개 변수|설명
   ---|---
   **이벤트 원본 이름** | 이벤트 원본 이름을 지정하는 데 사용되는 고유한 값이 필요합니다.
   **원본** | [IoT Hub]를 선택합니다.
   **가져오기 옵션** | 기본값으로 설정된 [사용 가능한 구독에서 IoT Hub 사용]을 사용합니다. 이 옵션을 사용하면 다음 드롭다운 목록이 사용 가능한 구독으로 채워집니다.
   **구독** | TSI 환경과 디바이스 시뮬레이션 리소스를 만들었던 동일한 구독을 선택합니다.
   **IoT Hub 이름** | 앞에서 기록해 둔 IoT Hub의 이름을 기본값으로 적용해야 합니다. 그렇지 않은 경우 올바른 IoT Hub를 선택합니다.
   **IoT Hub 정책 이름** | 기본값 “iothubowner”를 유지합니다.
   **IoT Hub 소비자 그룹** | 앞에서 만든 IoT Hub 소비자 그룹의 이름을 기본값으로 적용해야 합니다. 그렇지 않은 경우 올바른 소비자 그룹 이름을 선택합니다. 
   **이벤트 직렬화 형식** | 기본값 “JSON”을 유지합니다.
   **타임스탬프 속성 이름** | “timestamp”로 지정합니다.

   완료되면 **만들기**를 클릭하여 이벤트 원본을 추가합니다. 리소스 그룹 **개요** 페이지로 돌아가면 TSI 환경 리소스와 함께 새로운 “Time Series Insights 이벤트 원본” 리소스가 표시됩니다.

   ![TSI 환경 새 이벤트 원본](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>TSI 환경으로 데이터를 스트리밍하도록 디바이스 시뮬레이션 실행

이제 모든 구성 작업이 완료되었으므로 TSI 환경을 시뮬레이션된 디바이스의 샘플 데이터로 채우면 됩니다.

[디바이스 시뮬레이션 만들기 섹션](#create-a-device-simulation)에서 살펴보았듯이 여러 Azure 리소스가 솔루션을 지원하기 위해 가속기에 의해 만들어졌습니다. 앞에서 설명한 IoT Hub와 함께, 시뮬레이션된 장치 원격 분석을 만들고 전송하도록 Azure App Service 웹 애플리케이션이 생성되었습니다.

1. [솔루션 가속기 대시보드](https://www.azureiotsolutions.com/Accelerators#dashboard)로 돌아갑니다. 필요한 경우 이 자습서에서 사용한 것과 동일한 Azure 계정을 사용하여 다시 로그인합니다. 이제 “디바이스 시뮬레이션” 솔루션 아래 **실행** 단추를 클릭할 수 있습니다.

     ![솔루션 가속기 대시보드](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. 이 시점에서 디바이스 시뮬레이션 웹앱이 시작되고 초기 로드가 완료되는 데 몇 초가 소요될 수 있습니다. 또한 웹 애플리케이션에 “로그인 및 프로필 읽기” 권한을 부여하기 위한 동의를 묻는 메시지가 나타납니다. 이 권한을 통해 애플리케이션은 애플리케이션의 기능을 지원하는 데 필요한 사용자 프로필 정보를 검색할 수 있습니다.

     ![장치 시뮬레이션 웹 애플리케이션 동의](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. **시뮬레이션 설정** 페이지가 로드되면 필수 매개 변수를 입력합니다. 

   매개 변수|설명
   ---|---
   **대상 IoT Hub** | [사전 프로비전된 IoT Hub 사용]을 선택합니다.
   **디바이스 모델** | [냉각기]를 선택합니다.
   **디바이스 수**  | **양**에 1000을 입력합니다.
   **원격 분석 빈도** | 10초를 입력합니다.
   **시뮬레이션 기간** | **종료:** 를 선택하고 5분을 입력합니다.

   완료되면 **시뮬레이션 시작**을 클릭합니다. 시뮬레이션은 총 5분 동안 실행되어 1000개의 시뮬레이션된 디바이스에서 10초마다 데이터를 생성합니다.  

   ![디바이스 시뮬레이션 설정](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. 시뮬레이션이 실행되는 동안 약 10초마다 **총 메시지 수**와 **초당 메시지 수** 필드가 업데이트되는 것을 확인할 수 있습니다. 시뮬레이션은 약 5분 후에 끝나고 **시뮬레이션 설정**으로 돌아갑니다.

   ![디바이스 시뮬레이션 실행 중](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>원격 분석 데이터 확인

이 마지막 섹션에서는 원격 분석 데이터가 생성되어 TSI 환경에 저장되었는지 확인합니다. 데이터를 확인하려면 원격 분석 데이터를 쿼리하고 분석하는 데 사용되는 Time Series Insights 탐색기를 사용합니다.

1. TSI 환경의 리소스 그룹 **개요** 페이지로 돌아가서 TSI 환경을 다시 선택합니다.

   [![TSI 환경 리소스 그룹 및 환경](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. TSI 환경 **개요** 페이지에서 **Time Series Insights 탐색기 URL**을 클릭하여 TSI 탐색기를 엽니다.

   [![TSI 탐색기](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. TSI 탐색기는 Azure Portal 계정을 사용하여 로드하고 인증합니다. 초기 보기에서는 TSI 환경이 시뮬레이션된 원격 분석 데이터로 실제로 채워져 있음을 차트 영역에서 볼 수 있습니다. 더 좁은 범위의 시간을 필터링하려면 왼쪽 상단의 드롭다운을 선택합니다. 그런 다음, 디바이스 시뮬레이션의 기간을 처리할 만큼 충분히 큰 시간 범위를 입력합니다. 검색 돋보기를 클릭합니다.

   [![TSI 탐색기 시간 범위 필터](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. 시간 범위를 좁히면 데이터 전송의 고유한 버스트나 IoT Hub 및 TSI 환경으로 차트를 확대할 수 있습니다. 또한 오른쪽 상단의 **스트리밍 완료** 텍스트에는 발견된 총 이벤트 수가 표시됩니다. **간격 크기** 슬라이더를 끌어 차트의 플롯 세분성을 제어할 수도 있습니다.

   [![TSI 탐색기 시간 범위 필터링된 보기](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. 마지막으로 영역을 마우스 왼쪽 단추로 클릭하여 범위를 필터링한 다음, 마우스 오른쪽 단추를 클릭하고 **이벤트 탐색**을 사용하여 표 형식 **이벤트** 보기에서 이벤트 세부 정보를 볼 수 있습니다.

   [![TSI 탐색기 시간 범위 필터링된 보기 및 이벤트](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 TSI 환경과 디바이스 시뮬레이션 솔루션을 지원하기 위해 실행 중인 Azure 서비스를 여러 개 만듭니다. 이 자습서 시리즈를 중단 및/또는 지연시키려면 불필요한 비용이 발생하지 않도록 모든 리소스를 삭제하는 것이 좋습니다. 

Azure Portal의 왼쪽 메뉴에서:

1. **리소스 그룹** 아이콘을 클릭한 다음, TSI 환경용으로 만든 리소스 그룹을 선택합니다. 페이지 맨 위에서 **리소스 그룹 삭제**를 클릭하고, 리소스 그룹의 이름을 입력한 다음 **삭제**를 클릭합니다. 
2. **리소스 그룹** 아이콘을 클릭한 다음, 디바이스 시뮬레이션 솔루션 가속기에서 만든 리소스 그룹을 선택합니다. 페이지 맨 위에서 **리소스 그룹 삭제**를 클릭하고, 리소스 그룹의 이름을 입력한 다음, **삭제**를 클릭합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * TSI 환경 만들기 
> * IoT Hub를 포함하는 디바이스 시뮬레이션 솔루션 만들기
> * TSI 환경을 IoT Hub에 연결
> * TSI 환경으로 데이터를 스트리밍하도록 디바이스 시뮬레이션 실행
> * 시뮬레이션된 원격 분석 데이터 확인

고유한 TSI 환경을 만드는 방법을 배웠으므로, 이번에는 TSI 환경의 데이터를 사용하는 웹 애플리케이션을 빌드하는 방법을 알아보겠습니다.

> [!div class="nextstepaction"]
> [Azure Time Series Insights 단일 페이지 웹앱 만들기](tutorial-create-tsi-sample-spa.md)


