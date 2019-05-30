---
title: '개요: Azure Time Series Insights란? | Microsoft Docs'
description: 시계열 데이터 분석 및 IoT 솔루션을 위한 새로운 서비스인 Azure Time Series Insights를 소개합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 85ce1748ee6c68ac96436b353d9240a22f8a479a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826383"
---
# <a name="what-is-azure-time-series-insights"></a>Azure Time Series Insights란?

Azure Time Series Insights는 IoT 디바이스에 의해 생성된 것과 같은 대량의 시계열 데이터를 저장하고, 시각화하고, 쿼리하기 위해 구축되었습니다.  클라우드의 시계열 데이터를 저장, 관리, 쿼리 또는 시각화하려면 Time Series Insights를 사용하는 것이 좋습니다.  

![Time Series Insights 순서도](media/overview/time-series-insights-flowchart.png)

Time Series Insights에는 다음 4가지 핵심 작업이 포함됩니다.

- Azure IoT Hub 및 Azure Event Hubs 같은 클라우드 게이트웨이와 완전히 통합되었습니다. 이러한 이벤트 소스에 쉽게 연결해서 깨끗한 행 및 열에 있는 데이터가 포함된 메시지 및 구조체에서 JSON을 구문 분석할 수 있습니다. 메타데이터를 원격 분석과 조인하고 열 형식 저장소에 데이터를 인덱싱합니다.
- Time Series Insights는 데이터 스토리지를 관리합니다. 데이터에 항상 쉽게 액세스할 수 있도록 데이터를 메모리 및 SSD에 최대 400일까지 저장합니다. 사용자는 요청 시 수십억 개의 이벤트를 수초 내에 대화식으로 쿼리할 수 있습니다.
- Time Series Insights는 Time Series Insights 탐색기를 통해 즉시 사용 가능한 시각화를 제공합니다.  
- Time Series Insights는 Time Series Insights 탐색기에서, 그리고 시계열 데이터를 사용자 지정 애플리케이션에 포함시키기 위해 쉽게 통합할 수 있는 API를 사용하여 쿼리 서비스를 제공합니다.  

내부에서 사용하거나 외부 고객을 위해 애플리케이션을 빌드하는 경우 Time Series Insights는 시계열 데이터를 인덱싱, 저장 및 집계하기 위한 백 엔드로 사용할 수 있습니다. [클라이언트 SDK](tutorial-explore-js-client-lib.md)를 사용하여 맨 위에 사용자 지정 시각화 및 사용자 환경을 빌드할 수 있습니다. 뿐만 아니라 Time Series Insights는 이러한 사용자 지정 시나리오를 지원하는 여러 [쿼리 API](how-to-shape-query-json.md)를 갖추고 있습니다.  

시계열 데이터는 자산 또는 프로세스가 시간이 지남에 따라 어떻게 달라지는지를 나타냅니다. 따라서 시계열 데이터는 타임스탬프를 통해 인덱싱되며, 시간은 이러한 데이터를 구성하는 기준이 되는 가장 의미 있는 축입니다. 시계열 데이터는 일반적으로 순차적으로 도착하며, 데이터베이스에 결과로 업데이트되지 않고 삽입으로 처리됩니다.

대용량인 경우 시계열 데이터를 저장, 인덱싱, 쿼리, 분석 및 시각화하는 것이 어려울 수 있습니다.
하지만 Azure Time Series Insights는 모든 새 이벤트를 캡처하여 행으로 저장하고, 변경 내용이 시간에 따라 효율적으로 측정되므로 이전 데이터를 보고 인사이트를 얻고 향후 변화를 예측할 수 있습니다.

## <a name="video"></a>비디오

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>클라우드 기반 IoT 분석 플랫폼인 Azure Time Series Insights에 대해 알아보세요.</br>

[![비디오](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>기본 시나리오

- 확장 가능한 방식으로 시계열 데이터 저장.  

   기본적으로 Time Series Insights에서는 시계열 데이터를 고려해서 디자인된 데이터베이스를 제공합니다.  확장 가능하고 완전히 관리되므로 Time Series Insights는 이벤트의 저장과 관리 작업을 처리할 수 있습니다.

- 거의 실시간으로 데이터 탐색.  

   Time Series Insights는 모든 데이터 스트리밍을 단일 환경으로 시각화하는 탐색기를 제공합니다.  이벤트 원본을 연결한 후 즉시 Time Series Insights 내에서 이벤트 데이터를 보고, 탐색하고, 쿼리할 수 있습니다.  이러한 데이터는 디바이스가 예상대로 데이터를 내보내는지 확인하고 IoT 자산의 상태, 생산성 및 전반적인 효율성을 모니터링하는 데 유용합니다.  

- 근본 원인 분석 및 변칙 검색.

   Time Series Insights에는 다단계 근본 원인 분석을 이행하고 저장하기 위한 패턴 및 큐브 뷰와 같은 도구를 제공합니다.  또한 Time Series Insights는 Azure Stream Analytics 등의 경고 서비스와 함께 작동하므로 Time Series Insights 탐색기에서 거의 실시간으로 경고 및 검색된 변칙을 확인할 수 있습니다.  

- 다중 자산/사이트 비교를 위해 완전히 다른 위치에서 바라본 시계열 데이터 스트리밍의 전역 뷰.

   여러 이벤트 원본을 Time Series Insights 환경에 연결할 수 있습니다.  즉, 완전히 다른 여러 위치의 데이터 스트리밍을 거의 실시간으로 함께 볼 수 있습니다.  사용자는 이러한 가시성을 활용하여 비즈니스 경영진과 데이터를 공유할 수 있으며, 전문 지식을 적용할 수 있는 영역별 전문가와 보다 잘 협업함으로써 문제를 해결하고, 모범 사례를 적용하고, 학습한 내용을 공유하는 데 도움을 얻을 수 있습니다.

- Time Series Insights를 토대로 고객 애플리케이션 빌드 

   Time Series Insights는 REST 쿼리 API를 공개하므로 시계열 데이터를 사용하는 애플리케이션을 빌드할 수 있습니다.

## <a name="capabilities"></a>기능

- **빠른 시작**: Azure Time Series Insights는 데이터를 미리 준비할 필요가 없습니다. 수분 내에 Azure IoT Hub 또는 Event Hub에서 수십억 개의 이벤트에 연결합니다. 연결이 되면 센서 데이터를 시각화하고 상호 작용하여 IoT 솔루션의 유효성을 빠르게 검사할 수 있습니다. 코드를 작성하지 않고도 데이터와 상호 작용할 수 있습니다.
또한 고급 사용자에게는 세분화된 자유 텍스트 쿼리 화면을 제공하고, 모든 사용자에게는 가리킨 다음 클릭 탐색 기능을 제공하므로 새로 익혀야 할 언어가 없습니다.

- **거의 실시간에 가까운 인사이트**: Time Series Insights는 1분 대기 시간으로 하루에 수백만 개의 센서 이벤트를 수집할 수 있습니다. Time Series Insights는 추세와 비정상을 찾아내고, 복잡한 근본 원인을 분석하며, 비용이 많이 드는 가동 중지를 방지할 수 있게 함으로써 센서 데이터에 대한 유용한 정보를 얻을 수 있습니다. Time Series Insights는 실시간 데이터와 기록 데이터 간의 상호 상관 관계를 사용하여 데이터에 숨겨진 추세를 나타낼 수 있습니다.

- **사용자 지정 솔루션 빌드**: Azure Time Series Insights 데이터를 기존 애플리케이션에 포함하거나 Time Series Insights REST API를 사용하여 새로운 사용자 지정 솔루션을 만들 수 있습니다. 공유할 수 있는 개인 설정 보기를 만들어 다른 사용자가 여러분이 알아낸 정보를 탐색할 수 있도록 합니다.

- **확장성**: Time Series Insights는 대규모 IoT를 지원하도록 설계되었습니다. 기본 보존 기간을 31일로 설정하면 하루에 1백만 ~ 1억 개의 이벤트를 수신할 수 있습니다. 기록 데이터와 함께 라이브 데이터 스트림을 거의 실시간으로 시각화하고 분석할 수 있습니다. 엔터프라이즈 규모를 수용할 수 있도록 수신 및 보존 속도가 더욱 향상될 것입니다.

## <a name="getting-started"></a>시작

5분 이내에 시작할 수 있습니다.

1. 시작하려면 Azure Portal에서 Time Series Insights 환경을 프로비전합니다.
1. Azure IoT Hub 또는 이벤트 허브와 같은 이벤트 원본을 연결합니다.  
1. 참조 데이터(추가 서비스 아님)를 업로드합니다.
1. Time Series Insights 탐색기를 사용하여 몇 분 만에 데이터를 볼 수 있습니다.

## <a name="time-series-insights-explorer"></a>Time Series Insights 탐색기

이 다이어그램에는 탐색기(![Time Series Insights 탐색기](media/time-series-insights-explorer/explorer4.png))로 확인한 Time Series Insights 데이터 예제가 나와 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Time Series Insights 일반 공급 [무료 데모 환경](./time-series-quickstart.md)을 살펴봅니다.

- [Time Series Insights 계획](time-series-insights-environment-planning.md) 환경에 대해 자세히 알아봅니다.
