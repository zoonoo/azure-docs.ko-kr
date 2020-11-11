---
title: 샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트
description: 이 문서에서는 Azure Portal을 사용하여 Azure Stream Analytics 작업을 테스트하고, 입력을 샘플링하고, 샘플 데이터를 업로드하는 방법을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: 8e08c4c34495b58c105560dba9d818be9ebf5e34
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490970"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>포털에서 Azure Stream Analytics 작업 테스트

Azure Stream Analytics에서 작업을 시작 하거나 중지 하지 않고 쿼리를 테스트할 수 있습니다. 스트리밍 원본에서 들어오는 데이터에 대 한 쿼리를 테스트 하거나 Azure Portal의 로컬 파일에서 예제 데이터를 업로드할 수 있습니다. 또한 [Visual Studio](stream-analytics-live-data-local-testing.md) 의 로컬 샘플 데이터 또는 라이브 데이터에서 로컬로 쿼리를 테스트 하 고 [Visual Studio Code](visual-studio-code-local-run-live-input.md)수 있습니다.

## <a name="automatically-sample-incoming-data-from-input"></a>입력에서 들어오는 데이터를 자동으로 샘플링

Azure Stream Analytics은 스트리밍 입력에서 이벤트를 자동으로 인출 합니다. 기본 샘플에서 쿼리를 실행 하거나 샘플에 대 한 특정 시간 프레임을 설정할 수 있습니다.

1. Azure Portal에 로그인합니다.

2. 기존 Stream Analytics 작업을 찾아 선택 합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리** 를 선택하여 쿼리 편집기 창을 엽니다. 

4. 들어오는 이벤트의 샘플 목록을 보려면 파일을 사용 하 여 입력 아이콘을 선택 합니다. 그러면 샘플 이벤트가 **입력 미리 보기** 에 자동으로 표시 됩니다.

   a. JSON 또는 CSV의 경우 데이터의 serialization 형식이 자동으로 검색 됩니다. 드롭다운 메뉴에서 옵션을 변경 하 여 JSON, CSV, AVRO를 수동으로 변경할 수 있습니다.
    
   b. 선택기를 사용 하 여 **테이블** 또는 **원시** 형식으로 데이터를 볼 수 있습니다.
    
   다. 표시 된 데이터가 최신이 아닌 경우 **새로 고침** 을 선택 하 여 최신 이벤트를 확인 합니다.

   다음 표는 **테이블 형식의** 데이터에 대 한 예입니다.

   ![테이블 형식의 Azure Stream Analytics 샘플 입력](./media/stream-analytics-test-query/asa-sample-table.png)

   다음 표는 **원시 형식의** 데이터에 대 한 예입니다.

   ![원시 형식의 샘플 입력 Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 들어오는 데이터로 쿼리를 테스트 하려면 **쿼리 테스트** 를 선택 합니다. 결과는 **테스트 결과** 탭에 나타납니다. 결과 **다운로드** 를 선택 하 여 결과를 다운로드할 수도 있습니다.

   ![Azure Stream Analytics 예제 테스트 쿼리 결과](./media/stream-analytics-test-query/asa-test-query.png)

6. 들어오는 이벤트의 특정 시간 범위에 대해 쿼리를 테스트 하려면 **시간 범위 선택** 을 선택 합니다.
   
   ![들어오는 샘플 이벤트에 대 한 Azure Stream Analytics 시간 범위](./media/stream-analytics-test-query/asa-select-time-range.png)

7. 쿼리를 테스트 하는 데 사용 하려는 이벤트의 시간 범위를 설정 하 고 **샘플** 을 선택 합니다. 해당 시간 프레임 내에서 가장 먼저 발생 하는 최대 1000 이벤트 또는 1mb를 검색할 수 있습니다.

   ![들어오는 샘플 이벤트에 대 한 시간 범위 Azure Stream Analytics 설정](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 선택한 시간 범위에 대해 이벤트가 샘플링 되 면 **입력 미리 보기** 탭에 나타납니다.

   ![테스트 결과 보기 Azure Stream Analytics](./media/stream-analytics-test-query/asa-view-test-results.png)

9. **초기화** 를 선택 하 여 들어오는 이벤트의 샘플 목록을 표시 합니다. **재설정** 을 선택 하면 시간 범위 선택이 손실 됩니다. 쿼리 **테스트** 를 선택 하 여 쿼리를 테스트 하 고 **테스트 결과** 탭에서 결과를 검토 합니다.

10. 쿼리를 변경할 때 **쿼리 저장** 을 선택 하 여 새 쿼리 논리를 테스트 합니다. 이를 통해 반복적으로 쿼리를 수정 하 고 다시 테스트 하 여 출력이 어떻게 변경 되는지 확인할 수 있습니다.

11. 브라우저에 표시 된 결과를 확인 한 후에는 작업을 **시작할** 준비가 완료 된 것입니다.

## <a name="upload-sample-data-from-a-local-file"></a>로컬 파일에서 샘플 데이터 업로드

라이브 데이터를 사용 하는 대신 로컬 파일의 샘플 데이터를 사용 하 여 Azure Stream Analytics 쿼리를 테스트할 수 있습니다.

1. Azure Portal에 로그인합니다.
   
2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리** 를 선택하여 쿼리 편집기 창을 엽니다.

4. 로컬 파일을 사용 하 여 쿼리를 테스트 하려면 **입력 미리 보기** 탭에서 **샘플 입력 업로드** 를 선택 합니다. 

   ![스크린샷 샘플 입력 옵션을 보여 줍니다.](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. 로컬 파일을 업로드 하 여 쿼리를 테스트 합니다. JSON, CSV 또는 AVRO 형식만 사용 하 여 파일을 업로드할 수 있습니다. **확인** 을 선택합니다.

   ![파일을 선택할 수 있는 샘플 데이터 업로드 대화 상자가 스크린샷에 표시 됩니다.](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. 파일을 업로드 하는 즉시 양식의 파일 내용을 테이블 또는 원시 형식으로 볼 수도 있습니다. **재설정** 을 선택 하는 경우 샘플 데이터는 이전 섹션에서 설명 하는 들어오는 입력 데이터로 반환 됩니다. 언제 든 지 다른 파일을 업로드 하 여 쿼리를 테스트할 수 있습니다.

7. **쿼리 테스트** 를 선택 하 여 업로드 된 샘플 파일에 대해 쿼리를 테스트 합니다.

8. 쿼리를 기반으로 테스트 결과가 표시 됩니다. 쿼리를 변경 하 고 **쿼리 저장** 을 선택 하 여 새 쿼리 논리를 테스트할 수 있습니다. 이를 통해 반복적으로 쿼리를 수정 하 고 다시 테스트 하 여 출력이 어떻게 변경 되는지 확인할 수 있습니다.

9. 쿼리에서 여러 출력을 사용 하는 경우 선택한 출력에 따라 결과가 표시 됩니다. 

   ![선택한 출력 Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. 브라우저에 표시 된 결과를 확인 한 후 작업을 **시작할** 수 있습니다.

## <a name="limitations"></a>제한 사항

1.  시간 정책은 포털 테스트에서 지원 되지 않습니다.

    * 순서가 잘못 됨: 들어오는 모든 이벤트가 정렬 됩니다.
    * 지연 도착: Stream Analytics는 테스트용 으로만 기존 데이터를 사용할 수 있으므로 지연 도착 이벤트가 발생 하지 않습니다.
   
2.  C # UDF는 지원 되지 않습니다.

3.  모든 테스트는 하나의 스트리밍 단위가 있는 작업으로 실행 됩니다.

4.  제한 시간 크기는 1 분입니다. 따라서 창 크기가 1 분 보다 큰 쿼리는 데이터를 가져올 수 없습니다.

5.  Machine learning은 지원 되지 않습니다.

6. 샘플 데이터 API는 15 분 창에서 5 개 요청 후에 제한 됩니다. 15 분 기간이 끝난 후 더 많은 샘플 데이터 요청을 수행할 수 있습니다. 이 제한은 구독 수준에서 적용 됩니다.

## <a name="troubleshooting"></a>문제 해결

1.  이 오류가 발생 하면 "결과를 가져올 때 네트워크 연결 문제가 발생 했습니다. 네트워크 및 방화벽 설정을 확인 하십시오. "를 확인 하려면 다음 단계를 따르세요.

  * 서비스에 대 한 연결을 확인 하려면 [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index) 브라우저에서를 엽니다. 이 링크를 열 수 없는 경우 방화벽 설정을 업데이트 합니다.
  
2. 이 오류가 발생 하면 "요청 크기가 너무 큽니다. 입력 데이터 크기를 줄이고 다시 시도 하세요. "라는 다음 단계를 따르세요.

  * 입력 크기 줄이기 – 작은 크기의 샘플 파일이 나 더 작은 시간 범위를 사용 하 여 쿼리를 테스트 합니다.
  * 쿼리 크기 줄이기 – 쿼리 선택을 테스트 하려면 쿼리의 일부를 선택한 다음 **선택한 쿼리 테스트** 를 클릭 합니다.


## <a name="next-steps"></a>다음 단계
* [Stream Analytics를 사용 하 여 IoT 솔루션 빌드](./stream-analytics-build-an-iot-solution-using-stream-analytics.md):이 자습서에서는 유료 집에서 트래픽을 시뮬레이트하는 데이터 생성기를 사용 하 여 종단 간 솔루션을 구축 하는 방법을 안내 합니다.

* [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)

* [일반적인 Stream Analytics 사용 패턴에 대한 쿼리 예제](stream-analytics-stream-analytics-query-patterns.md)

* [Azure Stream Analytics의 입력 이해](stream-analytics-add-inputs.md)

* [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)
