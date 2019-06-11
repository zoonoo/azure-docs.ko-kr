---
title: 진단 및 Azure Functions에서 Azure Cosmos DB 트리거를 사용 하는 경우 문제 해결
description: 일반적인 문제, 해결 방법 및 Azure Functions를 사용 하 여 Azure Cosmos DB 트리거를 사용 하는 경우 진단 단계
author: ealsur
ms.service: cosmos-db
ms.date: 05/23/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09ea70ac302806b4cb0e97fde92dda4208e3d659
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734510"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>진단 및 Azure Functions에서 Azure Cosmos DB 트리거를 사용 하는 경우 문제 해결

이 문서에서는 일반적인 문제, 해결 방법 및 진단 단계를 사용 하는 경우는 [Azure Cosmos DB 트리거](change-feed-functions.md) Azure Functions를 사용 합니다.

## <a name="dependencies"></a>종속성

기본 Azure Functions 런타임을 통해 확장 패키지에는 Azure Cosmos DB 트리거 및 바인딩을 따라 달라 집니다. 수정 및 발생할 수 있는 모든 잠재적인 문제를 다루는 새 기능을 포함할 수 있습니다 이러한 패키지 업데이트, 항상 유지 합니다.

* Azure Functions V2에 대 한 참조 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)합니다.
* Azure Functions V1에 대 한 참조 [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)합니다.

이 문서는 항상 참조 Azure Functions V2 런타임이 언급 될 때마다 명시적으로 지정 하지 않는 한 합니다.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Azure Cosmos DB SDK를 독립적으로 사용

확장 패키지의 핵심 기능에 Azure Cosmos DB 트리거 및 바인딩에 대 한 지원을 제공 하는 것입니다. 또한 합니다 [Azure Cosmos DB.NET SDK](sql-api-sdk-dotnet-core.md), 트리거 및 바인딩을 사용 하지 않고 프로그래밍 방식으로 Azure Cosmos DB를 사용 하 여 상호 작용 하려는 경우 유용 합니다.

하는 경우 Azure Cosmos DB SDK를 사용 하는 추가 하지 말아야 프로젝트에 다른 NuGet 패키지 참조를 확인 합니다. 대신 **Azure Functions 확장 패키지를 통해 해결 SDK 참조 하도록**합니다. 트리거 및 바인딩을 별도로 Azure Cosmos DB SDK 사용

또한의 고유한 인스턴스를 수동으로 만들려는 경우 합니다 [Azure Cosmos DB SDK 클라이언트](./sql-api-sdk-dotnet-core.md), 클라이언트의 인스턴스를 하나만 갖는 패턴을 따라야 [Singleton 패턴 방식을 사용 하 여](../azure-functions/manage-connections.md#documentclient-code-example-c) . 이 프로세스는 작업에서 잠재적인 소켓 문제를 방지 합니다.

## <a name="common-scenarios-and-workarounds"></a>일반적인 시나리오 및 해결 방법

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>오류 메시지 컬렉션을 사용 하 여 azure 함수 실패 없습니다.

Azure 함수는 오류 메시지와 함께 실패 "중 하나는 소스 컬렉션 ' 컬렉션-name' (데이터베이스 ' 데이터베이스-name') 또는 임대 컬렉션 ' collection2-name' (데이터베이스 ' database2-name')가 없는 합니다. 두 컬렉션에는 수신기를 시작 하기 전에 존재 해야 합니다. 임대 컬렉션을 자동으로 만들려면 'CreateLeaseCollectionIfNotExists' to 'true' 설정 "

즉, 하나 또는 둘 다가 필요한 작업 트리거에 대 한 Azure Cosmos 컨테이너의 존재 하지 않거나 Azure Function에 연결할 수 있습니다. **오류 자체가 알려 Azure Cosmos 데이터베이스 및 컨테이너는 트리거를 찾고** 구성을 기반으로 합니다.

1. 확인 합니다 `ConnectionStringSetting` 특성과 한다는 **Azure 함수 앱에 있는 설정을 참조**합니다. 이 특성의 값은 연결 문자열 자체가, 하지만 구성 설정의 이름을 아니어야 합니다.
2. 있는지 확인 합니다 `databaseName` 및 `collectionName` Azure Cosmos 계정에 존재 합니다. 자동 값 대체를 사용 하는 경우 (사용 하 여 `%settingName%` 패턴), Azure 함수 앱에서 설정의 이름을 존재 하는지 확인 합니다.
3. 지정 하지 않으면는 `LeaseCollectionName/leaseCollectionName`, 기본값은 "임대"입니다. 이러한 컨테이너가 있는지 확인 합니다. 필요에 따라 설정할 수 있습니다는 `CreateLeaseCollectionIfNotExists` 를 트리거 특성 `true` 자동으로 만들어야 합니다.
4. 확인 프로그램 [Azure Cosmos 계정의 방화벽 구성을](how-to-configure-firewall.md) 아님을 표시 되도록 하려면 Azure Function을 차단 하지.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Function "공유 처리량 컬렉션에는 파티션 키" 시작 실패

내에서 생성 된 임대 컨테이너를 사용 하 여 이전 버전의 Azure Cosmos DB 확장 프로그램을 지원 하지 않았습니다를 [공유 처리량 데이터베이스](./set-throughput.md#set-throughput-on-a-database)합니다. 이 문제를 해결 하려면 업데이트 된 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) 확장이 최신 버전을 가져올 수 있습니다.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 함수가 실패 하는 시작 하기 "임대 컬렉션을 분할 하는 경우 키가 있어야 합니다 파티션 id 같음."

이 오류는 현재 임대 컨테이너 분할 되어 있지만 파티션 키 경로 아닙니다 의미 `/id`합니다. 이 문제를 해결 하려면 사용 하 여 임대 컨테이너를 다시 만들 필요가 `/id` 파티션 키로 합니다.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>표시 된 "값은 null 일 수 없습니다. 매개 변수 이름: o "트리거를 실행 하려고 할 때 Azure Functions 로그에서

Azure portal을 사용 하 고 선택 하려는 경우이 문제가 표시 되는 **실행** 트리거를 사용 하는 Azure Function을 검사 하는 경우 화면의 단추입니다. 트리거가 필요 하지 않습니다를 시작 하려면 실행을 선택할 수 있는 Azure Function 배포 될 때 자동으로 시작 됩니다. Azure portal에서 Azure Function의 로그 스트림 확인, 방금 모니터링 된 컨테이너로 이동 하 고 일부 새 항목을 삽입 하려는 경우 실행 하는 트리거를 자동으로 표시 됩니다.

### <a name="my-changes-take-too-long-be-received"></a>내 변경 내용이 너무 오래 수신

이 시나리오는 여러 원인이 있을 수 있습니다 하 고 모두 확인 해야 합니다.

1. Azure Cosmos 계정과 동일한 지역에 배포 된 Azure Function? 최적의 네트워크 대기 시간에 대 한 Azure Function 및 Azure Cosmos 계정 모두 동일한 Azure 지역에 공동 배치 해야 합니다.
2. 연속 또는 산발적 인 Azure Cosmos 컨테이너에서 발생 하는 변경 내용을?
후자의 경우 저장 되 고 변경 내용을 선택 하는 Azure Function 사이의 약간의 지연이 있을 수 있습니다. 내부적으로 트리거가 Azure Cosmos 컨테이너에서 변경 내용을 확인 하 고 보류 중인 none 읽을 발견 하는 경우이 대기 시간 (기본적으로 5 초)의 구성 가능한 기간에 대 한 새로운 변경 사항 (높은 RU 소비를 방지)를 확인 하기 전에 때문입니다. 통해이 절전 모드 시간을 구성할 수 있습니다는 `FeedPollDelay/feedPollDelay` 에서 설정 합니다 [구성](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) (값을 밀리초 단위로 되도록 예상) 하 고 트리거의 합니다.
3. Azure Cosmos 컨테이너 수 있습니다 [속도가 제한](./request-units.md)합니다.
4. 사용할 수는 `PreferredLocations` 사용자 지정 기본 연결 순서를 정의 하기 위해 Azure 지역의 쉼표로 구분 된 목록을 지정 하 여 트리거에서 특성입니다.

### <a name="some-changes-are-missing-in-my-trigger"></a>트리거 내에서 누락 된 일부 변경 내용

일부 Azure Cosmos 컨테이너에서 발생 한 변경 되 고 선택 되지 Azure Function에 의해를 찾은 경우에 수행 해야 하는 초기 조사 단계입니다.

Azure Function에 변경 내용을 받으면 해당 자주 처리 하 고 필요에 따라, 다른 대상으로 결과 보냅니다. 누락 된 변경 내용을 조사 하는 경우 했는지 **변경 내용을 수집 지점에서 수신 되는 측정값** (Azure Function 시작 되 면) 대상에 없습니다.

일부 변경 내용이 없는 경우 대상에 변경 내용을 받은 후 Azure Function 실행 하는 동안 발생 하는 일부 오류는이 의미할 수 있습니다.

이 시나리오에서는 최선의 조치를 추가 하는 것 `try/catch blocks` 있습니다 항목의 특정 하위 집합에 대 한 모든 오류를 감지 하 고 적절 하 게 처리 하 여 변경 내용을 처리 루프 내에서 코드에 (보낼 다른 저장소에 대 한 추가 분석 또는 다시 시도)입니다. 

> [!NOTE]
> Azure Cosmos DB 트리거를 기본적으로 없습니다 다시 시도 하세요 변경 내용의 일괄 처리에 코드 실행 하는 동안 처리 되지 않은 예외가 발생 했습니다. 이 변경 내용을 대상에 도착 하지 않은 이유는 처리에 실패 한 것을 의미 합니다.

트리거에 의해 일부 변경 내용이 전혀 받지 못해서에 가장 일반적인 시나리오를 찾아야 하는 경우 **다른 Azure Function 실행 되 고**입니다. Azure에 배포 된 Azure 함수를 다른 수 또는 개발자의 컴퓨터에서 로컬로 실행 하는 Azure 함수에 **동일한 구성을 정확 하 게** (동일한 모니터링 및 컨테이너 임대) 및이 Azure Function은 가로채기는 변경 내용 처리 하기 위해 Azure 함수를 예상할 수의 하위 집합입니다.

또한 시나리오의 유효성을 검사할 수, Azure 함수 앱 인스턴스 개수를 알고 있는 경우를 실행 합니다. 임대 컨테이너를 검사 하 고 고유한 값에 포함 된 임대 항목 수를 계산 하는 경우는 `Owner` 속성에 함수 앱의 인스턴스 수와 동일 해야 합니다. 알려진된 Azure 함수 앱 인스턴스가 보다 더 많은 소유자 인 경우 이러한 추가 소유자 "도용" 변경 내용을 하나는 의미 합니다.

이 상황을 적용할 쉬운 해결 방법 중 하나는 `LeaseCollectionPrefix/leaseCollectionPrefix` 새로운/다른 값을 사용 하 여 함수 또는 새 임대 컨테이너를 사용 하 여 테스트 또는 합니다.

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>바인딩 IReadOnlyList를 사용 하 여 수행할 수 있습니다<Document> 또는 JArray

Azure Functions 프로젝트 (또는 참조 프로젝트)에서 제공 하는 것 보다 다양 한 버전을 사용 하 여 Azure Cosmos DB SDK에 대 한 수동 NuGet 참조를 포함 하는 경우이 오류가 발생 합니다 [Azure Functions Cosmos DB 확장](./troubleshoot-changefeed-functions.md#dependencies)합니다.

문제를 해결 하려면이 경우, Azure Functions Cosmos DB 확장 패키지를 통해 해결 추가한 및 Azure Cosmos DB SDK 참조를 사용 하는 수동 NuGet 참조를 제거 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Functions에 대 한 모니터링을 사용 하도록 설정](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB.NET SDK 문제 해결](./troubleshoot-dot-net-sdk.md)