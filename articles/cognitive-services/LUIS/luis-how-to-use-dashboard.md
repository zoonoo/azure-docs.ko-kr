---
title: LUIS 앱용 응용 프로그램 대시보드 | Microsoft Docs
description: 한눈에 앱을 모니터링할 수 있는 시각화된 보고 도구인 응용 프로그램 대시보드에 대해 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: diberry
ms.openlocfilehash: 518227d9f4165a08fafefa357de255d97c710f61
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224503"
---
# <a name="application-dashboard"></a>응용 프로그램 대시보드
앱 대시보드를 사용하면 한눈에 앱을 모니터링할 수 있습니다. **내 앱** 페이지에서 응용 프로그램 이름을 클릭하여 앱을 열고 맨 위 패널에서 **대시보드**를 선택하면 **대시보드**가 표시됩니다. 

> [!CAUTION]
> LUIS의 최신 메트릭이 필요한 경우에는 다음을 수행해야 합니다.
> * Azure에서 만든 LUIS [끝점 키](luis-how-to-azure-subscription.md)를 사용합니다.
> * LUIS [API](https://aka.ms/luis-endpoint-apis) 및 봇을 포함한 모든 끝점 요청에 LUIS 끝점 키 사용
> * 각 LUIS 앱에 서로 다른 끝점 키를 사용합니다. 모든 앱에 단일 끝점 키를 사용하지 마세요. 끝점 키는 앱 수준이 아닌 키 수준에서 추적됩니다.  

**대시보드** 페이지에는 시간에 따른 [끝점](luis-glossary.md#endpoint) 사용 현황뿐 아니라 현재 모델 상태를 비롯한 LUIS 앱의 개요가 제공됩니다. <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>앱 상태
대시보드에는 앱이 마지막으로 학습 및 게시된 날짜 및 시간을 비롯하여 응용 프로그램의 학습 및 게시 상태가 표시됩니다.  

![대시보드 - 앱 상태](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>모델 데이터 통계
대시보드에는 앱에 있는 총 의도, 엔터티 및 레이블 지정 발화 수가 표시됩니다. 

![앱 데이터 통계](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>끝점 적중 횟수
대시보드에는 LUIS 앱이 수신하는 총 끝점 적중 횟수가 표시되고 대시보드를 통해 지정한 기간 내의 적중 횟수를 표시할 수 있습니다. 표시되는 총 적중 횟수는 [끝점 키](./luis-concept-keys.md#endpoint-key)를 사용하는 끝점 적중 횟수와 [작성 키](./luis-concept-keys.md#authoring-key)를 사용하는 끝점 적중 횟수의 합계입니다.

<!-- TBD: this image is old but I don't have a new one based on usage -->
![끝점 적중 횟수](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> 최근 끝점 적중 횟수는 Azure Portal의 LUIS 서비스 개요에 있습니다. 
 
### <a name="total-endpoint-hits"></a>총 끝점 적중 횟수
앱 생성 이후 현재 날짜까지 앱에 수신된 총 끝점 적중 횟수입니다.

### <a name="endpoint-hits-per-period"></a>기간별 끝점 적중 횟수
일별로 표시되는 지난 기간 내에 수신된 적중 횟수입니다. 시작 날짜와 종료 날짜 사이의 지점은 이 기간에 포함된 일수를 나타냅니다. 마우스 포인터로 각 지점을 가리키면 기간 내 각 날짜의 적중 횟수가 표시됩니다. 

차트에서 볼 기간을 선택하려면:
 
1. **추가 설정** ![추가 설정 단추](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png)을 클릭하여 기간 목록에 액세스합니다. 1주에서 1년까지의 기간을 선택할 수 있습니다. 

    ![기간별 끝점 적중 횟수](./media/luis-how-to-use-dashboard/timerange.png)

2. 목록에서 기간을 선택한 다음, 뒤로 화살표 ![뒤로 화살표](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 를 클릭하여 차트를 표시합니다.

### <a name="key-usage"></a>키 사용
응용 프로그램의 끝점 키에서 사용된 적중 횟수입니다. 끝점 키에 대한 자세한 내용은 [LUIS의 키](luis-concept-keys.md)를 참조하세요. 
  
## <a name="intent-breakdown"></a>의도 분석
**의도 분석**은 레이블이 지정된 발화 수 또는 끝점 적중 횟수에 따른 의도 분석을 표시합니다. 이 요약 그래프는 앱에서 각 의도의 상대적 중요성을 보여 줍니다. 마우스 포인터로 슬라이스를 가리키면 의도 이름 및 레이블이 지정된 발화 수/끝점 적중 횟수의 합계 중 의도가 나타내는 비율이 표시됩니다. 

![의도 분석](./media/luis-how-to-use-dashboard/intent-breakdown.png)

분석이 레이블이 지정된 발화 수 또는 끝점 적중 횟수를 기준으로 하는지 제어하려면:

1. 다음 이미지와 같이 **추가 설정** ![추가 설정 단추](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png)을 클릭하여 목록에 액세스합니다.

    ![의도 분석 목록](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. 목록에서 값을 선택한 후 뒤로 화살표 ![뒤로 화살표](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 를 클릭하여 차트를 표시합니다.

## <a name="entity-breakdown"></a>엔터티 분석
대시보드에는 레이블이 지정된 발화 수 또는 끝점 적중 횟수에 따른 엔터티 분석이 표시됩니다. 이 요약 그래프는 앱에서 각 엔터티의 상대적 중요성을 보여 줍니다. 마우스 포인터로 슬라이스를 가리키면 엔터티 이름 및 레이블이 지정된 발화 수/끝점 적중 횟수의 합계 중 엔터티 비율이 표시됩니다. 

![엔터티 분석](./media/luis-how-to-use-dashboard/entity-breakdown.png)

분석이 레이블이 지정된 발화 수 또는 끝점 적중 횟수를 기준으로 하는지 제어하려면:

1. 다음 이미지와 같이 **추가 설정** ![추가 설정 단추](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png)을 클릭하여 목록에 액세스합니다.

    ![엔터티 분석 목록](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. 목록에서 값을 선택한 후 뒤로 화살표 ![뒤로 화살표](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 를 클릭하여 이에 따라 차트를 표시합니다.
