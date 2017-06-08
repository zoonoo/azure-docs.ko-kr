---
title: "Azure Portal을 사용하여 Azure Data Lake Analytics 시작 | Microsoft Docs"
description: "Azure Portal을 사용하여 Data Lake Analytics 계정을 만들고, U-SQL을 사용하여 Data Lake Analytics 작업을 만들고, 작업을 제출하는 방법에 대해 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eb85d8ef6b29605d7e26b0d2139a4a95c35141fb
ms.contentlocale: ko-kr
ms.lasthandoff: 06/01/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>자습서: Azure Portal을 사용하여 Azure Data Lake Analytics 시작
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Portal을 사용하여 Azure Data Lake Analytics 계정을 만들고, [U-SQL](data-lake-analytics-u-sql-get-started.md)로 작업을 정의하고, 작업을 Data Lake Analytics 서비스에 제출하는 방법에 대해 알아봅니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 **Azure 구독**이 있어야 합니다. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="create-data-lake-analytics-account"></a>데이터 레이크 분석 계정 만들기

이제 Data Lake Analytics 및 Data Lake Store 계정을 동시에 만듭니다.  이 단계는 간단하며 완료하는 데 약 60초가 소요됩니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기** >  **인텔리전스 + 분석** > **Data Lake Analytics**를 클릭합니다.
3. 다음 항목에 대한 값을 선택합니다.
   * **이름**: Data Lake Analytics 계정의 이름을 지정합니다(소문자와 숫자만 허용).
   * **구독**: 분석 계정에 사용할 Azure 구독을 선택합니다.
   * **리소스 그룹**. 기존 Azure 리소스 그룹을 선택하거나 리소스 그룹을 새로 만듭니다.
   * **위치** - 데이터 레이크 분석 계정에 대한 Azure 데이터 센터를 선택합니다.
   * **Data Lake Store**: 지침에 따라 새 Data Lake Store 계정을 만들거나 기존 계정을 선택합니다. 
4. 필요에 따라 Data Lake Analytics 계정에 대한 가격 책정 계층을 선택합니다.
5. **만들기**를 클릭합니다. 

## <a name="create-and-submit-data-lake-analytics-jobs"></a>데이터 레이크 분석 작업 만들기 및 제출하기
원본 데이터를 준비한 후에는 U-SQL 스크립트 개발을 시작할 수 있습니다.  

**작업을 제출하려면**

1. Data Lake Analytics 계정에서 **새 작업**을 클릭합니다.
2. **작업 이름**을 입력하고 다음 U-SQL 스크립트를 입력합니다.

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
```


이 U-SQL 스크립트는 **Extractors.Tsv()**를 사용하여 원본 데이터 파일을 읽은 다음 **Outputters.Csv()**를 사용하여 csv 파일을 만듭니다.

1. **작업 제출**을 클릭합니다.   
2. 작업 상태가 **Succeeded**로 바뀔 때까지 기다립니다.
3. 작업이 실패한 경우 [Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)을 참조하세요.
4. **출력** 탭을 클릭하고 `SearchLog-from-Data-Lake.csv`를 클릭합니다. 

## <a name="see-also"></a>참고 항목

* U-SQL 응용 프로그램 개발을 시작하려면 [Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
* U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
* 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.

