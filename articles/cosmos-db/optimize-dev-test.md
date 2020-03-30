---
title: Azure Cosmos DB에서 개발 및 테스트 최적화
description: 이 문서에서는 Azure Cosmos DB에서 서비스의 개발 및 테스트를 위해 여러 가지 옵션을 무료로 제공하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246683"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 개발 및 테스트 비용 최적화

이 문서에서는 개발 또는 테스트 계정의 비용을 최적화하기 위한 기술과 함께 무료로 개발 및 테스트에 Azure Cosmos DB를 사용하는 다양한 옵션에 대해 설명합니다.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 에뮬레이터(로컬로 다운로드 가능한 버전)

[Azure Cosmos DB 에뮬레이터](local-emulator.md)는 Azure Cosmos DB 클라우드 서비스를 모방하는, 로컬로 다운로드 가능한 버전입니다. 네트워크 연결이 없어도 비용 발생 없이 Azure Cosmos DB API를 사용하는 코드를 작성하고 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터는 클라우드 서비스와 유사한, 개발 목적의 로컬 환경을 제공합니다. Azure 구독을 만들지 않고 로컬에서 애플리케이션을 개발하고 테스트할 수 있습니다. 애플리케이션을 클라우드에 배포할 준비가 되면 클라우드의 Azure Cosmos DB 엔드포인트에 연결하도록 연결 문자열을 업데이트하면 됩니다. 다른 수정은 필요하지 않습니다. 테스트 실행을 위해 Azure DevOps에서 [Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하여 CI/CD 파이프라인을 설정](tutorial-setup-ci-cd.md)할 수도 있습니다. [Azure Cosmos DB 에뮬레이터](local-emulator.md) 문서를 방문하여 시작할 수 있습니다.

## <a name="azure-cosmos-db-free-tier"></a>Azure 코스모스 DB 프리 티어 
Azure Cosmos DB 프리 티어를 사용하면 응용 프로그램을 쉽게 시작, 개발 및 테스트하거나 소규모 프로덕션 워크로드를 무료로 실행할 수 있습니다. 계정에서 프리 티어를 활성화하면 계정에서 처음 400개의 RU/s 및 5GB의 저장소를 무료로 받게 됩니다. 또한 데이터베이스 수준에서 400RU/s를 공유하는 25개의 컨테이너가 있는 공유 처리량 데이터베이스를 만들 수 있으며, 모두 프리 티어(프리 티어 계정의 공유 처리량 데이터베이스 5개 제한)가 포함됩니다. 프리 티어는 계정의 수명 동안 무기한 지속되며 무제한 저장 및 처리량(RU/s), SLA, 고가용성, 모든 Azure 리전에서턴키 전역 배포 등을 포함하여 일반 Azure Cosmos DB 계정의 모든 [이점과 기능이](introduction.md#key-benefits) 함께 제공됩니다. Azure 구독당 최대 하나의 프리 티어 계정을 가질 수 있으며 계정을 만들 때 옵트인해야 합니다. 시작하려면 [프리 티어를 사용하도록 설정한 새 계정을 만듭니다.](create-cosmosdb-resources-portal.md) 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요. 

## <a name="try-azure-cosmos-db-for-free"></a>무료로 Azure Cosmos DB 사용해 보기

[Azure Cosmos DB를 무료로 사용해 보십시오.](https://azure.microsoft.com/try/cosmosdb/) Try Azure Cosmos DB 계정은 현재 30일 동안 제한된 시간 동안 사용할 수 있습니다. 언제든지 계정을 갱신할 수 있습니다. Azure Cosmos DB 계정을 사용하면 Azure Cosmos DB를 쉽게 평가하고, 응용 프로그램을 빌드 및 테스트하거나, 빠른 시작 또는 자습서를 사용할 수 있습니다. 데모를 만들거나 단위 테스트를 수행하거나 다중 지역 계정을 만들고 비용을 발생하지 않고 앱을 실행할 수도 있습니다. Try Azure Cosmos DB 계정에서는 최대 25개의 컨테이너와 20,000개의 RU/s의 처리량이 있는 공유 처리량 데이터베이스 하나 또는 최대 5000RU/s의 컨테이너를 가질 수 있습니다. 시작하려면 [Azure Cosmos DB 체험해 보기](https://azure.microsoft.com/try/cosmosdb/)를 참조하세요.

## <a name="azure-free-account"></a>Azure 무료 체험 계정

Azure Cosmos DB는 Azure 크레딧과 리소스를 일정 기간 동안 무료로 제공하는 [Azure 체험 계정](https://azure.microsoft.com/free)에 포함됩니다. Azure Cosmos DB와 관련해서 이 체험 계정은 1년 동안 5GB의 스토리지와 400RU의 프로비전된 처리량을 제공합니다. 이 환경을 통해 개발자는 비용 없이 Azure Cosmos DB의 기능을 쉽게 테스트하거나 다른 Azure 서비스와 통합할 수 있습니다. Azure 체험 계정과 함께 처음 30일 동안 사용할 수 있는 $200 크레딧을 받게 됩니다. 서비스 사용이 시작된 후에도 업그레이드를 선택하기 전에는 요금이 청구되지 않습니다. 시작하려면 [Azure 체험 계정](https://azure.microsoft.com/free) 페이지를 방문하세요.

## <a name="use-shared-throughput-databases"></a>공유 처리량 데이터베이스 사용

공유 [처리량 데이터베이스에서](set-throughput.md#set-throughput-on-a-database)데이터베이스 내의 모든 컨테이너는 데이터베이스의 프로비저닝된 처리량(RU/s)을 공유합니다. 예를 들어 400개의 RU/s로 데이터베이스를 프로비전하고 4개의 컨테이너가 있는 경우 4개의 컨테이너가 모두 400RU/s를 공유합니다. 개발 또는 테스트 환경에서는 각 컨테이너에 덜 자주 액세스할 수 있으므로 최소 400RU/s보다 낮아야 하므로 공유 처리량 데이터베이스에 컨테이너를 배치하면 비용을 최적화하는 데 도움이 될 수 있습니다. 

예를 들어 개발 또는 테스트 계정에 4개의 컨테이너가 있다고 가정합니다. 전용 처리량(최소 400RU/s)으로 4개의 컨테이너를 만드는 경우 총 RU/s는 1600RU/s가 됩니다. 반대로 공유 처리량 데이터베이스(최소 400RU/s)를 만들고 컨테이너를 배치하면 총 RU/s는 400RU/s에 불과합니다. 일반적으로 공유 처리량 데이터베이스는 개별 컨테이너에 대한 보장된 처리량이 필요하지 않은 시나리오에 적합합니다.  공유 처리량 데이터베이스에 대해 자세히 [알아보세요.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 에뮬레이터 또는 Azure Cosmos DB 체험 계정 사용을 시작할 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](optimize-cost-queries.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보

