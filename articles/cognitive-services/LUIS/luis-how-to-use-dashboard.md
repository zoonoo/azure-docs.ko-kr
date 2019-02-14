---
title: 앱 대시보드
titleSuffix: Language Understanding - Azure Cognitive Services
description: 한눈에 앱을 모니터링할 수 있는 시각화된 보고 도구인 애플리케이션 대시보드에 대해 알아봅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 8927213dc4f1cf01eccdd8f54a946f9dbbaaa04d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869962"
---
# <a name="model-and-usage-statistics-in-the-dashboard"></a>대시보드에서 모델 및 사용 현황 통계
앱 대시보드를 사용하면 한눈에 앱을 모니터링할 수 있습니다. **내 앱** 페이지에서 애플리케이션 이름을 클릭하여 앱을 열고 맨 위 패널에서 **대시보드**를 선택하면 **대시보드**가 표시됩니다. 

> [!CAUTION]
> LUIS의 최신 메트릭이 필요한 경우에는 다음을 수행해야 합니다.
> * Azure에서 만든 LUIS [엔드포인트 키](luis-how-to-azure-subscription.md)를 사용합니다.
> * LUIS [API](https://aka.ms/luis-endpoint-apis) 및 봇을 포함한 모든 엔드포인트 요청에 LUIS 엔드포인트 키 사용
> * 각 LUIS 앱에 서로 다른 엔드포인트 키를 사용합니다. 모든 앱에 단일 엔드포인트 키를 사용하지 마세요. 엔드포인트 키는 앱 수준이 아닌 키 수준에서 추적됩니다.  

**대시보드** 페이지에는 시간에 따른 [엔드포인트](luis-glossary.md#endpoint) 사용 현황뿐 아니라 현재 모델 상태를 비롯한 LUIS 앱의 개요가 제공됩니다. 
  
## <a name="app-status"></a>앱 상태
대시보드에는 앱이 마지막으로 학습 및 게시된 날짜 및 시간을 비롯하여 애플리케이션의 학습 및 게시 상태가 표시됩니다.  

![대시보드 - 앱 상태](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>모델 데이터 통계
대시보드에는 앱에 있는 총 의도, 엔터티 및 레이블 지정 발화 수가 표시됩니다. 

![앱 데이터 통계](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>엔드포인트 적중 횟수
대시보드에는 LUIS 앱이 수신하는 총 엔드포인트 적중 횟수가 표시되고 대시보드를 통해 지정한 기간 내의 적중 횟수를 표시할 수 있습니다. 표시되는 총 적중 횟수는 [엔드포인트 키](./luis-concept-keys.md#endpoint-key)를 사용하는 엔드포인트 적중 횟수와 [작성 키](./luis-concept-keys.md#authoring-key)를 사용하는 엔드포인트 적중 횟수의 합계입니다.

![엔드포인트 적중 횟수](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> 최근 엔드포인트 적중 횟수는 Azure Portal의 LUIS 서비스 개요에 있습니다. 
 
### <a name="total-endpoint-hits"></a>총 엔드포인트 적중 횟수
앱 생성 이후 현재 날짜까지 앱에 수신된 총 엔드포인트 적중 횟수입니다.

### <a name="endpoint-hits-per-period"></a>기간별 엔드포인트 적중 횟수
일별로 표시되는 지난 기간 내에 수신된 적중 횟수입니다. 시작 날짜와 종료 날짜 사이의 지점은 이 기간에 포함된 일수를 나타냅니다. 마우스 포인터로 각 지점을 가리키면 기간 내 각 날짜의 적중 횟수가 표시됩니다. 

차트에서 볼 기간을 선택하려면:
 
1. **추가 설정** ![추가 설정 단추](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png)을 클릭하여 기간 목록에 액세스합니다. 1주에서 1년까지의 기간을 선택할 수 있습니다. 

    ![기간별 엔드포인트 적중 횟수](./media/luis-how-to-use-dashboard/timerange.png)

2. 목록에서 기간을 선택한 다음, 뒤로 화살표 ![뒤로 화살표](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 를 클릭하여 차트를 표시합니다.

### <a name="key-usage"></a>키 사용
애플리케이션의 끝점 키에서 사용된 적중 횟수입니다. 끝점 키에 대한 자세한 내용은 [LUIS의 키](luis-concept-keys.md)를 참조하세요. 
  
## <a name="intent-breakdown"></a>의도 분석
**의도 분석**은 레이블이 지정된 발화 수 또는 엔드포인트 적중 횟수에 따른 의도 분석을 표시합니다. 이 요약 그래프는 앱에서 각 의도의 상대적 중요성을 보여 줍니다. 마우스 포인터로 슬라이스를 가리키면 의도 이름 및 레이블이 지정된 발화 수/끝점 적중 횟수의 합계 중 의도가 나타내는 비율이 표시됩니다. 

![의도 분석](./media/luis-how-to-use-dashboard/intent-breakdown.png)

분석이 레이블이 지정된 발화 수 또는 엔드포인트 적중 횟수를 기준으로 하는지 제어하려면:

1. 다음 이미지와 같이 목록에 액세스하려면 **추가 설정** ![추가 설정 단추를 클릭하여 목록에 액세스합니다](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png).

    ![의도 분석 목록](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. 목록에서 값을 선택한 후 뒤로 화살표 ![뒤로 화살표](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 를 클릭하여 차트를 표시합니다.

## <a name="entity-breakdown"></a>엔터티 분석
대시보드에는 레이블이 지정된 발화 수 또는 엔드포인트 적중 횟수에 따른 엔터티 분석이 표시됩니다. 이 요약 그래프는 앱에서 각 엔터티의 상대적 중요성을 보여 줍니다. 마우스 포인터로 슬라이스를 가리키면 엔터티 이름 및 레이블이 지정된 발화 수/끝점 적중 횟수의 합계 중 엔터티 비율이 표시됩니다. 

![엔터티 분석](./media/luis-how-to-use-dashboard/entity-breakdown.png)

분석이 레이블이 지정된 발화 수 또는 엔드포인트 적중 횟수를 기준으로 하는지 제어하려면:

1. 다음 이미지와 같이 목록에 액세스하려면 **추가 설정** ![추가 설정 단추를 클릭하여 목록을 가져옵니다](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png).

    ![엔터티 분석 목록](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. 목록에서 값을 선택한 후 뒤로 화살표 ![뒤로 화살표](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 를 클릭하여 이에 따라 차트를 표시합니다.
