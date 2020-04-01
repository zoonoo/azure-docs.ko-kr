---
title: Azure Cosmos DB .NET V3 SDK에서 대량 실행기 라이브러리에서 대량 지원으로 마이그레이션
description: 응용 프로그램을 대량 실행기 라이브러리를 사용하여 Azure Cosmos DB SDK V3의 대량 지원으로 마이그레이션하는 방법에 대해 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: maquaran
ms.openlocfilehash: e1a2a5d849d3c94d62b8645c41f288ba130aa6a4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479332"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Azure Cosmos DB .NET V3 SDK에서 대량 실행기 라이브러리에서 대량 지원으로 마이그레이션

이 문서에서는 [.NET 대량 실행기 라이브러리를](bulk-executor-dot-net.md) 사용하는 기존 응용 프로그램의 코드를 최신 버전의 [대량 지원](tutorial-sql-api-dotnet-bulk-import.md) 기능으로 마이그레이션하는 데 필요한 단계를 설명합니다.

## <a name="enable-bulk-support"></a>대량 지원 사용

`CosmosClient` [AllowBulk실행](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) 구성을 통해 인스턴스에서 대량 지원을 사용하도록 설정합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>각 작업에 대한 작업 만들기

.NET SDK의 일괄 지원은 [작업 병렬 라이브러리를](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) 활용하고 동시에 발생하는 작업을 그룹화하여 작동합니다. 

문서 또는 작업 목록을 입력 매개 변수로 사용하는 단일 메서드는 없지만 대량으로 실행하려는 각 작업에 대해 작업을 만들어야 합니다.

예를 들어 초기 입력이 각 항목에 다음 스키마가 있는 항목 목록인 경우:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

대량 가져오기(BulkExecutor.BulkImportAsync 사용 과 유사)를 수행하려면 각 항목 `CreateItemAsync` 값에 대한 동시 호출이 있어야 합니다. 다음은 그 예입니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

대량 *업데이트를* 수행하려면(BulkExecutor.BulkUpdateAsync 사용과 유사) 항목 값을 업데이트한 `ReplaceItemAsync` 후 메서드에 대한 동시 호출이 있어야 합니다. [BulkExecutor.BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync) 다음은 그 예입니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

`DeleteItemAsync`대량 `id` *delete* [삭제(BulkExecutor.BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)사용 과 유사)를 수행하려면 각 항목의 및 파티션 키를 사용하여 에 대한 동시 호출이 필요합니다. 다음은 그 예입니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>캡처 작업 결과 상태

이전 코드 예제에서는 동시 작업 목록을 만들고 각 작업에 대한 `CaptureOperationResponse` 메서드를 호출했습니다. 이 메서드는 오류를 캡처하고 [요청 단위 사용량을](request-units.md)추적하여 BulkExecutor와 *유사한 응답 스키마를* 유지할 수 있는 확장입니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

`OperationResponse` 이가 선언되는 위치:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>동시에 작업을 실행

작업 목록이 정의된 후 모두 완료될 때까지 기다립니다. 다음 코드 조각에 표시된 대로 대량 작업 범위를 정의하여 작업의 완료를 추적할 수 있습니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>통계 캡처

이전 코드는 모든 작업이 완료될 때까지 대기하고 필요한 통계를 계산합니다. 이러한 통계는 대량 실행기 라이브러리의 [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)의 통계와 유사합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

다음이 `BulkOperationResponse` 포함되어 있습니다.

1. 대량 지원을 통해 작업 목록을 처리하는 데 걸린 총 시간입니다.
1. 성공한 작업 수입니다.
1. 소비된 요청 단위의 총입니다.
1. 오류가 있는 경우 로깅 및 식별 목적으로 예외 및 관련 항목이 포함된 tuples 목록이 표시됩니다.

## <a name="performance-improvements"></a>성능 향상

.NET SDK의 다른 작업과 마찬가지로 스트림 API를 사용하면 성능이 향상되고 불필요한 직렬화가 방지됩니다. 

스트림 API를 사용하는 것은 사용하는 데이터의 특성이 바이트 스트림(예: 파일 스트림)의 데이터와 일치하는 경우에만 가능합니다. 이러한 `CreateItemStreamAsync`경우 에서 를 `ReplaceItemStreamAsync`사용 `DeleteItemStreamAsync` 하 여 `ResponseMessage` 또는 `ItemResponse`메서드를 사용 하 고 (대신) 작업 달성 될 수 있는 처리량을 증가 합니다.

## <a name="next-steps"></a>다음 단계

* .NET SDK 릴리스에 대한 자세한 내용은 [Azure 코스모스 DB SDK](sql-api-sdk-dotnet.md) 문서를 참조하십시오.
* GitHub에서 전체 [마이그레이션 소스 코드를](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) 가져옵니다.
* [GitHub에 추가 대량 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
