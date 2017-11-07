---
title: "Azure Analysis Services 자습서 추가 단원: 세부 정보 행 | Microsoft Docs"
description: "Azure Analysis Services 자습서에서 세부 정보 행 식을 만드는 방법을 설명합니다."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: fbc6187e80a42330c96ebaa1195ea188430894cc
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2017
---
# <a name="supplemental-lesson---detail-rows"></a>추가 단원 - 세부 정보 행

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

이 추가 단원에서는 DAX 편집기를 사용하여 사용자 지정 세부 정보 행 식을 정의합니다. 세부 정보 행 식은 최종 사용자에게 측정값의 집계된 결과에 대한 더 많은 정보를 제공하는 측정값에 대한 속성입니다. 
  
이 단원을 완료하기 위한 예상 시간: **10분**  
  
## <a name="prerequisites"></a>필수 조건  
이 추가 단원 항목은 테이블 형식 모델링 자습서에 포함됩니다. 이 추가 단원의 작업을 수행하기 전에 이전의 모든 단원을 완료하거나 완료된 Adventure Works Internet Sales 샘플 모델 프로젝트가 있어야 합니다.  
  
## <a name="what-do-we-need-to-solve"></a>해결을 위해 무엇을 해야 하나요?
세부 정보 행 식을 추가하기 전에 InternetTotalSales 측정값에 대한 세부 정보를 살펴보겠습니다.

1.  SSDT에서 **모델** 메뉴 > **Excel에서 분석**을 클릭하여 Excel을 열고 빈 피벗 테이블을 만듭니다.
  
2.  **피벗 테이블 필드**에서, FactInternetSales 테이블의 **InternetTotalSales** 측정값을 **Values**로, DimDate 테이블의 **CalendarYear**를 **Columns**로, **EnglishCountryRegionName**을 **Rows**로 추가합니다. 이제 피벗 테이블은 InternetTotalSales 측정값으로부터 지역 및 연도별로 집계된 결과를 제공합니다. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. 피벗 테이블에서 연도 및 지역 이름에 대해 집계된 값을 두 번 클릭합니다. 여기서는 2014년 오스트레일리아에 대한 값을 두 번 클릭했습니다. 데이터가 포함된 새 시트가 열리지만 유용한 데이터가 아닙니다.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
여기서 보려고 하는 것은 InternetTotalSales 측정값의 집계 결과를 구성하는 데이터 행과 열이 포함된 테이블입니다. 이를 위해 세부 정보 행 식을 측정값의 속성으로 추가할 수 있습니다.

## <a name="add-a-detail-rows-expression"></a>세부 정보 행 식 추가

#### <a name="to-create-a-detail-rows-expression"></a>세부 정보 행 식을 만들려면 
  
1. SSDT의 FactInternetSales 테이블 측정값 표에서 **InternetTotalSales** 측정값을 클릭합니다. 

2. **속성** > **세부 정보 행 식**에서 편집기 단추를 클릭하여 DAX 편집기를 엽니다.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. DAX 편집기에서 다음 식을 입력합니다.

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    이 식은 FactInternetSales 테이블에서 이름, 열 및 측정값 결과를 지정하며 사용자가 피벗 테이블 또는 보고서에서 집계된 결과를 두 번 클릭하면 관련 테이블이 반환됩니다.

4. 다시 Excel에서 3단계에 만든 시트를 삭제한 후 집계된 값을 두 번 클릭합니다. 이때, 측정값에 대해 정의된 세부 정보 행 식 속성과 함께, 훨씬 유용한 데이터가 포함된 새 시트가 열립니다.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. 모델을 다시 배포합니다.

  
## <a name="see-also"></a>참고 항목  
[SELECTCOLUMNS 함수(DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[추가 단원 - 동적 보안](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[추가 단원 - 불규칙한 계층 구조](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  
