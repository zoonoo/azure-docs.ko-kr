---
title: Web Analytics | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 89cc8c4bffe910de0861d7f44925a10df3811fdb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097477"
---
<a name="web-analytics"></a>Web Analytics
=============

이 문서에서는 최적의 방식으로 사업을 확장하기 위해 Web Analytics를 학습하고 사용하는 방법의 지침을 제공합니다. 현재는 모든 AppSource 제품에서 이 Insights 탭이 제공됩니다.

제품을 빌드하고 게시한 다음에 진행하는 과정은 제품의 판매 실적을 추적하고 측정하는 것입니다. **Web Analytics**를 통해 Marketplace에서 각 제품의 판매 실적을 정확하게 확인할 수 있는 기능이 추가되었습니다. 이 과정을 시작하려면 클라우드 파트너 포털 왼쪽의 Insights 페이지로 이동하여 새롭게 제공되는 Analytics 탭을 확인합니다.

![WebAnalytics 페이지](./media/si-getting-started/WebAnalytics1.png)

게시자 ID에 대해 다양한 정보가 포함된 대시보드가 표시됩니다. Microsoft Power BI로 작성된 이 대시보드에서는 각 제품의 데이터(매일 갱신됨)를 확인할 수 있습니다.

<a name="microsoft-campaigns"></a>**Microsoft 캠페인**
-----------------------

제품 판매량을 늘리고 판매량 증가를 추적할 수 있도록 클라우드 파트너 포털에서 **Microsoft 캠페인**을 사용하는 기능이 설정되었습니다. Marketplace에서 새롭게 지원되는 기능인 캠페인에서는 고객이 앱 세부 정보 페이지로 이동하는 다양한 채널을 추적할 수 있습니다.

### <a name="how-to-make-a-campaign"></a>**캠페인을 만드는 방법**

캠페인을 설명하는 가장 단순한 방식은 Marketplace의 앱 세부 정보 페이지로 이동하는 URL에 사용자 지정 단어/용어를 추가하는 것입니다. Google, Bing, LinkedIn 및 기타 여러 사이트에서는 광고를 작성하여 자사 사이트를 원하는 사이트에 연결하는 방식을 권장합니다.

일반적으로 이러한 방식을 사용하면 제품의 신규 고객을 확보할 수 있으며, 각 채널의 실적을 측정할 수 있어야 합니다. 이러한 경우에 캠페인을 사용할 수 있습니다.

다음의 두 가지 방식으로 캠페인을 직접 생성할 수 있습니다.

1. 캠페인의 내용과 고객이 유입되는 페이지/이벤트를 설명하는 쿼리 매개 변수 **mktcmpid**를 URL에 추가합니다.

예를 들어 다음과 같은 주소를 사용할 수 있습니다. <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (고급): URL에는 지원 되는 제네릭 캠페인 Id 중 하나를 사용 합니다. 사용해야 하는 추가 ref 태그를 포함할 수 있도록 다음의 추가 태그를 자동으로 인식하는 규칙이 지원됩니다.
    
    1. **utm\_campaign**
    2. **utm\_source**
    3. **ref**
    4. **src**

예를 들어 다음과 같은 주소를 사용할 수 있습니다. <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

이러한 캠페인 ID를 여러 개 조합하여 캠페인의 트래픽을 생성하는 여러 출처(예: 전자 메일, 블로그, 소셜 미디어 소스 등 고객이 유입되는 위치)를 추가로 지정할 수도 있습니다.

예: 

1. 뉴스레터 참조 페이지: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn 참조 페이지: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**모든 페이지를 통해 캠페인 전달**

캠페인의 중간 페이지로 트래픽을 전달한 다음 해당 페이지에서 계속해서 고객을 Marketplace로 이동시키는 시나리오도 있을 수 있습니다. 이러한 경우 Marketplace로 보내는 최종 URL로 초기 캠페인 ID를 전달해야 합니다.

다음은 예제입니다.

1. 마케팅 담당 직원이 회사 방문 페이지 <https://contoso.com>으로 트래픽을 전달하기 위해 Google에서 광고를 구매합니다. 이 방문 페이지에는 <https://appsource.com>으로 이동하는 \"제품 사용해 보기\" 링크가 있습니다.
2. 사용자가 광고를 클릭하여 회사 방문 페이지를 방문합니다.
    1.  추천 URL = google.com
    2.  방문 페이지 URL = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. 사용자가 \"제품 사용해 보기\" 링크를 클릭하여 AppSource로 이동합니다.
    1. 추천 URL = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. 방문 페이지 URL(**이 URL에는 utm\_campaign 및 utm\_이 URL에 추가된 소스**가 있어야 함) = [https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**](https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>캠페인의 성공 여부를 평가하는 방법
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**캠페인별 페이지 방문 횟수**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

페이지 방문자가 유입되는 캠페인별 일일 페이지 방문 횟수 내역입니다.

### <a name="conversion-rate-by-campaign"></a>**캠페인별 전환율**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

전체 제품의 변환율이 표시되는 방식과 마찬가지로, 이 차트에서는 각 캠페인별 변환율 내역을 확인할 수 있습니다. 이 차트를 통해 변환율이 높은 캠페인의 방문자 유입 경로를 파악할 수 있습니다.

### <a name="distribution-by-campaign"></a>**캠페인별 분포**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

고객 도메인을 확인하는 방식과 마찬가지로, 이 차트에서는 사용자가 Marketplace를 방문하는 캠페인별 데이터 분포를 확인할 수 있습니다. \_NoCampaign는 고객이 Marketplace로 이동할 때 url에 캠페인 ID가 없었다는 의미입니다.

<a name="next-steps"></a>**다음 단계**
--------------

제품의 실적을 추적하는 방법을 알아보았으므로 캠페인을 직접 만들어 보시기 바랍니다.

궁금한 점이나 요청하고자 하는 기능이 있으면 오른쪽 아래의 피드백을 통해 알려 주시기 바랍니다.

![클라우드 파트너 포털 피드백](./media/si-getting-started/WebAnalytics5.png)
