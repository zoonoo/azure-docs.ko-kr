---
title: "Azure Portal을 사용하여 Azure Data Lake Analytics 작업 문제 해결 | Microsoft Docs"
description: "Azure 포털을 사용하여 데이터 레이크 분석 작업의 문제를 해결하는 방법에 대해 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: b9c7453cc0a94f70d0098ed83e5f127832065a62
ms.contentlocale: ko-kr
ms.lasthandoff: 06/15/2017


---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 문제 해결
Azure 포털을 사용하여 데이터 레이크 분석 작업의 문제를 해결하는 방법에 대해 알아봅니다.

이 자습서에서는 누락된 원본 파일 문제를 설치하고 Azure 포털을 사용하여 문제를 해결합니다.

## <a name="submit-a-data-lake-analytics-job"></a>데이터 레이크 분석 작업 제출

다음 U-SQL 작업 제출:

```
@searchlog =
   EXTRACT UserId          int,
           Start           DateTime,
           Region          string,
           Query           string,
           Duration        int?,
           Urls            string,
           ClickedUrls     string
   FROM "/Samples/Data/SearchLog.tsv1"
   USING Extractors.Tsv();

OUTPUT @searchlog   
   TO "/output/SearchLog-from-adls.csv"
   USING Outputters.Csv();
```
    
스크립트에 정의된 원본 파일은 **/Samples/Data/SearchLog.tsv1**이고 여기서는 **/Samples/Data/SearchLog.tsv**입니다.


## <a name="troubleshoot-the-job"></a>작업 문제 해결

**모든 작업을 보려면**

1. Azure 포털의 왼쪽 위 모서리에서 **Microsoft Azure** 를 클릭합니다.
2. 데이터 레이크 분석 계정 이름을 가진 타일을 클릭합니다.  **작업 관리** 타일에 작업 요약이 표시됩니다.

    ![Azure 데이터 레이크 분석 작업 관리](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    작업 관리에서 작업 상태를 한 눈에 볼 수 있습니다. 실패한 작업이 있는지 확인합니다.
3. 해당 작업을 보려면 **작업 관리** 타일을 클릭합니다. 작업은 **실행 중**, **대기**, **종료됨**으로 범주화되어 있습니다. **종료됨** 섹션에 실패한 작업이 표시됩니다. 목록에서 먼저 나옵니다. 작업이 많은 경우 **필터** 를 클릭하여 작업을 찾을 수 있습니다.

    ![Azure 데이터 레이크 분석 필터 작업](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. 새 블레이드에서 작업 세부 정보를 열려면 목록에서 실패한 작업을 클릭합니다.

    ![Azure 데이터 레이크 분석 실패 작업](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    **다시 제출** 단추를 확인합니다. 문제를 해결한 후 해당 작업을 다시 제출할 수 있습니다.
5. 이전 스크린샷에서 강조 표시된 부분을 클릭하고 오류 세부 정보를 엽니다.  다음과 같이 표시됩니다.

    ![Azure 데이터 레이크 분석 실패 작업 세부 정보](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    원본 폴더를 찾을 수 없다고 나옵니다.
6. **Duplicate Script**(스크립트 복제)를 클릭합니다.
7. **FROM** 경로를 다음으로 업데이트합니다.

    "/Samples/Data/SearchLog.tsv"
8. **작업 제출**을 클릭합니다.

## <a name="see-also"></a>참고 항목
* [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-powershell.md)
* [Visual Studio를 사용하여 Azure 데이터 레이크 분석 및 U-SQL 시작](data-lake-analytics-u-sql-get-started.md)
* [Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-portal.md)

