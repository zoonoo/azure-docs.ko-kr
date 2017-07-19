---
title: Azure Application Insights Funnels
description: "Funnels를 사용하여 고객이 응용 프로그램과 상호 작용하는 방법을 검색하는 방법을 알아봅니다."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 85f47daaaff8967eb83c330bab839023f128b486
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017

---

# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Application Insights Funnels를 사용하여 고객이 응용 프로그램을 사용하는 방법 검색

고객 환경을 이해하는 것이 비즈니스에 가장 중요합니다. 응용 프로그램이 여러 단계와 관련된 경우, 고객 대부분이 전체 프로세스를 거치는지 아니면 특정 시점에서 프로세스를 종료하는지를 알아야 합니다. 웹 응용 프로그램에서 일련의 단계를 거치는 것을 “깔때기”라고 합니다. Application Insights Funnels를 사용하여 사용자에 대한 정보를 얻고 단계별 전환율을 모니터링할 수 있습니다. 

## <a name="get-started-with-the-funnels-blade"></a>Funnels 블레이드 시작
Funnels에 대해 알아보는 가장 쉬운 방법은 예제를 살펴보는 것입니다. 다음 그림에서는 전자상거래 비즈니스 소유자가 고객이 자사 웹 응용 프로그램과 상호 작용하는 방법을 알아보기 위해 수행하는 단계를 보여 줍니다.  

### <a name="create-your-funnel"></a>깔때기 만들기
깔때기를 만들기 전에 먼저 답변하려는 질문에 관해 결정해야 합니다. 예를 들어 홈페이지를 보는 고객 중 광고를 클릭하는 고객이 얼마나 되는지를 알고 싶을 수 있습니다. 이 예제에서는 Fabrikam Fiber 회사 소유자가 지난 달에 쇼핑 카트에 항목을 추가한 후 구매한 고객의 비율을 알고자 합니다.

깔때기를 만들기 위해 수행하는 단계는 다음과 같습니다.

1. Funnels 블레이드에서 새로 만들기 단추를 클릭합니다.
1. **시간 범위** 드롭다운에서 “지난 달” 시간 범위를 선택합니다. 
1. **1단계** 드롭다운 목록에서 **제품 페이지** 이벤트를 선택합니다. 
1. **2단계** 드롭다운 목록에서 **쇼핑 카트에 추가** 이벤트를 선택합니다.
1. **3단계** 드롭다운 목록에서 **구매 클릭** 이벤트를 선택합니다.
1. 깔때기에 이름을 추가하고 **저장**을 클릭합니다.

다음 그림에서는 Funnels 블레이드에서 생성하는 데이터를 보여 줍니다. 여기서 Fabrikam 소유자는 지난 주 동안 쇼핑 카트에 항목을 추가한 고객의 22.7%가 구매를 완료한 것을 알 수 있습니다. 또한 고객의 1%는 제품 페이지를 방문하기 전에 광고를 클릭했으며, 고객의 20%는 구매를 완료한 후 로그아웃한 것을 알 수 있습니다.


![데이터가 포함된 Funnels 블레이드](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>다음 단계
- [사용량 분석](app-insights-usage-overview.md)에 대해 자세히 알아봅니다. 

