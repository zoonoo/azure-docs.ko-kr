---
title: "Azure Time Series Insights 환경에 참조 데이터 집합을 추가하는 방법 | Microsoft Docs"
description: "이 문서에서는 Azure Time Series Insights 환경에 참조 데이터 집합을 추가하는 방법을 설명합니다. 참조 데이터는 값을 확장하기 위해 원본 데이터에 연결하는 데 유용합니다."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Azure Portal을 사용하여 Time Series Insights 환경에 대한 참조 데이터 집합 만들기

이 문서에서는 Azure Time Series Insights 환경에 참조 데이터 집합을 추가하는 방법을 설명합니다. 참조 데이터는 값을 확장하기 위해 원본 데이터에 연결하는 데 유용합니다.

참조 데이터 집합은 이벤트 원본의 이벤트로 보강된 항목의 컬렉션입니다. Time Series Insights 수신 엔진은 이벤트 원본의 이벤트와 참조 데이터 집합의 항목을 조인합니다. 이렇게 보강된 이벤트는 쿼리에 사용할 수 있습니다. 이 조인은 참조 데이터 집합에서 정의된 키를 기반으로 합니다.

## <a name="add-a-reference-data-set"></a>참조 데이터 집합 추가

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 기존 Time Series Insights 환경을 찾습니다. Azure Portal의 왼쪽에 있는 메뉴에서 **모든 리소스**를 클릭합니다. Time Series Insights 환경을 선택합니다.

3. **환경 토폴로지** 제목 아래에서 **참조 데이터 집합**을 선택합니다.

    ![Time Series Insights 참조 데이터 집합 만들기](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. **+ 추가**를 선택하여 새 참조 데이터 집합을 추가합니다.

5. 고유한 **참조 데이터 집합 이름**을 지정합니다.

    ![Time Series Insights 참조 데이터 집합 만들기 - 세부 정보](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. 빈 필드에 **키 이름**을 지정하고 **형식**을 선택합니다. 이 이름과 형식은 참조 데이터에 연결할 이벤트 원본의 이벤트에서 올바른 속성을 선택하는 데 사용됩니다. 

   예를 들어, 키 이름을 **DeviceId**, 형식을 **String**으로 제공한 경우 Time Series Insights 수신 엔진은 조회하고 연결할 들어오는 각 이벤트에서 **DeviceId**라는 이름과 **String** 형식의 속성을 찾습니다. 이벤트와 조인하는 키를 둘 이상 제공할 수 있습니다. 키 이름 일치는 대/소문자를 구분합니다.

7. **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계
* 프로그래밍 방식으로 [참조 데이터를 관리](time-series-insights-manage-reference-data-csharp.md)합니다.
* 전체 API 참조는 [참조 데이터 API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) 문서를 참조하세요.
