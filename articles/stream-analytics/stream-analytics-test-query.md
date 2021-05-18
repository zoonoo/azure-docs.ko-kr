---
title: 샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트
description: 이 문서에서는 Azure Portal을 사용하여 Azure Stream Analytics 작업을 테스트하고, 입력을 샘플링하고, 샘플 데이터를 업로드하는 방법을 설명합니다.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: eff9103f476e6074ab46198ff8cc78588675569f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020046"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Azure Portal에서 Stream Analytics 작업 테스트

Azure Stream Analytics에서 작업을 시작하거나 중지하지 않고 쿼리를 테스트할 수 있습니다. 스트리밍 원본에서 들어오는 데이터에 대한 쿼리를 테스트하거나 Azure Portal의 로컬 파일에서 예제 데이터를 업로드할 수 있습니다. 또한 [Visual Studio](stream-analytics-live-data-local-testing.md) 및 [Visual Studio Code](visual-studio-code-local-run-live-input.md)의 로컬 샘플 데이터 또는 라이브 데이터에서 로컬로 쿼리를 테스트할 수 있습니다.

## <a name="automatically-sample-incoming-data-from-input"></a>입력에서 들어오는 데이터 자동 샘플링

Azure Stream Analytics는 스트리밍 입력에서 이벤트를 자동으로 페치합니다. 기본값 샘플에서 쿼리를 실행하거나 샘플에 대한 특정 시간 프레임을 설정할 수 있습니다.

1. Azure Portal에 로그인합니다.

2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리** 를 선택하여 쿼리 편집기 창을 엽니다. 

4. 들어오는 이벤트의 샘플 목록을 보려면 파일 아이콘이 있는 입력을 선택합니다. 그러면 샘플 이벤트가 **입력 미리 보기** 에 자동으로 표시됩니다.

   a. JSON 또는 CSV의 경우 데이터의 serialization 형식이 자동으로 검색됩니다. 드롭다운 메뉴에서 옵션을 변경하여 JSON, CSV, AVRO를 수동으로 변경할 수 있습니다.
    
   b. 선택기를 사용하여 **테이블** 또는 **원시** 형식으로 데이터를 볼 수 있습니다.
    
   다. 표시된 데이터가 최신이 아닌 경우 **새로 고침** 을 선택하여 최신 이벤트를 확인합니다.

   다음 표는 **테이블 형식의** 데이터에 대한 예제입니다.

   ![테이블 형식의 Azure Stream Analytics 샘플 입력](./media/stream-analytics-test-query/asa-sample-table.png)

   다음 표는 **원시 형식의** 데이터에 대한 예제입니다.

   ![원시 형식의 Azure Stream Analytics 샘플 입력](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 들어오는 데이터로 쿼리를 테스트하려면 **쿼리 테스트** 를 선택합니다. 결과는 **테스트 결과** 탭에 나타납니다. 결과 **다운로드** 를 선택하여 결과를 다운로드할 수도 있습니다.

   ![Azure Stream Analytics 샘플 테스트 쿼리 결과](./media/stream-analytics-test-query/asa-test-query.png)

6. 들어오는 이벤트의 특정 시간 범위에 대해 쿼리를 테스트하려면 **시간 범위 선택** 을 선택합니다.
   
   ![들어오는 샘플 이벤트에 대한 Azure Stream Analytics 시간 범위](./media/stream-analytics-test-query/asa-select-time-range.png)

7. 쿼리를 테스트하는 데 사용하려는 이벤트의 시간 범위를 설정하고 **샘플** 을 선택합니다. 해당 시간 프레임 내에서 최대 1000개의 이벤트 또는 1MB의 이벤트 중 가장 먼저 발생하는 이벤트를 검색할 수 있습니다.

   ![들어오는 샘플 이벤트에 대한 Azure Stream Analytics 시간 범위 설정](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 선택한 시간 범위에 대해 이벤트가 샘플링되면 **입력 미리 보기** 탭에 나타납니다.

   ![Azure Stream Analytics 테스트 결과 보기](./media/stream-analytics-test-query/asa-view-test-results.png)

9. **초기화** 를 선택하여 들어오는 이벤트의 샘플 목록을 표시합니다. **초기화** 를 선택하면 시간 범위 선택 영역이 사라집니다. **쿼리 테스트** 를 선택하여 쿼리를 테스트하고 **테스트 결과** 탭에서 결과를 검토합니다.

10. 쿼리를 변경할 때는 **쿼리 저장** 을 선택하여 새 쿼리 논리를 테스트합니다. 이렇게 하면 반복해서 쿼리를 수정하고 테스트하여 출력이 어떻게 변하는지 확인할 수 있습니다.

11. 브라우저에 표시된 결과를 확인하고 나면 작업을 **시작할** 준비가 완료됩니다.

## <a name="upload-sample-data-from-a-local-file"></a>로컬 파일에서 샘플 데이터 업로드

라이브 데이터를 사용하는 대신 로컬 파일의 샘플 데이터를 사용하여 Azure Stream Analytics 쿼리를 테스트할 수 있습니다.

1. Azure Portal에 로그인합니다.
   
2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리** 를 선택하여 쿼리 편집기 창을 엽니다.

4. 로컬 파일을 사용하여 쿼리를 테스트하려면 **입력 미리 보기** 탭에서 **샘플 입력 업로드** 를 선택합니다. 

   ![스크린샷은 샘플 입력 업로드 옵션을 보여 줍니다.](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. 로컬 파일을 업로드하여 쿼리를 테스트합니다. JSON, CSV 또는 AVRO 포맷만 사용하여 파일을 업로드할 수 있습니다. **확인** 을 선택합니다.

   ![파일을 선택할 수 있는 샘플 데이터 업로드 대화 상자가 스크린샷에 표시됩니다.](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. 파일을 업로드하는 즉시 양식의 파일 콘텐츠를 테이블 또는 원시 형식으로 볼 수도 있습니다. **초기화** 를 선택하는 경우 샘플 데이터는 이전 섹션에서 설명한 들어오는 입력 데이터로 반환됩니다. 언제든지 다른 파일을 업로드하여 쿼리를 테스트할 수 있습니다.

7. **쿼리 테스트** 를 선택하여 업로드된 샘플 파일에 대해 쿼리를 테스트합니다.

8. 쿼리를 기반으로 테스트 결과가 표시됩니다. 쿼리를 변경하고 **쿼리 저장** 을 선택하여 새 쿼리 논리를 테스트할 수 있습니다. 이렇게 하면 반복해서 쿼리를 수정하고 테스트하여 출력이 어떻게 변하는지 확인할 수 있습니다.

9. 쿼리에서 여러 출력을 사용하는 경우 선택한 출력에 따라 결과가 표시됩니다. 

   ![Azure Stream Analytics 선택된 출력](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. 브라우저에 표시된 결과를 확인하고 나면 작업을 **시작할** 수 있습니다.

## <a name="limitations"></a>제한 사항

1.  시간 정책은 포털 테스트에서 지원되지 않습니다.

    * 잘못된 순서: 들어오는 모든 이벤트가 정렬됩니다.
    * 지연 도착: Stream Analytics는 기존 데이터를 테스트용으로만 사용할 수 있으므로 지연 도착 이벤트가 발생하지 않습니다.
   
2.  C# UDF는 지원되지 않습니다.

3.  모든 테스트는 하나의 스트리밍 단위가 있는 작업으로 실행됩니다.

4.  제한 시간 크기는 1분입니다. 따라서 창 크기가 1분보다 큰 쿼리는 데이터를 가져올 수 없습니다.

5.  기계 학습은 지원되지 않습니다.

6. 샘플 데이터 API는 15분 창에서 5개 요청이 있은 후에 제한됩니다. 15분 창이 끝난 후 더 많은 샘플 데이터 요청을 수행할 수 있습니다. 제한 사항은 구독 수준에서 적용됩니다.

## <a name="troubleshooting"></a>문제 해결

1.  다음과 같은 오류가 발생하는 경우 "결과를 페치할 때 네트워크 연결 문제가 발생했습니다. 네트워크 및 방화벽 설정을 확인하세요. " 다음 단계를 따르세요.

  * 서비스에 대한 연결을 확인하려면 브라우저에서 [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index)를 엽니다. 이 링크를 열 수 없는 경우 방화벽 설정을 업데이트합니다.
  
2. 다음과 같은 오류가 발생하는 경우 "요청 크기가 너무 큽니다. 입력 데이터 크기를 줄이고 다시 시도하세요." 다음 단계를 따르세요.

  * 입력 크기 줄이기 – 더 작은 크기의 샘플 파일 또는 더 작은 시간 범위를 사용하여 쿼리를 테스트합니다.
  * 쿼리 크기 줄이기 – 쿼리 선택을 테스트하려면 쿼리의 일부를 선택한 다음 **선택한 쿼리 테스트** 를 클릭합니다.


## <a name="next-steps"></a>다음 단계
* [Stream Analytics를 사용하여 IoT 솔루션 빌드](./stream-analytics-build-an-iot-solution-using-stream-analytics.md): 이 자습서에서는 요금소에서 트래픽을 시뮬레이션하는 데이터 생성기를 사용하여 엔드투엔드 솔루션을 빌드하는 방법을 안내합니다.

* [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)

* [일반적인 Stream Analytics 사용 패턴에 대한 쿼리 예제](stream-analytics-stream-analytics-query-patterns.md)

* [Azure Stream Analytics의 입력 이해](stream-analytics-add-inputs.md)

* [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)
