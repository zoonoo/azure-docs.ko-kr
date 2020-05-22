---
title: 데이터 오류 코드 - Azure Stream Analytics
description: 데이터 오류 코드의 Azure Stream Analytics 문제를 해결합니다.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: f7383a56a11ac9b567c80e73cc84944174c30ac8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594092"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Azure Stream Analytics 구성 오류 코드

활동 로그 및 리소스 로그를 사용하여 Azure Stream Analytics 작업에서 예기치 않은 동작을 디버그할 수 있습니다. 이 문서에는 모든 데이터 오류 코드에 대한 설명이 나와 있습니다. 예기치 않은 레코드 스키마와 같이 스트림에 잘못된 데이터가 있는 경우 데이터 오류가 발생합니다.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **원인**: 입력 데이터를 역직렬화하는 동안 오류가 발생했습니다.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **원인**: Stream Analytics에서 리소스에 대한 타임스탬프를 가져올 수 없습니다. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **원인**: Stream Analytics에서 `TIMESTAMP BY OVER COLUMN`의 값을 가져올 수 없습니다.

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **원인**: 입력 이벤트가 구성된 허용 오차보다 나중에 전송되었습니다.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **원인**: 입력 이벤트 도착 시간이 입력 이벤트 애플리케이션 타임스탬프 임계값보다 빠릅니다.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **원인**: Azure Functions에 대한 메시지 출력이 크기 제한을 초과합니다.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **원인**: 출력 레코드가 이벤트 허브에 쓸 때의 최대 크기 제한을 초과합니다.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **원인**: 특정 열의 값 또는 형식이 잘못되었습니다.
* **권장 사항**: 255자 이하의 고유한 비어 있지 않은 문자열을 입력합니다.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **원인**: 출력 레코드의 문서 ID에 잘못된 문자가 포함되어 있습니다.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **원인**: 출력 레코드에 기본 키 속성으로 사용할 열 \[id]가 포함되어 있지 않았습니다.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **원인**: 출력 레코드에 문서 ID 속성이 포함되어 있지 않습니다. 
* **권장 사항**: 쿼리 출력에 ‘255’자 미만의 고유한 비어 있지 않은 문자열이 있는 열이 포함되어 있는지 확인합니다.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **원인**: 출력 레코드에 파티션 키 속성으로 사용할 열이 누락되었습니다.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **원인**: Cosmos DB에 대한 단일 레코드 쓰기가 너무 큽니다.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **원인**: Stream Analytics가 데이터의 문제로 인해 SQL Database에 이벤트를 쓸 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [입력 연결 문제 해결](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics 출력 문제 해결](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics 쿼리 문제 해결](stream-analytics-troubleshoot-query.md)
* [리소스 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics 데이터 오류](data-errors.md)
