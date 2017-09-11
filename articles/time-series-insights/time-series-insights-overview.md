---
title: "Azure Time Series Insights 개요 | Microsoft Docs"
description: "시계열 데이터 분석 및 IoT 솔루션을 위한 새로운 서비스인 Azure Time Series Insights를 소개합니다."
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: abd66208ab7ac30831f3f1eddb2891ed7bcd3995
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="what-is-azure-time-series-insights"></a>Azure Time Series Insights란?

Azure Time Series Insights는 수십억 개의 이벤트를 동시에 쉽게 수집, 저장, 탐색, 분석할 수 있는 저장소, 분석 및 시각화 구성 요소를 갖춘 관리되는 클라우드 서비스입니다. Time Series Insights는 데이터의 전체 보기를 제공하여 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 숨겨진 추세와 비정상을 검색하고 거의 실시간으로 근본 원인 분석을 수행할 수 있도록 하여 비용이 많이 드는 장치 가동 중지를 방지할 수 있습니다. Time Series Insights는 이벤트 브로커(예: IoT Hub 또는 Event Hubs)에서 시계열 데이터를 수집하고, 데이터를 인덱싱하고, 구성 가능한 보존 정책에 따라 데이터를 폐기합니다. 사용자는 직관적인 UX 또는 REST 쿼리 API를 통해 데이터를 사용합니다.

![Time Series Insight 개요](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>기본 시나리오

* 수분 내에 IoT 솔루션을 모니터링하고 유효성을 검사합니다.
* 대용량 IoT 데이터를 시각화하고 분석합니다.
* 근본 원인 분석 및 변칙 검색을 빠르게 수행합니다.
* 여러 장치, 장비 및 데이터의 전체 보기를 만듭니다.

## <a name="capabilities-and-benefits"></a>기능 및 이점

* **쉬운 시작**: Azure Time Series Insights는 데이터를 미리 준비할 필요가 없으며 매우 빠릅니다. 수분 내에 Azure IoT Hub 또는 Event Hub에서 수십억 개의 이벤트에 연결합니다. 연결이 되면 수초 내에 센서 데이터를 시각화하고 상호 작용하여 IoT 솔루션의 유효성을 빠르게 검사할 수 있습니다. Time Series Insights는 한 줄의 코드도 작성하지 않고 데이터와 상호 작용할 수 있으므로 사용하기가 쉽습니다.  또한 고급 사용자에게는 세분화된 자유 텍스트 쿼리 화면을 제공하고, 모든 사용자에게는 가리킨 다음 클릭 탐색 기능을 제공하므로 새로 익혀야 할 언어가 없습니다.

* **거의 실시간에 가까운 정보**: Time Series Insights는 1분 대기 시간으로 하루에 수억 개의 센서 이벤트를 수집할 수 있으므로 변화에 빠르게 대응할 수 있습니다. Time Series Insights는 추세와 비정상을 빠르게 찾아내고, 복잡한 근본 원인을 분석하며, 비용이 많이 드는 가동 중지를 방지할 수 있게 함으로써 센서 데이터에 대한 심층적인 정보를 얻을 수 있습니다. Time Series Insights는 실시간 데이터와 기록 데이터 간의 상호 상관 관계를 사용하여 데이터에 숨겨진 추세를 나타낼 수 있습니다.

* **사용자 지정 솔루션 빌드**: Azure Time Series Insights 데이터를 기존 응용 프로그램에 포함하거나 Time Series Insights REST API를 사용하여 새로운 사용자 지정 솔루션을 만들 수 있습니다. 다른 사용자가 검색 결과를 공유하여 탐색할 수 있는 개인 설정 보기를 만들고 공유합니다.

* **확장성**: Time Series Insights는 대용량 IoT를 지원하도록 설계되었습니다. 미리 보기에서 기본 보존 기간을 31일로 설정하면 하루에 1백만 ~ 1억 개의 이벤트를 수신할 수 있습니다. 방대한 양의 기록 데이터와 함께 라이브 데이터 스트림을 거의 실시간으로 시각화하고 분석할 수 있습니다. 계속 진화하는 엔터프라이즈 규모를 수용할 수 있도록 전달, 수신 및 고객 보유율이 향상될 것입니다.

## <a name="time-series-insights-glossary"></a>Time Series Insights 용어

* **환경**: 환경은 수신 및 저장 용량이 있는 Azure 리소스입니다.  고객은 필요한 용량을 갖춘 Azure Portal을 통해 환경을 프로비전합니다.
* **이벤트 원본**: 이벤트 원본은 Azure Event Hubs 같은 이벤트 브로커에서 파생됩니다.  Time Series Insights는 이벤트 원본에 직접 연결하여 코드를 작성하지 않고 데이터 스트림을 수집합니다. 현재 Time Series Insights는 Azure Event Hubs 및 Azure IoT Hubs를 지원합니다.
* **참조 데이터**: Time Series Insights는 사용자에게 시계열 데이터를 참조 데이터와 조인하는 기능을 제공합니다.  참조 데이터는 장치에 대한 메타데이터 또는 비교적 자주 변경되지 않는 다른 정적 데이터를 포함할 수 있습니다. Time Series Insights는 사용자가 데이터를 거의 실시간으로 시각화하고 분석할 수 있도록 참조 데이터를 데이터 스트림과 조인합니다.

