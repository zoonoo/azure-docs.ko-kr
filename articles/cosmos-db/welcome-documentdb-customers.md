---
title: "DocumentDB 고객의 Azure Cosmos DB 시작 | Microsoft 문서"
description: "DocumentDB 고객이 Azure Cosmos DB 고객으로 전환되는 //build 2017 공지에 대해 자세히 알아봅니다."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: n/a
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f60c3502ed51424647088fa8109f13cabc4e73da
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017

---

# <a name="welcome-documentdb-customers-to-azure-cosmos-db"></a>DocumentDB 고객의 Azure Cosmos DB 시작

DocumentDB 고객님, 이제 Azure Cosmos DB 가족이 되신 것을 알려 드리게 되어 기쁘게 생각합니다. 

[Microsoft Build 2017 컨퍼런스에서 발표된](https://azure.microsoft.com/blog/azure-cosmos-db-microsofts-globally-distributed-multi-model-database-service/) Azure Cosmos DB는 글로벌하게 배포된 최초의 데이터베이스 서비스로, 업계에서 가장 포괄적인 SLA를 기반으로 낮은 대기 시간, 높은 가용성 및 일관성을 보장하고 다수의 데이터 모델 및 API를 지원함으로써 수많은 지리적 영역에서 처리량 및 저장소를 탄력적으로 확장할 수 있습니다. 

![Azure Cosmos DB는 탄력적 규모 확장, 낮은 대기 시간 보증, 일관성 모델 5개, 포괄적 보장 SLA를 갖춘 글로벌하게 배포된 데이터베이스 서비스입니다.](./media/welcome-documentdb-customers/azure-cosmos-db.png)

Azure Cosmos DB에서 지원하는 API 중 하나는 DocumentDB API 및 문서 데이터 모델입니다. DocumentDB API는 이미 익숙하게 사용할 수 있습니다. DocumentDB API는 현재 DocumentDB 응용 프로그램을 실행하는 데 사용하고 있는 API입니다. 이러한 API는 변경되지 _않습니다_. NuGet 패키지, 네임스페이스 및 모든 종속성이 동일하게 유지됩니다. DocumentDB API로 기본 제공된 앱을 계속해서 실행하기 위해 **어떠한 항목도 변경할 필요가 없습니다**. Azure Cosmos DB는 단순히 현재 그들이 속한 서비스의 이름입니다. 

가질 수 있는 몇 가지 질문을 살펴보겠습니다. 

## <a name="why-move-to-azure-cosmos-db"></a>Azure Cosmos DB로 전환해야 하는 이유는 무엇인가요? 

Azure Cosmos DB는 대규모로 글로벌하게 배포된 클라우드 데이터베이스에서 다음으로 큰 도약입니다. 이제 DocumentDB 고객은, Azure Cosmos DB에서 제공하는 새롭고 혁신적인 시스템 및 기능에 액세스할 수 있습니다.

Azure Cosmos DB는 2010년, Microsoft 내부에서 대규모 응용 프로그램에 직면한 개발자의 과제에 부응하기 위해 “Project Florence”로 시작되었습니다. 글로벌하게 배포된 앱을 구축하는 문제가 Microsoft만의 문제가 아니라는 것을 알게 된 2015년, Azure 개발자에게 Azure DocumentDB라는 형태로 이 기술의 1세대를 제공했습니다. 

그 이후로, 새로운 기능이 추가되었으며 중요한 새 기능이 도입되었습니다.  Azure Cosmos DB가 그 결과물입니다.  Azure Cosmos DB의 이 릴리스에서는 DocumentDB 고객(데이터 포함)이 자동으로 Azure Cosmos DB 고객이 됩니다. 이러한 전환은 원활히 진행되며 이제 Azure Cosmos DB에서 제공하는 새롭고 혁신적인 시스템 및 기능에 액세스할 수 있습니다. 이러한 기능은 핵심 데이터베이스 엔진 및 글로벌 배포, 탄력적 확장성, 업계 최고의 포괄적인 SLA 분야에 관한 것입니다. 특히, 널리 사용되는 모든 데이터 모델, 형식 시스템 및 API를 Azure Cosmos DB의 기본 데이터 모델에 효과적으로 매핑할 수 있도록 Azure Cosmos DB 데이터베이스 엔진을 발전시켜왔습니다. 

이러한 노력으로 현재 개발자에게 제공되는 것이 [Gremlin](graph-introduction.md) 및 [Table Storage API](table-introduction.md)에 대한 새로운 지원입니다. 그리고 이것은 시작일 뿐입니다… 시간이 지날수록 다른 인기있는 API 및 최신 데이터 모델을 추가하여 글로벌 규모로 성능 및 저장소를 더욱 발전시킬 예정입니다. 

DocumentDB의 [SQL 언어](../documentdb/documentdb-sql-query.md)는 기본 Cosmos DB가 지원할 수 있는 많은 API 중 하나일 뿐이라는 점에 주목해야 합니다. Azure Cosmos DB와 같은 완전히 관리되는 서비스를 사용하는 개발자로서 서비스에 대한 유일한 인터페이스는 서비스에 노출된 API입니다. 이를 위해, 기존 DocumentDB 고객에 대해 실제로 변경되는 사항은 아무 것도 없습니다. Azure Cosmos DB는 DocumentDB와 정확히 동일한 SQL API를 제공합니다. 그러나 이제(및 앞으로)는, 이전에는 액세스할 수 없었던 다른 기능에 액세스할 수 있습니다. 

지속적인 노력의 또 다른 결과는 처리량 및 저장소의 전역적이고 탄력적인 확장성을 위한 기반 확장입니다. 이를 위해 처음으로 등장한 것이 [RU/m](request-units-per-minute.md)이지만 이 분야에서 더 많은 기능을 발표할 예정입니다. 이러한 새로운 기능을 통해 다양한 워크로드를 지닌 고객이 비용을 절감할 수 있습니다. 글로벌 배포 하위 시스템에 몇 가지 기본적인 향상된 기능을 제공하였습니다. 이러한 노력으로 많은 개발자에게 제공되는 것 중 하나는 일관된 접두사 일관성 모델입니다(총 5가지의 잘 정의된 일관성 모델 제공). 그러나 기술이 발전되어가면서 더 많은 흥미로운 기능을 출시할 예정입니다. 

## <a name="what-do-i-need-to-do-to-ensure-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>내 DocumentDB 리소스가 Azure Cosmos DB에서 계속 실행되도록 하려면 어떻게 해야 하나요?

아무 일도 일어나지 않습니다. 변경할 사항이 없습니다. DocumentDB 리소스는 이제 Azure Cosmos DB 리소스이며 전환되더라도 서비스 중단이 없습니다.

## <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>내 앱을 Azure Cosmos DB와 함께 작동하도록 하려면 무엇을 변경해야 하나요?

변경할 사항이 없습니다. 클래스, 네임스페이스 및 NuGet 패키지 이름은 변경되지 않습니다. 항상 그렇듯이, 최신 기능과 개선 사항을 활용하려면 SDK를 최신 상태로 유지하는 것이 좋습니다. 

## <a name="whats-changed-in-the-azure-portal"></a>Azure Portal에서 변경된 내용은 무엇인가요?

Azure DocumentDB는 포털에 Azure 서비스로 더 이상 나타나지 않습니다. 대신, 다음 이미지에 나와 있는 것처럼 Azure Cosmos DB가 새로운 아이콘으로 표시됩니다. 모든 컬렉션은 이전과 마찬가지로 사용할 수 있으며 처리량을 조정하고 일관성을 변경하며 SLA를 모니터링할 수 있습니다. **데이터 탐색기(미리 보기)** 기능이 향상되었습니다. 이제 다음 이미지와 같이 한 페이지에서 문서를 보고 편집하고, 쿼리를 작성하고 실행하며 저장 프로시저, 트리거 및 UDF로 작업할 수 있습니다. 

![Azure Portal에서 선택키 보기 및 복사, 키 블레이드](./media/welcome-documentdb-customers/cosmos-db-data-explorer.png)

## <a name="are-there-changes-to-pricing"></a>가격 책정에 변경 사항이 있나요?

아니요, Azure Cosmos DB에서 앱 실행 비용은 이전과 동일합니다. 하지만 발표된 새로운 **분당 요청 단위 기능**을 활용할 수 있습니다. 자세한 내용은 [분당 처리량 조정](request-units-per-minute.md) 문서를 참조하세요.

## <a name="are-there-changes-to-the-service-level-agreements-slas"></a>Service Level Agreement(서비스 수준 약정)에 변경 사항이 있나요?

아니요, 가용성, 일관성, 대기 시간 및 처리량에 대한 SLA는 변경되지 않으며 포털에 계속 표시됩니다. SLA에 대한 자세한 내용은 [Azure Cosmos DB의 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)를 참조하세요.
   
![샘플 데이터를 사용한 Todo 앱](./media/welcome-documentdb-customers/azure-cosmosdb-portal-metrics-slas.png)

## <a name="whats-next-with-azure-cosmos-db"></a>Azure Cosmos DB의 다음 기술은 무엇인가요?

Azure Cosmos DB는 지속적으로 진화하고 있는 데이터베이스 서비스입니다. 모든 새로운 기능은 Microsoft 내부의 대규모 응용 프로그램에서 검증된 후 주요 외부 고객에게 노출되고 최종적으로 전 세계에 릴리스됩니다. 

새로운 Gremlin 및 Table API 지원은 Azure Cosmos DB에서 지원할 대중적인 API 및 데이터 모델의 시작일 뿐이며 Microsoft는 발표되는 새로운 데이터 모델을 공유하기를 기대합니다. 또한 글로벌 규모로 성능 및 저장소도 지속적으로 발전시키고 있습니다. 

항상 Azure DocumentDB 고객이 되어 주신 것에 감사드리며 Azure Cosmos DB를 전 세계의 개발자 및 고객 여러분 모두와 공유하게 된 것을 기쁘게 생각합니다. 전 세계에서 가장 신뢰할 수 있는 데이터베이스 서비스가 되어 고객이 놀라울 정도로 강력한, 대규모 앱을 더욱 쉽게 빌드할 수 있도록 하는 것을 목표로 하고 있습니다. Azure Cosmos DB의 새로운 기능을 사용해 보고 의견을 제시해 주세요.  Cosmos DB로 어떤 결과물을 얻었는지 소식을 기다리고 있습니다.

— Azure Cosmos DB 관련 친구들 [@AzureCosmosDB](https://twitter.com/AzureCosmosDB)

