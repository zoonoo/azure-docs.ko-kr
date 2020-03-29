---
title: Cosmos DB에 Azure Functions 트리거를 사용할 때 문제 해결
description: Cosmos DB에 대한 Azure Functions 트리거를 사용할 때 일반적인 문제, 해결 및 진단 단계
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365511"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB에 Azure Functions 트리거를 사용할 때 문제 진단 및 문제 해결

이 문서에서는 [Cosmos DB에 대한 Azure Functions 트리거를](change-feed-functions.md)사용할 때 일반적인 문제, 해결 방법 및 진단 단계를 다룹니다.

## <a name="dependencies"></a>종속성

Cosmos DB에 대한 Azure Functions 트리거 및 바인딩은 기본 Azure Functions 런타임에 대한 확장 패키지에 따라 달라집니다. 발생할 수 있는 잠재적인 문제를 해결할 수 있는 수정 사항과 새로운 기능이 포함될 수 있으므로 항상 이러한 패키지를 업데이트상태로 유지하십시오.

* Azure 함수 V2의 경우 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)를 참조하십시오.
* Azure 함수 V1의 경우 [Microsoft.Azure.WebJobs.확장.문서DB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)를 참조하십시오.

이 문서에서는 명시적으로 지정하지 않는 한 런타임이 언급될 때마다 항상 Azure Functions V2를 참조합니다.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Azure 코스모스 DB SDK를 독립적으로 사용

확장 패키지의 주요 기능은 Azure Functions 트리거 및 Cosmos DB에 대한 바인딩에 대한 지원을 제공하는 것입니다. 또한 Azure [Cosmos DB .NET SDK를](sql-api-sdk-dotnet-core.md)포함하며 트리거 및 바인딩을 사용하지 않고 프로그래밍 방식으로 Azure Cosmos DB와 상호 작용하려는 경우에 유용합니다.

Azure Cosmos DB SDK를 사용하려면 프로젝트에 다른 NuGet 패키지 참조를 추가하지 않도록 합니다. 대신 **SDK 참조가 Azure Functions의 확장 패키지를 통해 해결하도록 합니다.** Azure Cosmos DB SDK를 트리거 및 바인딩과 별도로 사용합니다.

또한 [Azure Cosmos DB SDK 클라이언트의](./sql-api-sdk-dotnet-core.md)고유한 인스턴스를 수동으로 만드는 경우 [Singleton 패턴 접근 방식을 사용하여](../azure-functions/manage-connections.md#documentclient-code-example-c)클라이언트의 인스턴스가 하나만 있는 패턴을 따라야 합니다. 이 프로세스는 작업에서 잠재적인 소켓 문제를 방지합니다.

## <a name="common-scenarios-and-workarounds"></a>일반적인 시나리오 및 해결 해결

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>오류 메시지 컬렉션이 존재하지 않는 Azure Function에 실패합니다.

Azure Function은 오류 메시지와 함께 "원본 컬렉션 '컬렉션 이름'(데이터베이스 '데이터베이스 이름') 또는 임대 컬렉션 'collection2-name'(데이터베이스 '데이터베이스2-name')이 존재하지 않습니다. 수신기가 시작되기 전에 두 컬렉션이 모두 있어야 합니다. 임대 컬렉션을 자동으로 만들려면 'CreateLeaseCollectionIfNotExists'를 'true'로 설정합니다.

즉, 트리거가 작동하는 데 필요한 Azure Cosmos 컨테이너 중 하나 또는 둘 다 존재하지 않거나 Azure 함수에 연결할 수 없습니다. **오류 자체는 구성에 따라 찾고 있는 트리거인 Azure Cosmos 데이터베이스 및 컨테이너를 알려줍니다.**

1. 특성을 `ConnectionStringSetting` 확인하고 **Azure Function 앱에 있는 설정을 참조합니다.** 이 특성의 값은 연결 문자열 자체가 아니라 구성 설정의 이름이어야 합니다.
2. Azure Cosmos 계정에 `databaseName` 있는지 `collectionName` 확인합니다. 패턴을 사용하여 `%settingName%` 자동 값 대체를 사용하는 경우 Azure Function App에 설정 이름이 있는지 확인합니다.
3. `LeaseCollectionName/leaseCollectionName`을 지정하지 않으면 기본값은 "임대"입니다. 이러한 컨테이너가 있는지 확인합니다. 선택적으로 트리거에서 `CreateLeaseCollectionIfNotExists` 속성을 자동으로 만들도록 `true` 설정할 수 있습니다.
4. Azure [Cosmos 계정의 방화벽 구성을](how-to-configure-firewall.md) 확인하여 Azure 기능을 차단하지 않는 지 확인합니다.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Function이 "공유 처리량 컬렉션에 파티션 키가 있어야 합니다"로 시작하지 못합니다.

Azure Cosmos DB 확장의 이전 버전은 [공유 처리량 데이터베이스](./set-throughput.md#set-throughput-on-a-database)내에서 만들어진 임대 컨테이너를 사용 하 여 지원 되지 않았습니다. 이 문제를 해결하려면 최신 버전을 얻으려면 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) 확장을 업데이트합니다.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure 함수가 "이 작업에 대해 파티션키를 제공해야 합니다."로 시작하지 못합니다.

이 오류는 현재 이전 [확장 종속성이](#dependencies)있는 분할된 임대 컬렉션을 사용하고 있음을 의미합니다. 사용 가능한 최신 버전으로 업그레이드합니다. 현재 Azure Functions V1에서 실행 중인 경우 Azure Functions V2로 업그레이드해야 합니다.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure Function은 "임대 컬렉션이 분할된 경우 id와 동일한 파티션 키가 있어야 합니다."로 시작하지 못합니다.

이 오류는 현재 임대 컨테이너가 분할되었지만 파티션 키 `/id`경로가 없습니다. 이 문제를 해결하려면 파티션 `/id` 키로 임대 컨테이너를 다시 만들어야 합니다.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>"값은 null이 될 수 없습니다. 매개 변수 이름: 트리거를 실행 하려고 할 때 Azure Functions 로그에서 o"

이 문제는 Azure 포털을 사용 하 고 트리거를 사용 하는 Azure 함수를 검사할 때 화면에 **실행** 단추를 선택 하려고 하는 경우에 나타납니다. 시작 실행을 선택 하기 위해 트리거를 필요로 하지 않습니다., Azure Function 배포 될 때 자동으로 시작 됩니다. Azure Portal에서 Azure Function의 로그 스트림을 확인하려면 모니터링되는 컨테이너로 이동하여 새 항목을 삽입하면 트리거 실행이 자동으로 표시됩니다.

### <a name="my-changes-take-too-long-to-be-received"></a>변경 내용을 받는 데 시간이 너무 오래 걸릴 수 있습니다.

이 시나리오에는 여러 원인이 있을 수 있으며 모든 원인을 확인해야 합니다.

1. Azure 함수가 Azure Cosmos 계정과 동일한 지역에 배포되나요? 네트워크 대기 시간을 최적화하려면 Azure 함수와 Azure Cosmos 계정이 동일한 Azure 지역에 공동 배치되어야 합니다.
2. Azure Cosmos 컨테이너가 연속적으로 변경되나요? 간헐적으로 변경되나요?
후자인 경우 저장되는 변경 내용과 변경 내용을 선택하는 Azure 함수 사이에 약간의 지연이 있을 수 있습니다. 이는 내부적으로 트리거가 Azure Cosmos 컨테이너에서 변경 내용을 확인하여 읽기 보류 중인 항목이 없으면 새 변경 내용을 확인하기 전에 높은 RU 사용을 방지하기 위해 구성 가능한 시간(기본값: 5초) 동안 일시 중지하기 때문입니다. 트리거의 [구성](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)에서 `FeedPollDelay/feedPollDelay` 설정을 통해 이 일시 중지 시간을 구성할 수 있습니다(값은 밀리초 단위여야 함).
3. Azure Cosmos 컨테이너는 [요금이 제한될](./request-units.md)수 있습니다.
4. 트리거의 특성을 `PreferredLocations` 사용하여 Azure 영역의 쉼표로 구분된 목록을 지정하여 사용자 지정 기본 설정 연결 순서를 정의할 수 있습니다.

### <a name="some-changes-are-repeated-in-my-trigger"></a>트리거에서 일부 변경 내용이 반복됩니다.

"변경"의 개념은 문서에서 작업입니다. 동일한 문서에 대한 이벤트가 수신되는 가장 일반적인 시나리오는 다음과 같습니다.
* 계정이 최종 일관성을 사용하고 있습니다. 최종 일관성 수준에서 변경 피드를 사용하는 동안 후속 변경 피드 읽기 작업 사이에 중복 이벤트가 있을 수 있습니다(한 읽기 작업의 마지막 이벤트는 다음 작업의 첫 번째 로 표시).
* 문서가 업데이트되고 있습니다. 변경 피드에는 동일한 문서에 대한 여러 작업이 포함될 수 있으며, 해당 문서가 업데이트를 받는 경우 각 업데이트에 대해 여러 이벤트를 선택할 수 있습니다. 동일한 문서에 대해 서로 다른 작업을 구분하는 한 `_lsn` 가지 쉬운 방법은 [각 변경에 대한 속성을](change-feed.md#change-feed-and-_etag-_lsn-or-_ts)추적하는 것입니다. 일치하지 않는 경우 동일한 문서에 대해 다른 변경 내용입니다.
* 에 의해 `id`문서를 식별하는 경우 문서의 고유 식별자가 `id` 파티션 키(동일하지만 `id` 파티션 키가 다른 두 개의 문서가 있을 수 있음)임을 기억하십시오.

### <a name="some-changes-are-missing-in-my-trigger"></a>트리거에 일부 변경 내용이 누락되었습니다.

Azure Cosmos 컨테이너에서 발생한 변경 사항 중 일부가 Azure Function에서 선택되지 않는 경우 수행해야 하는 초기 조사 단계가 있습니다.

Azure Function이 변경 내용을 수신하면 종종 변경 내용을 처리하고 선택적으로 결과를 다른 대상으로 보낼 수 있습니다. 누락된 변경 내용을 조사할 때는 대상이 아닌 수신 지점(Azure Function이 시작될 **때)에서 수신되는 변경 내용을 측정해야** 합니다.

대상에서 일부 변경 내용이 누락된 경우 변경 내용을 받은 후 Azure Function 실행 중에 일부 오류가 발생할 수 있습니다.

이 시나리오에서 가장 좋은 방법은 변경 `try/catch` 내용을 처리할 수 있는 코드 및 루프 내부에 블록을 추가하여 특정 항목 하위 집합에 대한 오류를 감지하고 그에 따라 처리하는 것입니다(추가 분석 또는 재시도를 위해 다른 저장소로 보내). 

> [!NOTE]
> 코드를 실행하는 동안 처리되지 않은 예외가 발생하면 기본적으로 Cosmos DB에 대한 Azure Functions 트리거가 변경 내용 일괄 처리를 다시 시도하지 않습니다. 즉, 변경 내용이 대상에 도착하지 않은 이유는 변경 내용을 처리하지 못했기 때문입니다.

트리거에 의해 일부 변경 내용이 전혀 수신되지 않은 경우 가장 일반적인 시나리오는 **다른 Azure Function이 실행되고**있다는 것입니다. Azure에 배포된 다른 Azure Function 또는 **정확히 동일한 구성(동일한** 모니터링 및 임대 컨테이너)을 가진 개발자의 컴퓨터에서 로컬로 실행되는 Azure Function일 수 있으며, 이 Azure Function은 Azure Function이 처리할 것으로 예상되는 변경 사항의 하위 집합을 훔치고 있습니다.

또한 실행 중인 Azure Function App 인스턴스 수를 알고 있는 경우 시나리오의 유효성을 검사할 수 있습니다. 임대 컨테이너를 검사하고 내 임대 항목 수를 계산하는 경우 `Owner` 해당 속성의 고유한 값은 Function App의 인스턴스 수와 같아야 합니다. 알려진 Azure Function App 인스턴스보다 소유자가 더 많은 경우 이러한 추가 소유자가 변경 내용을 "도용"하는 소유자임을 의미합니다.

이 상황을 해결 하는 한 가지 쉬운 `LeaseCollectionPrefix/leaseCollectionPrefix` 방법은 새/다른 값으로 함수에 적용 하거나, 또는 새 임대 컨테이너와 테스트 하는 것입니다.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>처음부터 컨테이너의 모든 항목을 다시 시작하고 다시 처리해야 합니다. 
컨테이너의 모든 항목을 처음부터 다시 처리하려면 다음을 수행합니다.
1. Azure 기능이 현재 실행 중인 경우 중지합니다. 
1. 임대 컬렉션의 문서를 삭제하거나 임대 컬렉션을 삭제하고 다시 만들어 비어 있음)
1. 시작 [시작](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDB트리거 함수의 속성을 true로 설정합니다. 
1. Azure 함수를 다시 시작합니다. 이제 처음부터 모든 변경 내용을 읽고 처리합니다. 

[StartFrom 시작에서](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) true로 설정하면 Azure 함수가 현재 시간 대신 컬렉션 기록의 시작 부분에서 변경 내용을 읽기 시작하도록 지시합니다. 이는 이미 생성된 임대(즉, 임대 컬렉션의 문서)가 없는 경우에만 작동합니다. 이미 생성된 임대가 있는 경우 이 속성을 true로 설정하면 효과가 없습니다. 이 시나리오에서는 함수가 중지 되 고 다시 시작 될 때 임대 컬렉션에 정의 된 대로 마지막 검사에서 읽기 시작 됩니다. 처음부터 다시 처리하려면 위의 1-4단계를 따릅니다.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>바인딩은 IReadOnlyList\<문서> 또는 JArray에서만 수행할 수 있습니다.

이 오류는 Azure Functions 프로젝트(또는 참조된 프로젝트)에 Azure Functions Cosmos DB 확장에서 제공하는 버전과 다른 버전으로 Azure Cosmos DB SDK에 대한 수동 NuGet 참조가 포함되어 있는 경우 [발생합니다.](./troubleshoot-changefeed-functions.md#dependencies)

이 문제를 해결하려면 추가된 수동 NuGet 참조를 제거하고 Azure Functions Cosmos DB 확장 패키지를 통해 Azure Cosmos DB SDK 참조가 해결되도록 합니다.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>검색 변경 사항에 대한 Azure Function의 폴링 간격 변경

앞에서 설명한 대로 [내 변경 내용을 수신하는 데 시간이 너무 오래 걸리므로](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)Azure 함수는 높은 RU 소비를 피하기 위해 새 변경 내용을 확인하기 전에 구성 가능한 시간(기본적으로 5초)동안 절전 모드로 유지됩니다. 트리거의 [구성](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)에서 `FeedPollDelay/feedPollDelay` 설정을 통해 이 일시 중지 시간을 구성할 수 있습니다(값은 밀리초 단위여야 함).

## <a name="next-steps"></a>다음 단계

* [Azure 기능에 대한 모니터링 사용](../azure-functions/functions-monitoring.md)
* [Azure 코스모스 DB .NET SDK 문제 해결](./troubleshoot-dot-net-sdk.md)
