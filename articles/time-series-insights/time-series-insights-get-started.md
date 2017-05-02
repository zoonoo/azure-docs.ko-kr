---
title: "Azure Time Series Insights 환경 만들기 | Microsoft Docs"
description: "이 자습서에서는 몇 분 안에 Time Series 환경을 만들고, 이벤트 원본에 연결하고, 이벤트 데이터 분석을 준비하는 방법을 배웁니다."
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
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: eb710795916a2d7beea75a6408a0982fb4dc8750
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Azure Portal에서 Time Series Insights 환경 만들기

Time Series Insights 환경은 수신 및 저장 기능이 있는 Azure 리소스입니다. 고객은 Azure Portal을 통해 환경에 필요한 용량을 프로비전합니다.

## <a name="steps-to-create-the-environment"></a>환경을 만드는 단계

다음 단계에 따라 환경을 만듭니다.

1.    [Azure 포털](https://portal.azure.com)에 로그인합니다.
2.    왼쪽 위 모퉁이에서 더하기 기호(“+”)를 클릭합니다.
3.    검색 상자에서 "Time Series Insights"를 검색합니다.

  ![Time Series Insights 환경 만들기](media/get-started/getstarted-create-environment1.png)

4.    “Time Series Insights”를 선택하고 “만들기”를 클릭합니다.

  ![Time Series Insights 리소스 그룹 만들기](media/get-started/getstarted-create-environment2.png)

5.    환경 이름을 지정합니다. 이 이름은 [time series 탐색기](https://insights.timeseries.azure.com)에서 환경을 나타냅니다.
6.    구독을 선택합니다. 이벤트 원본이 포함된 구독을 선택해야 합니다. Time Series Insights는 같은 구독에 있는 Azure IoT Hub 및 Event Hub 리소스를 자동으로 검색할 수 있습니다.
7.    리소스 그룹을 선택하거나 만듭니다. 리소스 그룹은 함께 사용되는 Azure 리소스 컬렉션입니다.
8.    호스팅 위치를 선택합니다. 데이터 센터 간에 데이터를 이동하지 못하게 하려면 이벤트 원본이 포함된 위치를 선택합니다.
9.    가격 책정 계층을 선택합니다.
10.    용량을 선택합니다. 환경을 만든 후 환경의 용량을 변경할 수 있습니다.
11.    환경을 만듭니다. 로그인할 때마다 손쉽게 액세스할 수 있도록 환경을 대시보드에 고정할 수도 있습니다.

  ![Time Series Insights를 만들고 대시보드에 고정](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>다음 단계

* [Time Series Insights 포털](https://insights.timeseries.azure.com)에서 환경에 액세스하도록 [데이터 액세스 정책 정의](time-series-insights-data-access.md)
* [이벤트 원본 만들기](time-series-insights-add-event-source.md)
* 이벤트 원본으로 [이벤트 보내기](time-series-insights-send-events.md)

