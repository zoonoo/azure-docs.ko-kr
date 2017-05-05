---
title: "Azure Time Series Insights 개요 | Microsoft Docs"
description: "시계열 데이터 분석 및 IoT 솔루션을 위한 새로운 서비스인 Azure Time Series Insight를 소개"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.lasthandoff: 04/26/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Azure Time Series Insights 개요

Azure Time Series Insights는 동시에 수십억 개의 이벤트를 놀랄 만큼 간단하게 수집, 저장, 탐색, 분석할 수 있는 저장, 분석 및 시각화 구성 요소를 갖고 있는 완벽화게 관리되는 클라우드 서비스입니다. Time Series Insights는 데이터에 대한 글로벌 보기를 제공하므로 사용자는 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 숨겨진 동향과 문제를 찾아내고, 거의 실시간으로 근본 원인 분석을 수행할 수 있도록 지원하는 기능을 통해, 큰 비용이 수반되는 중요 업무용 장치의 가동 중지를 방지할 수 있습니다. Time Series Insights는 이벤트 브로커(예: IoT Hub 또는 Event Hubs)에서 시계열 데이터를 수집하고, 데이터를 인덱싱하고, 구성 가능한 보존 정책에 따라 데이터를 폐기합니다. 사용자는 직관적인 UX 또는 REST 쿼리 API를 통해 해당 비즈니스 시나리오에 데이터를 사용합니다.

![Time Series Insight 개요](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>기본 시나리오

* 몇 분 만에 IoT 솔루션의 유효성을 검사하고 모니터링
* 규모에 관계없이 IoT 데이터의 직관적인 시각화 및 분석
* 신속한 근본 원인 분석 및 이상 감지
* 여러 장치, 시설 및 데이터의 전역 보기 생성

## <a name="key-capabilities-and-benefits"></a>주요 기능 및 이점

* **간편한 시작**: Azure Time Series Insights는 사전 데이터 준비가 필요 없고 놀랍도록 빠르기 때문에 Azure IoT Hub 또는 Event Hub의 이벤트 수십억 개를 몇 분이면 연결할 수 있습니다. 이벤트를 연결할 후에는 몇 초 내에 센서 데이터를 시각화하고 해당 데이터와 상호 작용하여 IoT 솔루션의 유효성을 신속하게 확인할 수 있습니다. Time Series Insights는 매우 직관적이고 사용 방법이 간단합니다. 코드를 전혀 작성하지 않고도 데이터를 조작할 수 있습니다.  또한 Time Series Insights는 고급 사용자를 위한 세밀하고 텍스트 없는 쿼리 화면은 물론이고 초보자를 위한 가리키고 클릭 탐색 방법을 제공하기 때문에 새 언어를 배우지 않아도 됩니다.

* **거의 실시간으로 몇 초 안에 고급 정보 제공**: 한 장소에서 Time Series Insights의 저장, 분석 및 시각화 구성 요소를 통해 시계열 데이터에서 최대한 많은 가치를 얻어낼 수 있습니다. Time Series Insights는 하루에 수억 개의 센서 이벤트를 수집할 수 있고 대기 시간이 1분에 불과하므로 변화에 신속하게 대응할 수 있습니다. Time Series Insights는 추세와 이상 현상을 신속하게 발견하고, 복잡한 근본 원인 분석을 수행하고, 비용이 많이 드는 가동 중지를 예방할 수 있게 지원함으로써 센서 데이터에 대한 심층 정보를 제공합니다. Time Series Insights는 사용자가 데이터 속에 숨겨진 추세를 발견할 수 있도록 실시간 데이터와 기록 데이터 간에 상관 관계를 사용합니다.

* **사용자 지정 솔루션 빌드**: Azure Time Series Insights 데이터를 기존 응용 프로그램에 포함하거나 Time Series Insights REST API를 사용하여 새로운 사용자 지정 솔루션을 만들 수 있습니다.  또한 매우 간단하게 개인 설정된 보기를 만들어서 공유할 수 있으며, 이렇게 하면 사용자가 발견한 정보를 다른 사람들도 간단하게 탐색할 수 있습니다.

* **확장성**: Time Series Insights는 IoT 확장을 지원하도록 설계되었습니다. 미리 보기에서는 하루에 1백만~1억 이벤트를 수신할 수 있고, 기본 보존 기간은 31일입니다. Time Series Insights를 사용하면 사용자가 방대한 양의 기록 데이터와 함께 실시간 데이터 스트림을 거의 실시간으로 시각화하고 분석할 수 있습니다. 점점 늘어나는 엔터프라이즈 규모를 수용할 수 있도록 수신 및 보존 속도가 더욱 향상될 것입니다.

## <a name="taxonomy-of-time-series-insights"></a>Time Series Insights의 분류

* **환경**: 환경은 수신 및 저장 용량이 있는 Azure 리소스입니다.  고객은 Azure Portal을 통해 환경에 필요한 용량을 프로비전합니다.
* **이벤트 원본**: 이벤트 원본은 Azure Event Hubs 같은 이벤트 브로커에서 파생됩니다.  Time Series Insights는 이벤트 원본에 직접 연결하여 데이터 스트림을 수집하므로 사용자가 코드를 작성할 필요가 전혀 없습니다. 현재 Time Series Insights는 Azure Event Hubs와 Azure IoT Hubs를 지원하며, 향후 더 많은 이벤트 원본이 추가될 예정입니다.
* **참조 데이터**: Time Series Insights는 사용자에게 시계열 데이터를 참조 데이터와 조인하는 기능을 제공합니다.  참조 데이터는 장치에 대한 메타데이터 또는 비교적 자주 변경되지 않는 다른 정적 데이터를 포함할 수 있습니다. Time Series Insights는 사용자가 데이터를 거의 실시간으로 시각화하고 분석할 수 있도록 참조 데이터를 데이터 스트림과 조인합니다.

