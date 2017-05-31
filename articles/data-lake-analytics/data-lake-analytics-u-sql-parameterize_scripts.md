---
title: "Azure Data Lake Analytics에서 U-SQL 스크립트 매개 변수화 | Microsoft Docs"
description: "Azure Data Lake Analytics에서 U-SQL 스크립트를 매개 변수화하는 방법에 대해 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6318fa8b14d8bb7d650522e2d96a5b1417afe3df
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="parameterizing-u-sql-scripts"></a>U-SQL 스크립트 매개 변수화

U-SQL 스크립트를 사용할 때는 스크립트를 매개 변수화하는 것이 편리합니다. 매개 변수를 사용하면 본문을 동일하게 유지하고 개별 매개 변수 값을 전달하여 스크립트의 실행을 제어할 수 있습니다. 일반적인 시나리오에서는 일정 시간에 걸쳐 데이터를 처리하는 스크립트가 관련되어 있는데, 개발자는 시작 날짜와 종료 날짜에 이러한 스크립트를 매개 변수화하려고 할 수 있습니다. 

스크립트는 다음 두 가지 방법으로 매개 변수화됩니다.
* 스크립트가 스크립트 맨 위에 매개 변수의 안전한 추가를 허용합니다. U-SQL에서는 이 방법을 지원합니다.
* 작업 제출 API는 호출자가 매개 변수를 전송하도록 허용합니다. U-SQL에서는 이 방법을 지원하지 않습니다.

## <a name="example-scripts"></a>예제 스크립트

간단한 초기 스크립트는 다음과 같습니다.

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    
   
이 시나리오에서는 `DECLARE EXTERNAL` 문을 추가하여 ``Region``을 매개 변수화합니다.

```
DECLARE EXTERNAL @TargetRegion = "en-us";

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    

이 스크립트의 실행은 `en-us` 하위 지역의 행만 필터링하도록 기본적으로 설정되어 있습니다. 지금까지 매개 변수는 스크립트에서 정의했습니다. `EXTERNAL` 키워드는 `@TargetRegion` 매개 변수를 정의하는 DECLARE 문을 추가하여 `@TargetRegion` 값을 다시 정의할 수 있음을 나타냅니다.

다음 스크립트는 매개 변수의 값이 제공되는 경우 매개 변수화된 스크립트의 모양을 보여 줍니다. 이제 이 스크립트는 `en-gb`의 하위 지역이 포함된 행을 필터링합니다.

```
DECLARE @TargetRegion = "en-gb";

DECLARE EXTERNAL @TargetRegion = "en-us";

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    


## <a name="next-steps"></a>다음 단계
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

