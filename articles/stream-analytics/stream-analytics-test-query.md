---
title: Azure Stream Analytics에서 쿼리 테스트
description: 이 문서에서는 Stream Analytics 작업에서 예제 데이터 파일을 사용하여 쿼리를 테스트하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: fb7d936dfdc543c208ce98c588f5ad83704ff5dc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Azure Portal에서 Azure Stream Analytics 쿼리 테스트

Azure Stream Analytics를 사용하면 작업을 시작하거나 중지하지 않고도 Azure Portal에서 쿼리를 테스트할 수 있습니다.

## <a name="test-the-input"></a>입력 테스트

1. 샘플 입력 데이터를 테스트하려면 사용자 입력 중 하나를 마우스 오른쪽 단추로 클릭한 다음 **파일에서 샘플 데이터 업로드**를 선택합니다. 현재 JSON 형식 데이터만 업로드할 수 있습니다. 데이터가 CSV 등의 다른 형식인 경우 업로드하기 전에 JSON으로 변환해야 합니다. [CSV-JSON 변환기](http://www.convertcsv.com/csv-to-json.htm)와 같은 오픈 소스 변환 도구를 사용하여 데이터를 JSON으로 변환할 수 있습니다.

    ![Stream Analytics 쿼리 편집기 쿼리 테스트](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. 업로드가 완료되면 **테스트**를 클릭하여 입력한 샘플 데이터에 대해 이 쿼리를 테스트합니다.

    ![Stream Analytics 쿼리 편집기 샘플 데이터 테스트](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

나중에 사용하기 위해 테스트 출력을 저장하려는 경우 사용자 쿼리의 출력이 다운로드 결과 링크와 함께 브라우저에 표시됩니다. 이제 쉽고 반복적으로 쿼리를 수정하고 반복적으로 테스트하여 출력이 어떻게 변화하는지 확인할 수 있습니다.

![Stream Analytics 쿼리 편집기 샘플 출력](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

쿼리에서 사용된 여러 출력을 통해 두 출력에 대한 결과를 개별적으로 볼 수 있으며 서로 쉽게 전환할 수 있습니다.

브라우저에 표시된 결과에 만족한다면 쿼리를 저장하고, 작업을 시작하여 오류 없이 이벤트를 처리할 수 있습니다.

## <a name="get-help"></a>도움말 보기

추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
