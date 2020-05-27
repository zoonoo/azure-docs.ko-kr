---
title: 구성 오류 코드 - Azure Stream Analytics
description: 구성 오류 코드의 Azure Stream Analytics 문제를 해결합니다.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 5aa15ae4a234a56a172a0166070c32be4f822910
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650063"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Azure Stream Analytics 구성 오류 코드

활동 로그 및 리소스 로그를 사용하여 Azure Stream Analytics 작업에서 예기치 않은 동작을 디버그할 수 있습니다. 이 문서에는 모든 구성 오류 코드에 대한 설명이 나와 있습니다. 구성 오류는 작업 구성 또는 입력 및 출력 구성과 관련이 있습니다.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **원인**: 이벤트 허브에서 *권한 없는 액세스* 오류를 throw했습니다.

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **원인**: Epoch 값이 서로 다른 이벤트 허브 수신기가 두 개 이상 있습니다.
* **권장 사항**: Stream Analytics 작업이 실행되는 동안*Service Bus Explorer* 또는 *EventProcessorHost* 애플리케이션이 연결되어 있지 않은지 확인합니다.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **원인**: 소비자 그룹의 파티션당 허용된 최대 수신자 수에 도달했기 때문에 Stream Analytics에서 파티션에 연결할 수 없습니다.
* **권장 사항**: 다른 Stream Analytics 작업 또는 Service Bus Explorer에서 동일한 소비자 그룹을 사용하고 있지 않은지 확인합니다.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **원인**: 제한으로 인해 이벤트 허브에 데이터를 쓰는 동안 오류가 발생했습니다.
* **권장 사항**: 이 문제가 계속 발생하면 처리량을 업그레이드하세요.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **원인**: 제공된 연결 구성이 올바르지 않습니다.
* **권장 사항**: 구성을 수정하고 작업을 다시 시작합니다.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **원인**: 이벤트 허브 호스트에 연결할 수 없습니다.
* **권장 사항**: 제공된 호스트 이름이 올바른지 확인합니다.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **원인**: EventHub 발신자에 예기치 않은 EventHub 파티션 수가 발생했습니다.
* **권장 사항**: EventHub의 파티션 수가 변경된 경우 Stream Analytics 작업을 다시 시작합니다.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **원인**: Stream Analytics가 데이터베이스에서 특정 Cosmos DB 컬렉션의 파티션 키를 찾을 수 없습니다.
* **권장 사항**: Cosmos DB의 컬렉션에 대해 올바른 파티션 키가 지정되어 있는지 확인합니다.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **원인**: 파티션 키가 리프 노드나 최상위 수준이 아닌 경우 throw됩니다.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **원인**: 다른 열이 기본 키 속성으로 선택된 경우에는 쿼리 출력에 열 \[id]가 포함될 수 없습니다.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **원인**: Stream Analytics가 CosmosDB 데이터베이스를 찾을 수 없습니다.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **원인**: Stream Analytics가 데이터베이스에서 특정 Cosmos DB 컬렉션을 찾을 수 없습니다.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **원인**: Cosmos DB의 제한으로 인해 데이터를 쓰는 동안 오류가 발생했습니다.
* **권장 사항**: 컬렉션 성능 계층을 업그레이드하고 데이터베이스의 성능을 조정합니다.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **원인**: Stream Analytics 작업에서 인증 오류가 발생했습니다.
* **권장 사항**: SQL Database 연결 문자열이 정확한지 확인하세요.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **원인**: Stream Analytics 작업에서 인증 오류가 발생했습니다. 
* **권장 사항**: 계정 이름이 제대로 구성되어 있고 작업의 관리 ID에 SQL Database에 대한 액세스 권한이 있는지 확인하세요.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **원인**: Stream Analytics가 특정 테이블에 대한 스키마 정보를 찾을 수 없습니다.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **원인**: SQL Database가 지원되지 않습니다.
* **권장 사항**: Synapse SQL 풀을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [입력 연결 문제 해결](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics 출력 문제 해결](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics 쿼리 문제 해결](stream-analytics-troubleshoot-query.md)
* [리소스 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics 데이터 오류](data-errors.md)
