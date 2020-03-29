---
title: 샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트
description: 이 문서에서는 Azure Portal을 사용하여 Azure Stream Analytics 작업을 테스트하고, 입력을 샘플링하고, 샘플 데이터를 업로드하는 방법을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898396"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>포털에서 Azure 스트림 분석 작업 테스트

Azure Stream Analytics에서 작업을 시작하거나 중지하지 않고 쿼리를 테스트할 수 있습니다. 스트리밍 소스에서 들어오는 데이터에 대한 쿼리를 테스트하거나 Azure Portal의 로컬 파일에서 샘플 데이터를 업로드할 수 있습니다. 로컬 샘플 데이터 또는 [Visual Studio](stream-analytics-live-data-local-testing.md) [코드에서](visual-studio-code-local-run-live-input.md)라이브 데이터에서 로컬로 쿼리를 테스트할 수도 있습니다.

## <a name="automatically-sample-incoming-data-from-input"></a>입력에서 들어오는 데이터를 자동으로 샘플링

Azure Stream Analytics는 스트리밍 입력에서 이벤트를 자동으로 가져옵니다. 기본 샘플에서 쿼리를 실행하거나 샘플에 대한 특정 시간 프레임을 설정할 수 있습니다.

1. Azure Portal에 로그인합니다.

2. 기존 스트림 분석 작업을 찾아 선택합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리**를 선택하여 쿼리 편집기 창을 엽니다. 

4. 들어오는 이벤트의 샘플 목록을 보려면 파일 아이콘이 있는 입력을 선택하면 샘플 이벤트가 **입력 미리 보기에**자동으로 나타납니다.

   a. JSON 또는 CSV인 경우 데이터의 직렬화 유형이 자동으로 검색됩니다. 드롭다운 메뉴에서 옵션을 변경하여 수동으로 JSON, CSV, AVRO로 변경할 수 있습니다.
    
   b. 선택기를 사용하여 **데이터를 테이블** 또는 **원시** 형식으로 봅니다.
    
   다. 표시된 데이터가 최신 데이터가 아닌 경우 **새로 고침을** 선택하여 최신 이벤트를 확인합니다.

   다음 표는 **테이블 형식의**데이터의 예입니다.

   ![테이블 형식의 Azure 스트림 분석 샘플 입력](./media/stream-analytics-test-query/asa-sample-table.png)

   다음 표는 **Raw 형식의**데이터의 예입니다.

   ![Azure 스트림 분석 샘플 입력(원시 형식)](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 들어오는 데이터로 쿼리를 테스트하려면 **쿼리 테스트**를 선택합니다. 결과는 테스트 **결과** 탭에 나타납니다. **결과 다운로드를** 선택하여 결과를 다운로드할 수도 있습니다.

   ![Azure 스트림 분석 샘플 테스트 쿼리 결과](./media/stream-analytics-test-query/asa-test-query.png)

6. 들어오는 이벤트의 특정 시간 범위에 대해 쿼리를 테스트하려면 **시간 범위 선택을**선택합니다.
   
   ![들어오는 샘플 이벤트에 대한 Azure 스트림 분석 시간 범위](./media/stream-analytics-test-query/asa-select-time-range.png)

7. 쿼리를 테스트하고 **샘플을**선택하는 데 사용할 이벤트의 시간 범위를 설정합니다. 이 기간 내에 최대 1,000개의 이벤트 또는 1MB 중 먼저 검색할 수 있습니다.

   ![들어오는 샘플 이벤트에 대한 Azure 스트림 분석 설정 시간 범위](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 이벤트가 선택한 시간 범위에 대해 샘플링되면 입력 **미리 보기** 탭에 나타납니다.

   ![Azure 스트림 분석 보기 테스트 결과](./media/stream-analytics-test-query/asa-view-test-results.png)

9. 수신 이벤트의 샘플 목록을 보려면 **재설정을** 선택합니다. **재설정을**선택하면 시간 범위 선택이 손실됩니다. **테스트 쿼리를** 선택하여 쿼리를 테스트하고 **테스트 결과** 탭에서 결과를 검토합니다.

10. 쿼리를 변경할 때 쿼리 **저장을** 선택하여 새 쿼리 논리를 테스트합니다. 이렇게 하면 쿼리를 반복적으로 수정하고 다시 테스트하여 출력이 어떻게 변경되는지 확인할 수 있습니다.

11. 브라우저에 표시된 결과를 확인한 후 작업을 **시작할** 준비가 된 것입니다.

## <a name="upload-sample-data-from-a-local-file"></a>로컬 파일에서 샘플 데이터 업로드

라이브 데이터를 사용하는 대신 로컬 파일의 샘플 데이터를 사용하여 Azure Stream Analytics 쿼리를 테스트할 수 있습니다.

1. Azure Portal에 로그인합니다.
   
2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리**를 선택하여 쿼리 편집기 창을 엽니다.

4. 로컬 파일로 쿼리를 테스트하려면 입력 미리 **보기** 탭에서 **샘플 입력 업로드를** 선택합니다. 

   ![Azure 스트림 분석 업로드 샘플 파일](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. 쿼리를 테스트하기 위해 로컬 파일을 업로드합니다. JSON, CSV 또는 AVRO 형식으로만 파일을 업로드할 수 있습니다. **확인**을 선택합니다.

   ![Azure 스트림 분석 업로드 샘플 파일](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. 파일을 업로드하는 즉시 양식의 파일 내용을 테이블또는 원시 형식으로 볼 수도 있습니다. **재설정을**선택하면 샘플 데이터가 이전 섹션에서 설명한 들어오는 입력 데이터로 돌아갑니다. 언제든지 다른 파일을 업로드하여 쿼리를 테스트할 수 있습니다.

7. 업로드된 샘플 파일에 대해 쿼리를 테스트하려면 **테스트 쿼리를** 선택합니다.

8. 테스트 결과는 쿼리에 따라 표시됩니다. 쿼리를 변경하고 쿼리 **저장을** 선택하여 새 쿼리 논리를 테스트할 수 있습니다. 이렇게 하면 쿼리를 반복적으로 수정하고 다시 테스트하여 출력이 어떻게 변경되는지 확인할 수 있습니다.

9. 쿼리에서 여러 출력을 사용하는 경우 선택한 출력을 기반으로 결과가 표시됩니다. 

   ![Azure 스트림 분석 선택 출력](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. 브라우저에 표시된 결과를 확인한 후 작업을 **시작할** 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Stream Analytics를 사용하여 IoT 솔루션을 빌드합니다:](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)이 자습서에서는 유료 부스에서 트래픽을 시뮬레이션하는 데이터 생성기로 종단 간 솔루션을 빌드할 수 있습니다.

* [Azure 스트림 분석 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [일반적인 Stream Analytics 사용 패턴에 대한 쿼리 예제](stream-analytics-stream-analytics-query-patterns.md)

* [Azure Stream Analytics의 입력 이해](stream-analytics-add-inputs.md)

* [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)
