---
title: Azure Functions에서 Azure Cosmos DB 변경 피드를 사용하는 방법
description: Azure Functions에서 Azure Cosmos DB 변경 피드 사용
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 943cd79237290cd91d56cc1c51f5d773cdb16634
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636393"
---
# <a name="how-to-use-azure-cosmos-db-change-feed-with-azure-functions"></a>Azure Functions에서 Azure Cosmos DB 변경 피드를 사용하는 방법

Azure Functions를 사용하는 경우 변경 피드에 연결하는 가장 간단한 방법은 Azure Functions 앱에 [Azure Cosmos DB 트리거](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)를 추가하는 것입니다. Azure Functions 앱에서 Cosmos DB 트리거를 만들 때 연결할 Cosmos 컨테이너를 선택하면 컨테이너의 항목이 변경될 때마다 함수가 트리거됩니다.

트리거는 Azure Functions 포털에서, Azure Cosmos DB 포털에서 또는 프로그래밍 방식으로 만들 수 있습니다. 자세한 내용은 [Azure Cosmos DB 및 Azure Functions를 사용하는 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md)을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>특정 지역에서 데이터를 읽도록 Azure Functions를 구성하려면 어떻게 해야 하나요?

Azure Cosmos DB 트리거를 사용하여 지역 목록을 지정할 때 [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations)를 정의할 수 있습니다. 트리거가 선호 지역에서 데이터를 읽도록 만들기 위해 ConnectionPolicy를 사용자 지정하는 것과 동일합니다. Azure Functions가 배포된 가장 가까운 지역에서 데이터를 읽는 것이 가장 이상적입니다.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Azure Functions의 기본 일괄 처리 크기는 얼마입니까?

기본 크기는 Azure Functions 호출마다 항목 100개입니다. 하지만 function.json 파일 내에서 이 숫자를 구성할 수 있습니다. 다음은 완전한 [구성 옵션 목록](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)입니다. 로컬에서 개발하는 경우 local.settings.json 파일 내에서 애플리케이션 설정을 업데이트해야 합니다.

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>저는 컨테이너를 모니터링하고 변경 피드를 읽고 있는데, 삽입된 문서 중 일부를 얻지 못하고 일부 문서가 누락됩니다.

동일한 임대 컨테이너를 사용하여 동일한 컨테이너를 읽는 다른 Azure 함수가 있는지 확인하세요. 누락된 문서는 동일한 임대를 사용하는 다른 Azure Functions에서 처리됩니다.

따라서 동일한 변경 피드를 읽는 Azure Functions를 여러 개 만드는 경우 함수끼리 서로 다른 임대 컨테이너를 사용하거나 "leasePrefix" 구성을 사용하여 동일한 컨테이너를 공유해야 합니다. 그러나 변경 피드 프로세서 라이브러리를 사용하는 경우 Azure 함수의 여러 인스턴스를 시작하면 SDK가 자동으로 문서를 여러 인스턴스에 분배합니다.

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Azure Cosmos 항목이 1초마다 업데이트되는데, 변경 피드를 수신 대기하는 Azure Functions에서 변경 내용 중 일부를 가져올 수 없습니다.

Azure Functions는 변경 내용에 대한 변경 피드를 지속적으로 폴링하며, 최대 기본 지연 시간은 5초입니다. 읽어야 하는 보류 중인 변경 내용이 없거나 트리거가 적용된 후 보류 중인 변경 내용이 있으면 함수가 해당 변경 내용을 즉시 읽습니다. 그러나 보류 중인 변경 내용이 없으면 함수는 5초 동안 기다렸다가 더 많은 변경 내용을 폴링합니다.

해당 문서가 동일한 간격으로 여러 변경 내용을 수신하고 그 간격에 따라 트리거가 새 변경 내용을 폴링하는 경우 문서의 중간 버전이 아닌 최신 버전이 수신될 수 있습니다.

변경 피드를 폴링하는 시간을 5초 미만으로 줄이려면(예를 들어 매 1초), 폴링 시간 "feedPollDelay"를 구성하면 됩니다. [전체 구성](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations)을 참조하세요. 밀리초 단위로 정의되며 기본값은 5000입니다. 폴링 시간을 1초 미만으로 낮출 수 있지만 CPU 메모리 사용량이 많아지기 때문에 권장하지는 않습니다.

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>여러 Azure Functions가 한 컨테이너의 변경 피드를 읽을 수 있나요?

예. 여러 Azure Functions가 동일한 컨테이너의 변경 피드를 읽을 수 있습니다. 그러나 Azure Functions에서 별도의 “leaseCollectionPrefix”를 정의해야 합니다.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>Azure Functions를 사용하여 변경 피드를 처리하고 10개 문서를 일괄 처리한다고 가정할 경우 7번째 문서에서 오류가 발생합니다. 마지막 3개 문서가 처리되지 않는 경우 그 다음 피드에서 실패한 문서(즉, 7번째 문서)부터 처리를 시작하려면 어떻게 해야 하나요?

오류를 처리하는 권장 패턴으로, try-catch 블록을 사용하여 코드를 래핑합니다. 그리고 문서 목록에 대해 반복하는 경우 각 반복을 고유의 try-catch 블록에 래핑합니다. 오류를 catch하고 해당 문서를 큐(배달하지 못한 편지)에 배치한 다음, 오류를 생성한 문서를 처리하는 논리를 정의합니다. 이 방법을 사용하여 200개 문서 일괄 처리 중 문서 하나만 실패한 경우 일괄 처리 전체를 버릴 필요는 없습니다.

오류 발생 시 검사점을 시작 위치로 되돌리면 안 됩니다. 그렇지 않으면 변경 피드에서 이러한 문서를 계속 가져올 수 있습니다. 변경 피드는 문서의 마지막 최종 스냅숏을 보관하므로 문서의 이전 스냅숏이 손실될 수 있습니다. 변경 피드는 문서의 마지막 버전 하나만 보관하며, 그 사이에 다른 프로세스가 문서를 변경할 수 있습니다.

코드를 계속 수정하다 보면 배달하지 못한 편지 큐가 더 이상 발견되지 않을 것입니다. 변경 피드 시스템이 Azure Functions를 자동으로 호출하고, 검사점은 Azure 함수에 의해 내부적으로 유지됩니다. 검사점을 롤백하고 검사점과 관련된 모든 사항을 제어하려면 변경 피드 프로세서 SDK를 사용해야 합니다.

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>Azure Cosmos DB 트리거를 사용하면 추가 비용이 발생하나요?

Azure Cosmos DB 트리거는 내부적으로 변경 피드 프로세서 라이브러리를 활용합니다. 따라서 상태 및 부분 검사점을 유지하려면 임대 컬렉션이라고 하는 추가 컬렉션이 필요합니다. Azure Functions를 중지했다가 나중에 이어서 처리할 수 있도록 동적으로 크기를 조정하고 작업을 계속 진행하려면 이 상태 관리가 필요합니다. 자세한 내용은 [변경 피드 프로세서 라이브러리를 사용하는 방법](change-feed-processor.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [변경 피드를 읽는 방법](read-change-feed.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
* [변경 피드 프로세서 라이브러리를 사용하는 방법](change-feed-processor.md)
* [Azure Cosmos DB 및 Azure Functions를 사용하는 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md)
