---
title: Azure Stream Analytics에서 샘플 데이터를 사용하여 쿼리 테스트
description: 이 아티클에서는 Azure Stream Analytics에서 일부 샘플 입력 데이터를 사용하여 쿼리를 테스트하는 방법에 대해 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/12/2018
ms.locfileid: "30961692"
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Azure Stream Analytics에서 쿼리 및 샘플 입력 테스트 

Azure Stream Analytics를 사용하면 작업을 시작 또는 중지할 필요 없이 파일에서 제공하는 입력 이벤트를 샘플링하고, 포털에서 쿼리를 테스트할 수 있습니다.

## <a name="testing-your-query"></a>쿼리 테스트

Stream Analytics 작업 세부 정보 창에서 **쿼리** 아래에 있는 쿼리 이름을 클릭하여 **쿼리 편집기** 블레이드를 엽니다. (예제 시나리오에서는 쿼리가 아직 생성되지 않았으므로 **< >** 자리 표시자를 클릭합니다.)

![Stream Analytics 쿼리 편집기](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

이전 릴리스에서와 마찬가지로 쿼리를 만들기 위한 다양한 편집기 블레이드가 표시됩니다. 이제 쿼리에 사용되고 이 작업에 대해 정의된 입력 및 출력을 보여 주는 새로운 왼쪽 창으로 블레이드가 업데이트됩니다.

![Stream Analytics 쿼리 편집기 입력 및 출력 목록](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

또한 정의되지 않은 추가 입력 및 출력이 표시됩니다. 이는 시작할 때 사용하는 새 쿼리 템플릿에서 가져온 것입니다. 사용자가 쿼리를 편집함에 따라 변경되거나 심지어 완전히 사라집니다. 지금은 무시해도 무방합니다.

샘플 입력 데이터를 테스트하려면 사용자 입력 중 하나를 마우스 오른쪽 단추로 클릭한 다음 **파일에서 샘플 데이터 업로드**를 선택합니다.

![Stream Analytics 쿼리 편집기 파일에서 샘플 데이터 업로드 명령](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

업로드가 완료되면 **테스트**를 클릭하여 방금 입력한 샘플 데이터에 대해 이 쿼리를 테스트합니다.

![Stream Analytics 쿼리 편집기 테스트 단추](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

나중에 사용하기 위해 테스트 출력을 저장하려는 경우 사용자 쿼리의 출력이 다운로드 결과에 대한 링크와 함께 브라우저에 표시됩니다. 이제 쉽고 반복적으로 쿼리를 수정하고 반복적으로 테스트하여 출력이 어떻게 변화하는지 확인할 수 있습니다.

![Stream Analytics 쿼리 편집기 샘플 출력](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

위 이미지에서 **HighAvgTempOutput**이라는 두 번째 출력이 추가되었습니다.

쿼리에서 여러 출력을 사용하면 각 출력에 대한 결과를 개별적으로 볼 수 있으며 서로 쉽게 전환할 수 있습니다.

결과에 만족한다면 쿼리를 저장하고, 작업을 시작하고, 앉아서 Stream Analytics의 마법을 지켜볼 수 있습니다.

## <a name="get-help"></a>도움말 보기

추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
