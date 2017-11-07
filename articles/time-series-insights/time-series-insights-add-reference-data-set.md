---
title: "Azure Time Series Insights 환경에 참조 데이터 집합 추가 | Microsoft Docs"
description: "이 자습서에서는 Time Series Insights 환경에 참조 데이터 집합을 추가합니다."
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: b94ca172dba71b407ee5e9a40c283a97602efd17
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Ibiza 포털을 사용하여 Time Series Insights 환경에 대한 참조 데이터 집합 만들기

참조 데이터 집합은 이벤트 원본의 이벤트로 보강된 항목의 컬렉션입니다. Time Series Insights 수신 엔진은 이벤트 원본의 이벤트와 참조 데이터 집합의 항목을 조인합니다. 이렇게 보강된 이벤트는 쿼리에 사용할 수 있습니다. 이 조인은 참조 데이터 집합에서 정의된 키를 기반으로 합니다.

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>환경에 참조 데이터 집합을 추가하는 단계

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. Azure Portal의 왼쪽에 있는 메뉴에서 “모든 리소스”를 클릭합니다.
3. Time Series Insights 환경을 선택합니다.

    ![Time Series Insights 참조 데이터 집합 만들기](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. “참조 데이터 집합”을 선택하고, “+ 추가”를 합니다.

    ![Time Series Insights 참조 데이터 집합 만들기 - 세부 정보](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. 참조 데이터 집합의 이름을 지정합니다.
6. 키 이름과 형식을 지정합니다. 이 이름과 형식은 이벤트 원본의 이벤트에서 올바른 속성을 선택하는 데 사용됩니다. 예를 들어, 키 이름을 “DeviceId”, 형식을 “String”으로 제공한 경우 Time Series Insights 수신 엔진은 들어오는 이벤트에서 “DeviceId”라는 이름과 “String” 형식의 속성을 찾습니다. 이벤트와 조인하는 키를 둘 이상 제공할 수 있습니다. 키 이름 일치는 대/소문자를 구분합니다.

     ![Time Series Insights 참조 데이터 집합 만들기 - 세부 정보](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. “만들기”를 클릭합니다.

## <a name="next-steps"></a>다음 단계

* 프로그래밍 방식으로 [참조 데이터를 관리](time-series-insights-manage-reference-data-csharp.md)합니다.
* 전체 API 참조는 [참조 데이터 API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) 문서를 참조하세요.
