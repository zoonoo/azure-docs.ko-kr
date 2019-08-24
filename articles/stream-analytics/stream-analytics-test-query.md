---
title: 샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트
description: 이 문서에서는 Azure Portal을 사용하여 Azure Stream Analytics 작업을 테스트하고, 입력을 샘플링하고, 샘플 데이터를 업로드하는 방법을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: b8670fc7958e66e7c25e27bb3a22429ab20b2514
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990965"
---
# <a name="test-an-azure-stream-analytics-job-with-sample-data"></a>샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트

Azure Stream Analytics에서 작업을 시작 하거나 중지 하지 않고 쿼리를 테스트할 수 있습니다. 입력 싱크에서 들어오는 데이터에 대 한 쿼리를 테스트 하거나 로컬 파일에서 업로드 된 샘플 데이터를 테스트할 수 있습니다.

## <a name="sample-incoming-data-from-input"></a>입력에서 들어오는 데이터 샘플링

Azure Stream Analytics은 자동으로 스트리밍 입력에서 이벤트를 페치합니다. 기본 샘플에서 쿼리를 실행 하거나 샘플에 대 한 특정 시간 프레임을 설정할 수 있습니다.

1. Azure 포털에 로그인합니다.

2. 기존 Stream Analytics 작업을 찾아 선택 합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리**를 선택하여 쿼리 편집기 창을 엽니다. 

4. 들어오는 이벤트의 샘플 목록을 보려면 파일을 사용 하 여 입력 아이콘을 선택 합니다. 그러면 샘플 이벤트가 **입력 미리 보기**에 자동으로 표시 됩니다. 

   a. JSON 또는 CSV의 경우 데이터의 serialization 형식이 자동으로 검색 됩니다. 드롭다운 메뉴에서 옵션을 변경 하 여 JSON, CSV, AVRO를 수동으로 변경할 수 있습니다.
    
   b. 선택기를 사용 하 여 **테이블** 또는 **원시** 형식으로 데이터를 볼 수 있습니다.
    
   c. 표시 된 데이터가 최신이 아닌 경우 **새로 고침** 을 선택 하 여 최신 이벤트를 확인 합니다.

   다음 표는 **테이블 형식의**데이터에 대 한 예입니다.

   ![테이블 형식의 Azure Stream Analytics 샘플 입력](./media/stream-analytics-test-query/asa-sample-table.png)

   다음 표는 **원시 형식의**데이터에 대 한 예입니다.

   ![원시 형식의 샘플 입력 Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 들어오는 데이터로 쿼리를 테스트 하려면 **쿼리 테스트**를 선택 합니다. 결과는 **테스트 결과** 탭에 나타납니다. 결과 **다운로드** 를 선택 하 여 결과를 다운로드할 수도 있습니다.

   ![Azure Stream Analytics 예제 테스트 쿼리 결과](./media/stream-analytics-test-query/asa-test-query.png)

6. 들어오는 이벤트의 특정 시간 범위에 대해 쿼리를 테스트 하려면 **시간 범위 선택**을 선택 합니다.
   
   ![들어오는 샘플 이벤트에 대 한 Azure Stream Analytics 시간 범위](./media/stream-analytics-test-query/asa-select-time-range.png)

7. 쿼리를 테스트 하는 데 사용 하려는 이벤트의 시간 범위를 설정 하 고 **샘플**을 선택 합니다. 해당 시간 프레임 내에서 가장 먼저 발생 하는 최대 1000 이벤트 또는 1mb를 검색할 수 있습니다.

   ![들어오는 샘플 이벤트에 대 한 시간 범위 Azure Stream Analytics 설정](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 선택한 시간 범위에 대해 이벤트가 샘플링 되 면 **입력 미리 보기** 탭에 나타납니다.

   ![테스트 결과 보기 Azure Stream Analytics](./media/stream-analytics-test-query/asa-view-test-results.png)

9. **초기화** 를 선택 하 여 들어오는 이벤트의 샘플 목록을 표시 합니다. **재설정**을 선택 하면 시간 범위 선택이 손실 됩니다. 쿼리 **테스트** 를 선택 하 여 쿼리를 테스트 하 고 **테스트 결과** 탭에서 결과를 검토 합니다.

10. 쿼리를 변경할 때 **쿼리 저장** 을 선택 하 여 새 쿼리 논리를 테스트 합니다. 이를 통해 반복적으로 쿼리를 수정 하 고 다시 테스트 하 여 출력이 어떻게 변경 되는지 확인할 수 있습니다.

11. 브라우저에 표시 된 결과를 확인 한 후에는 작업을 **시작할** 준비가 완료 된 것입니다.

## <a name="upload-sample-data-from-a-local-file"></a>로컬 파일에서 샘플 데이터 업로드

라이브 데이터를 사용 하는 대신 로컬 파일의 샘플 데이터를 사용 하 여 Azure Stream Analytics 쿼리를 테스트할 수 있습니다.

1. Azure 포털에 로그인합니다.
   
2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리**를 선택하여 쿼리 편집기 창을 엽니다.

4. 로컬 파일을 사용 하 여 쿼리를 테스트 하려면 **입력 미리 보기** 탭에서 **샘플 입력 업로드** 를 선택 합니다. 

   ![샘플 파일 업로드 Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. 로컬 파일을 업로드 하 여 쿼리를 테스트 합니다. JSON, CSV 또는 AVRO 형식만 사용 하 여 파일을 업로드할 수 있습니다. **확인**을 선택합니다.

   ![샘플 파일 업로드 Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. 파일을 업로드 하는 즉시 양식의 파일 내용을 테이블 또는 원시 형식으로 볼 수도 있습니다. **재설정**을 선택 하는 경우 샘플 데이터는 이전 섹션에서 설명 하는 들어오는 입력 데이터로 반환 됩니다. 언제 든 지 다른 파일을 업로드 하 여 쿼리를 테스트할 수 있습니다.

7. **쿼리 테스트** 를 선택 하 여 업로드 된 샘플 파일에 대해 쿼리를 테스트 합니다.

8. 쿼리를 기반으로 테스트 결과가 표시 됩니다. 쿼리를 변경 하 고 **쿼리 저장** 을 선택 하 여 새 쿼리 논리를 테스트할 수 있습니다. 이를 통해 반복적으로 쿼리를 수정 하 고 다시 테스트 하 여 출력이 어떻게 변경 되는지 확인할 수 있습니다.

9. 쿼리에서 여러 출력을 사용 하는 경우 선택한 출력에 따라 결과가 표시 됩니다. 

   ![선택한 출력 Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. 브라우저에 표시 된 결과를 확인 한 후 작업을 **시작할** 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [일반적인 Stream Analytics 사용 패턴에 대 한 쿼리 예제](stream-analytics-stream-analytics-query-patterns.md)

* [Azure Stream Analytics에 대 한 입력 이해](stream-analytics-add-inputs.md)

* [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)