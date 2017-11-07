---
title: "비정상적인 되풀이 작업 문제를 해결하는 방법 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio를 사용하여 비정상적인 되풀이 작업을 디버그하는 방법을 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>비정상적인 되풀이 작업 문제를 해결하는 방법

이 문서에서는 [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)를 사용하여 되풀이 작업 문제를 해결하는 방법을 소개합니다. 파이프라인 및 되풀이 작업에 대한 자세한 내용은 [여기](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)를 참조하세요.
되풀이 작업은 일반적으로 동일한 쿼리 논리 및 유사한 입력 데이터를 공유합니다. 예를 들어 매주 월요일 오전 8시에 지난 주의 주간 활성 사용자를 집계하기 위해 되풀이 작업이 실행되고, 이러한 작업의 스크립트는 쿼리 논리를 포함하고 있는 한 스크립트 템플릿을 공유하고, 이러한 작업의 입력은 지난 주의 사용 현황 데이터라고 가정해 봅시다. 동일한 쿼리 논리 및 유사한 입력을 공유한다는 것은 일반적으로 이러한 작업의 성능이 비슷하고 안정적이라는 의미이며, 되풀이 작업 중 하나가 갑자기 비성상적으로 실행되거나 오류가 발생하거나 심각하게 느려진 경우 다음과 같은 조치를 취할 수 있습니다.

1.  되풀이 작업의 미리 보기 실행에 대한 통계 보고서를 통해 어떤 일이 발생했는지 파악합니다.
2.  비정상 작업을 정상 작업과 비교하여 무엇이 변경되었는지 확인합니다.

Azure Data Lake Tools for Visual Studio의 **관련 작업 보기**는 두 사례에서 문제 해결 속도를 높이는 데 도움이 됩니다.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>1단계: 되풀이 작업을 찾아서 관련 작업 보기 열기

관련 작업 보기를 사용하여 되풀이 작업 문제를 해결하려면 먼저 Visual Studio에서 되풀이 작업을 찾은 후 관련 작업 보기를 열어야 합니다.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>사례 1: 되풀이 작업의 URL을 갖고 있는 경우

**도구 > Data Lake > 작업 보기**를 통해 작업 URL을 붙여넣으면 Visual Studio에서 작업 보기를 열 수 있고, 관련 작업 보기를 통해 관련 작업 보기를 열 수 있습니다.

![Data Lake Analytics 도구 관련 작업 보기](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>사례 2: 되풀이 작업에 대한 파이프라인을 갖고 있지만 URL은 아닌 경우

Visual Studio에서 **서버 탐색기 > Data Lake Analytics 계정 > 파이프라인**을 통해 파이프라인 브라우저를 열 수 있습니다. 서버 탐색기에서 이 노드를 찾을 수 없으면 [여기](http://aka.ms/adltoolsvs)서 최신 도구를 받으세요. 파이프라인 브라우저에서 ADLA 계정에 대한 모든 파이프라인은 왼쪽에 표시되며, 파이프라인을 확장하여 모든 되풀이 작업을 찾을 수 있습니다. 문제가 있는 되풀이 작업을 클릭하면 오른쪽에 관련 작업 보기가 열립니다.

![Data Lake Analytics 도구 관련 작업 보기](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Data Lake Analytics 도구 관련 작업 보기](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>2단계: 통계 보고서 분석

관련 작업 보기의 맨 위에는 요약 및 통계 보고서가 표시되며, 이 보고서를 통해 비정상 작업의 근본 원인을 파악할 수 있습니다. 

1.  먼저 보고서에서 비정상 작업을 찾아야 합니다. X 축에는 작업 제출 시간이 표시되며, 이 시간을 통해 비정상 작업을 찾을 수 있습니다.
2.  아래 프로세스에 따라 통계를 확인하고 비정상 작업 및 가능한 해결 방법에 대한 정보를 얻습니다.

![Data Lake Analytics 도구 관련 작업 보기](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>3단계: 비정상적인 되풀이 작업을 정상 작업과 비교

제출된 모든 되풀이 작업은 관련 작업 보기의 맨 아래에 표시되는 작업 목록을 통해 찾을 수 있습니다. 마우스 오른쪽 단추를 클릭하면 작업 차이점 보기에서 비정상 작업과 이전의 정상 작업을 비교하여 보다 구체적인 정보와 가능한 해결 방법을 찾을 수 있습니다.

![Data Lake Analytics 도구 관련 작업 보기](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

이러한 두 작업은 성능 문제를 유발하는 주요 원인이므로 일반적으로 두 작업의 큰 차이점에 주의를 기울여야 하며, 아래 단계에 따라 추가 검사를 수행할 수 있습니다.

![Data Lake Analytics 도구 관련 작업 보기](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>다음 단계

* [데이터 기울이기 문제를 디버그 및 해결하는 방법](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [사용자 정의 코드 오류에 대한 U-SQL 작업 오류를 디버그하는 방법](data-lake-analytics-debug-u-sql-jobs.md)