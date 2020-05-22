---
title: 외부 오류 코드 - Azure Stream Analytics
description: 외부 오류 코드의 Azure Stream Analytics 문제를 해결합니다.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: e7ef1adea72ed69dc11f0ea8c7e3ce0db3e6ab7c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594082"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Azure Stream Analytics 외부 오류 코드

활동 로그 및 리소스 로그를 사용하여 Azure Stream Analytics 작업에서 예기치 않은 동작을 디버그할 수 있습니다. 이 문서에서는 모든 외부 오류 코드에 대한 설명을 나열합니다. 외부 오류는 Stream Analytics에서 데이터 오류, 구성 오류 또는 외부 가용성 오류로 구분할 수 없는 업스트림 또는 다운스트림 서비스에서 throw되는 일반 오류입니다.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **원인**: 어댑터를 초기화하는 동안 오류가 발생했습니다.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **원인**: 어댑터에 데이터를 쓰는 중 오류가 발생했습니다.

## <a name="kafkaservererror"></a>KafkaServerError

* **원인**: Kafka 서버가 오류를 반환했습니다.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **원인**: Azure Functions에서 HTTP 오류가 반환되었습니다.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **원인**: Stream Analytics에서 Azure 함수에 이벤트를 쓰지 못했습니다.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **원인**: Azure Functions에 출력할 때 리디렉션 오류가 발생합니다.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **원인**: Azure Functions에 출력할 때 클라이언트 오류가 발생합니다.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **원인**: Azure Functions에 출력할 때 서버 오류가 발생합니다.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **원인**: HTTP 요청이 시간 제한을 초과하여 Azure Functions에 쓰지 못했습니다. 
* **권장 사항**: Azure Functions 로그에서 잠재적 지연을 확인합니다.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **원인**: 입력 오프셋이 잘못되었습니다. 이는 장애 조치(failover)로 인한 것일 수 있습니다.
* **권장 사항**: 마지막 출력 시간에서 Stream Analytics 작업을 다시 시작합니다.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **원인**: 이벤트 허브에 데이터를 보내는 동안 오류가 발생했습니다.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **원인**: Stream Analytics에서 최대 재시도 수를 초과한 후 Cosmos DB 계정에 연결하지 못했습니다.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **원인**: Stream Analytics에서 최대 재시도 수를 초과한 후 Cosmos DB 데이터베이스 및 컬렉션을 쿼리하지 못했습니다.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **원인**: CosmosDB에서 여러 번 재시도 후에 저장 프로시저를 만들 수 없습니다.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **원인**: upsert 저장 프로시저에서 오류를 반환했습니다. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **원인**: Stream Analytics에서 SQL Database 출력을 초기화할 수 없습니다.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **원인**: Stream Analytics에서 SQL Database 출력에 이벤트를 쓸 수 없습니다.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **원인**: Synapse SQL 풀 출력을 초기화하는 동안 오류가 발생했습니다.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **원인**: Synapse SQL 풀에 출력을 쓰는 동안 오류가 발생했습니다.

## <a name="next-steps"></a>다음 단계

* [입력 연결 문제 해결](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics 출력 문제 해결](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics 쿼리 문제 해결](stream-analytics-troubleshoot-query.md)
* [리소스 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics 데이터 오류](data-errors.md)
