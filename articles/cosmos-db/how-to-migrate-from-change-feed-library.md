---
title: 변경 피드 프로세서 라이브러리에서 Azure Cosmos DB .NET V3 SDK로 마이그레이션
description: 변경 피드 프로세서 라이브러리를 사용하여 Azure Cosmos DB SDK V3로 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588886"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>변경 피드 프로세서 라이브러리에서 Azure Cosmos DB .NET V3 SDK로 마이그레이션

이 문서에서는 [변경 피드 프로세서 라이브러리를](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) 사용하는 기존 응용 프로그램의 코드를 .NET SDK(.NET V3 SDK)의 최신 버전의 [변경 피드](change-feed.md) 기능으로 마이그레이션하는 데 필요한 단계를 설명합니다.

## <a name="required-code-changes"></a>필요한 코드 변경

.NET V3 SDK에는 몇 가지 주요 변경 사항이 있으며 응용 프로그램을 마이그레이션하는 주요 단계는 다음과 같습니다.

1. 인스턴스를 `DocumentCollectionInfo` 모니터링되는 컨테이너에 대한 참조로 `Container` 변환하고 컨테이너를 임대합니다.
1. `WithProcessorOptions` 사용 하는 사용자 지정 사용 `WithLeaseConfiguration` 하 `WithPollInterval` 고 `WithStartTime` 간격, 시작 시간에 대 [한](how-to-configure-change-feed-start-time.md)및 최대 항목 수를 정의 `WithMaxItems` 하려면 업데이트 해야 합니다.
1. `processorName` 에 구성된 `GetChangeFeedProcessorBuilder` `ChangeFeedProcessorOptions.LeasePrefix`값과 일치하도록 on을 `string.Empty` 설정하거나 그렇지 않으면 사용합니다.
1. 변경 내용은 더 이상 `IReadOnlyList<Document>`로 전달되지 않습니다. `IReadOnlyCollection<T>` `T`
1. 변경 내용을 처리하려면 더 이상 구현이 필요하지 않으며 대신 [대리자를 정의해야](change-feed-processor.md#implementing-the-change-feed-processor)합니다. 대리자는 정적 Function이거나 실행 간에 상태를 유지해야 하는 경우 고유한 클래스를 만들고 인스턴스 메서드를 대리자로 전달할 수 있습니다.

예를 들어 변경 피드 프로세서를 빌드하는 원래 코드가 다음과 같습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

마이그레이션된 코드는 다음과 같습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

그리고 대리자는 정적 메서드일 수 있습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>상태 및 임대 컨테이너

변경 피드 프로세서 라이브러리와 마찬가지로 .NET V3 SDK의 변경 피드 기능은 [임대 컨테이너를](change-feed-processor.md#components-of-the-change-feed-processor) 사용하여 상태를 저장합니다. 그러나 스키마는 다릅니다.

SDK V3 변경 피드 프로세서는 이전 라이브러리 상태를 검색하고 마이그레이션된 응용 프로그램 코드를 처음 실행할 때 자동으로 새 스키마로 마이그레이션합니다. 

이전 코드를 사용하여 응용 프로그램을 안전하게 중지하고, 코드를 새 버전으로 마이그레이션하고, 마이그레이션된 응용 프로그램을 시작하고, 응용 프로그램이 중지되는 동안 발생한 모든 변경 내용을 새 버전에서 선택및 처리할 수 있습니다.

> [!NOTE]
> 상태(임대)가 새 스키마로 마이그레이션되므로 라이브러리를 사용하는 응용 프로그램에서 .NET V3 SDK로의 마이그레이션은 단방향입니다. 마이그레이션은 이전 버전과 호환되지 않습니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub의 사용 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub의 추가 샘플](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드 프로세서에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 프로세서 개요](change-feed-processor.md)
* [변경 피드 평가기 사용](how-to-use-change-feed-estimator.md)
* [변경 피드 프로세서 시작 시간](how-to-configure-change-feed-start-time.md)
