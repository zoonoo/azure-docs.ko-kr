---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 4e19c1afefdc5bcacebcb0d495193b48c7a6d724
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313713"
---
Bing Statistics는 Bing Search API에 대한 분석을 제공합니다. 분석에는 호출량, 주요 쿼리 문자열, 지리적 분포 등이 포함됩니다. Bing Search 유료 구독에서 Bing Statistics를 사용하도록 설정하려면 [Azure 대시보드](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7)로 이동한 후 유료 구독을 선택하고 Bing Statistics 사용을 클릭합니다. Bing Statistics를 사용하도록 설정하면 구독 속도가 약간 늘어납니다([가격 책정](https://aka.ms/bingstatisticspricing) 참조).


> [!NOTE]
> Bing Statistics는 유료 구독에서만 사용 가능하고 평가판 구독에서는 사용할 수 없습니다. 

> [!NOTE]
> 제3자에게 배포할 응용 프로그램을 만들 때는 Bing Statistics 대시보드를 통해 사용 가능한 어떤 데이터도 사용할 수 없습니다.

Bing은 24시간마다 분석 데이터를 업데이트하고 최대 13개월 동안의 기록을 유지 관리합니다.

## <a name="accessing-your-analytics"></a>분석 액세스

분석 대시보드에 액세스하려면 https://bingapistatistics.com으로 이동합니다. 유료 구독을 가져오는 데 사용할 계정과 동일한 MSA(Microsoft 계정)를 사용하여 로그인되어 있는지 확인합니다.


## <a name="filtering-the-data"></a>데이터 필터링

기본적으로 차트와 그래프는 사용자가 액세스할 수 있는 모든 메트릭 데이터를 반영합니다. 관심 있는 리소스, 지역/국가, 끝점 및 보고 기간을 선택하여 차트 및 그래프에 표시되는 데이터를 필터링할 수 있습니다. 차트와 그래프는 적용한 필터에 맞게 변경됩니다. 다음은 변경할 수 있는 필터에 대한 설명입니다.

- **리소스 ID**: Azure 구독을 식별하는 고유한 리소스 ID입니다. 둘 이상의 Bing Search API 계층을 구독하는 경우 이 목록에는 여러 ID가 포함됩니다. 기본적으로 모든 리소스가 선택됩니다.  
  
- **지역/국가**: 결과가 나오는 지역/국가입니다. 예를 들면 en-us(영어, 미국)와 같습니다. 기본적으로 모든 지역/국가가 선택됩니다. en-WW 지역/국가는 호출에 지역/국가가 지정되지 않고 Bing에서 사용자의 지역/국가를 확인할 수 없는 경우 사용되는 지역/국가입니다.  
  
- **끝점**: Bing Search API 끝점입니다. 목록에는 유료 구독이 있는 모든 끝점이 포함됩니다. 기본적으로 모든 끝점이 선택됩니다.  

- **시간 프레임**: 보고 기간입니다. 다음을 지정할 수 있습니다.  
  
  - 모두&mdash;최대 13개월 동안의 데이터가 포함됩니다.  
  - 지난 24시간&mdash;지난 24시간 동안의 분석이 포함됩니다.  
  - 지난 주&mdash;이전 7일의 분석이 포함됩니다.  
  - 지난 달&mdash;이전 30일의 분석이 포함됩니다.  
  - 사용자 지정 날짜 범위&mdash;사용 가능한 경우 지정된 날짜 범위에서 분석이 포함됩니다.  
  
  > [!NOTE]  
  > 대시보드에 메트릭이 표시되는 데 최대 24시간이 걸릴 수 있습니다. 대시보드에는 데이터가 마지막으로 업데이트된 날짜 및 시간이 표시됩니다.  
  
  > [!NOTE]  
  > 메트릭은 Bing Statistics 추가 기능을 사용하도록 설정할 때부터 사용할 수 있습니다. 


## <a name="charts-and-graphs"></a>차트 및 그래프

대시보드에는 선택한 끝점에 사용할 수 있는 메트릭의 차트 및 그래프가 표시됩니다. 모든 끝점에 모든 메트릭을 사용할 수 있는 것은 아닙니다. 각 끝점의 차트와 그래프는 정적입니다(표시할 차트 및 그래프를 선택할 수 없음). 대시보드에는 데이터가 있는 차트 및 그래프만 표시됩니다. 

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

가능한 메트릭은 다음과 같습니다. 각 메트릭은 끝점 제한 사항을 나타냅니다. 

- **호출량**: 보고 기간 동안 수행된 호출 수를 표시합니다. 보고 기간이 하루 동안인 경우 차트에는 시간당 호출 수가 표시됩니다. 그렇지 않은 경우, 차트에는 보고 기간 중 하루당 호출 수가 표시됩니다.  
  
  > [!NOTE]
  > 호출량은 청구 보고서마다 다를 수 있으며 일반적으로는 성공한 호출만 포함됩니다. 
  
-  **상위 쿼리**: 보고 기간 동안의 상위 쿼리 및 각 쿼리의 발생 수가 표시됩니다. 표시된 쿼리의 수를 구성할 수 있습니다. 예를 들어, 상위 25, 50 또는 75개 쿼리를 표시할 수 있습니다. 다음 끝점에 대해서는 상위 쿼리를 사용할 수 없습니다.  
  
  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > 일부 쿼리 용어는 전자 메일, 전화 번호, SSN 등의 기밀 정보를 제거하도록 표시되지 않을 수 있습니다.  

- **지리적 배포**: 결과가 나오는 지역/국가입니다. 예를 들면 en-us(영어, 미국)와 같습니다. Bing에서는 `mkt` 쿼리 매개 변수를 사용하여 지역/국가(지정된 경우)를 결정합니다. 그렇지 않은 경우, Bing은 호출자의 IP 주소와 같은 신호를 사용하여 지역/국가를 결정합니다.  
  
- **응답 코드 배포**: 보고 기간 동안 수행된 모든 호출에 대한 HTTP 상태 코드입니다.  
  
- **호출 원본 배포**: 사용자가 사용하는 브라우저 유형입니다. 예를 들면 Edge, Chrome, Safari 및 FireFox와 같습니다. 봇, Postman과 같은 외부 브라우저나 콘솔 앱의 curl을 사용하여 수행되는 호출은 라이브러리 아래에 그룹화됩니다. 원본은 요청의 사용자 에이전트 헤더 값을 사용하여 결정됩니다. 요청에 사용자 에이전트 헤더가 포함되지 않을 경우 Bing은 다른 신호에서 원본을 파생하려고 시도합니다.  
  
- **안전 검색 배포**: 안전 검색 값의 배포입니다. 예를 들면 off, moderate 또는 strict입니다. `safeSearch` 쿼리 매개 변수는 값(지정된 경우)을 포함합니다. 이렇게 하지 않으면 Bing은 기본적으로 moderate 값을 사용합니다.  
  
- **요청된 답변 배포**: `responseFilter` 쿼리 매개 변수에서 사용자가 요청한 Web Search API 답변입니다.  
  
- **반환된 답변 배포**: Web Search API가 응답에 반환한 답변입니다.  
  
- **응답 서버 배포**: API 요청을 처리하는 응용 프로그램 서버입니다. 가능한 값은 Bing.com(데스크톱 및 랩톱 디바이스에서 제공된 트래픽) 및 Bing.com-mobile(모바일 디바이스에서 제공된 트래픽)입니다. 서버는 요청의 사용자 에이전트 헤더 값을 사용하여 결정됩니다. 요청에 사용자 에이전트 헤더가 포함되지 않을 경우 Bing은 다른 신호에서 서버를 파생하려고 시도합니다.  
  


다음은 각 끝점에 사용할 수 있는 분석입니다.

![끝점 지원 매트릭스별 배포](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)


