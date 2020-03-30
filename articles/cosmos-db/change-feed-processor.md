---
title: Azure 코스모스 DB에서 피드 프로세서 라이브러리 변경
description: Azure Cosmos DB 변경 피드 프로세서 라이브러리를 사용하여 변경 피드 프로세서의 구성 요소인 변경 피드를 읽는 방법에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273316"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB의 변경 피드 프로세서 

변경 피드 프로세서는 [Azure 코스모스 DB SDK V3의](https://github.com/Azure/azure-cosmos-dotnet-v3)일부입니다. 변경 피드를 읽는 프로세스를 간소화하고 이벤트 처리를 여러 소비자에게 효과적으로 배포합니다.

변경 피드 프로세서 라이브러리의 주요 이점은 변경 피드의 모든 이벤트를 "최소 한 번" 전달하는 내결함성 동작입니다.

## <a name="components-of-the-change-feed-processor"></a>변경 피드 프로세서의 구성 요소

변경 피드 프로세서를 구현하는 네 가지 주요 구성 요소는 다음과 같습니다. 

1. **모니터링된 컨테이너:** 모니터링된 컨테이너에는 변경 피드가 생성되는 데이터가 있습니다. 모니터링되는 컨테이너에 대한 모든 삽입 및 업데이트는 컨테이너의 변경 피드에 반영됩니다.

1. **임대 컨테이너:** 임대 컨테이너는 상태 저장소 역할을 하며 여러 작업자간에 변경 피드를 처리하도록 조정합니다. 임대 컨테이너는 모니터링되는 컨테이너와 동일한 계정 또는 별도의 계정에 저장할 수 있습니다. 

1. **호스트:** 호스트는 변경 피드 프로세서를 사용하여 변경 내용을 수신하는 응용 프로그램 인스턴스입니다. 동일한 임대 구성을 가진 여러 인스턴스가 병렬로 실행할 수 있지만 각 인스턴스에는 다른 **인스턴스 이름이**있어야 합니다. 

1. **대리자:** 대리자는 개발자가 변경 피드 프로세서가 읽는 변경 내용의 각 일괄 처리로 수행할 작업을 정의하는 코드입니다. 

변경 피드 프로세서의 이러한 4개의 요소가 상호 작용하는 방법을 이해하기 위해 다음 다이어그램의 예제를 살펴보겠습니다. 모니터링되는 컨테이너는 문서를 저장하고 'City'를 파티션 키로 사용합니다. 파티션 키 값이 항목을 포함하는 범위로 분산되어 있음을 알 수 있습니다. 두 개의 호스트 인스턴스가 있으며 변경 피드 프로세서는 계산 분포를 최대화하기 위해 각 인스턴스에 서로 다른 파티션 키 값을 할당합니다. 각 범위는 병렬로 판독되고 진행 상황은 임대 컨테이너의 다른 범위와 별도로 유지됩니다.

![변경 피드 프로세서 예제](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>변경 피드 프로세서 구현

진입점은 항상 호출하는 `Container` `GetChangeFeedProcessorBuilder`인스턴스에서 모니터링되는 컨테이너입니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

첫 번째 매개 변수는 이 프로세서의 목표를 설명하는 고유한 이름이고 두 번째 이름은 변경 내용을 처리하는 대리자 구현입니다. 

대리자의 예는 다음과 같은 것입니다.


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

마지막으로 이 프로세서 인스턴스의 이름을 `WithInstanceName` 정의하고 `WithLeaseContainer`을 사용하여 임대 상태를 유지할 컨테이너입니다.

호출하면 `Build` 를 호출하여 `StartAsync`시작할 수 있는 프로세서 인스턴스가 표시됩니다.

## <a name="processing-life-cycle"></a>처리 수명 주기

호스트 인스턴스의 일반적인 수명 주기는 다음과 입니다.

1. 변경 피드를 읽습니다.
1. 변경 사항이 없는 경우 미리 정의된 시간(Builder에서 `WithPollInterval` 사용자 지정 가능)을 절전 모드로 이동하여 #1.
1. 변경 사항이 있는 경우 **대리인에게**보냅니다.
1. 대리자가 변경 내용 처리를 **성공적으로**완료하면 임대 저장소를 최신 처리 된 시점으로 업데이트하고 #1 이동합니다.

## <a name="error-handling"></a>오류 처리

변경 피드 프로세서는 사용자 코드 오류에 탄력적입니다. 즉, 대리자 구현에 처리되지 않은 예외(단계 #4)가 있는 경우 특정 변경 일괄 처리가 중지되고 새 스레드가 만들어집니다. 새 스레드는 임대 저장소가 해당 파티션 키 값 범위에 대해 가지고 있는 최신 시점임을 확인하고 거기에서 다시 시작하여 동일한 변경 일괄 처리를 대리자에게 효과적으로 보냅니다. 대리자가 변경 내용을 올바르게 처리하고 변경 피드 프로세서에 "최소 한 번" 보장이 있는 이유는 대리자 코드가 throw되면 해당 일괄 처리를 다시 시도하기 때문에 이 동작은 계속됩니다.

## <a name="dynamic-scaling"></a>동적 크기 조정

소개 중에 언급했듯이 변경 피드 프로세서는 여러 인스턴스에 걸쳐 계산을 자동으로 배포할 수 있습니다. 변경 피드 프로세서를 사용하여 응용 프로그램의 여러 인스턴스를 배포하고 이를 활용할 수 있습니다.

1. 모든 인스턴스는 동일한 임대 컨테이너 구성을 가져야 합니다.
1. 모든 인스턴스에는 동일한 워크플로 이름이 있어야 합니다.
1. 각 인스턴스에는 다른 인스턴스 이름()이`WithInstanceName`있어야 합니다.

이러한 세 가지 조건이 적용되면 변경 피드 프로세서는 균등 분배 알고리즘을 사용하여 실행 중인 모든 인스턴스에 임대 컨테이너의 모든 임대를 배포하고 계산을 병렬화합니다. 하나의 임대는 지정된 시간에 하나의 인스턴스에서만 소유할 수 있으므로 최대 인스턴스 수는 임대 수와 같습니다.

인스턴스 수는 증가하고 축소될 수 있으며 변경 피드 프로세서는 그에 따라 재배포하여 부하를 동적으로 조정합니다.

또한 변경 피드 프로세서는 처리량 또는 스토리지 증가로 인해 컨테이너 규모로 동적으로 조정할 수 있습니다. 컨테이너가 증가하면 변경 피드 프로세서는 임대를 동적으로 늘리고 기존 인스턴스 간에 새 임대를 배포하여 이러한 시나리오를 투명하게 처리합니다.

## <a name="change-feed-and-provisioned-throughput"></a>변경 피드 및 프로비전된 처리량

Cosmos 컨테이너 내부 및 외부의 데이터 이동은 항상 RU를 사용하므로 사용된 RU 요금이 청구됩니다. 임대 컨테이너에서 사용되는 RU 요금이 청구됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub의 사용 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub의 추가 샘플](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드 프로세서에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [변경 피드 프로세서 라이브러리에서 마이그레이션하는 방법](how-to-migrate-from-change-feed-library.md)
* [변경 피드 평가기 사용](how-to-use-change-feed-estimator.md)
* [변경 피드 프로세서 시작 시간](how-to-configure-change-feed-start-time.md)
