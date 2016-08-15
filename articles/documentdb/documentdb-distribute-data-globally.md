<properties
   pageTitle="DocumentDB로 데이터 글로벌 배포 | Microsoft Azure"
   description="완전 관리형 NoSQL 데이터베이스 서비스, Azure DocumentDB에서 글로벌 데이터베이스를 사용한 전 세계적 지역에서 복제, 장애 조치, 데이터 복구에 대해 알아봅니다."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="kipandya"/>
   
   
# DocumentDB로 데이터를 글로벌 배포

> [AZURE.NOTE] DocumentDB 데이터베이스의 전역 배포는 일반적으로 사용 가능하며, 새로 만든 DocumentDB 계정에 대해 자동으로 사용되도록 설정됩니다. 현재, 모든 기존 계정에 대해 전역 배포를 사용하도록 설정하기 위해 작업 중이지만 계정에 대해 전역 배포를 일시적으로 사용하도록 설정하려면 [지원 서비스에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. Microsoft에서 처리해 드리겠습니다.

Azure DocumentDB는 전 세계의 사용자에게 매우 반응성이 높은 경험을 제공하는 인터넷 규모 응용 프로그램과 전 세계에 배포된 수많은 기기로 구성된 사물 인터넷(IoT) 응용 프로그램의 필요에 맞추어 설계되었습니다. 이러한 데이터베이스 시스템은 잘 정의된 데이터 일관성과 가용성 보증으로 여러 개의 지역에서 응용 프로그램 데이터에 짧은 대기 시간으로 액세스해야 하는 과제를 해결해야 합니다. 글로벌 배포된 데이터베이스 시스템인 DocumentDB는 일관성, 가용성, 성능을 알맞은 보증으로 명확히 절충하는, 완전 관리형 다중 지역 데이터베이스 계정을 제공함으로써 데이터의 글로벌 배포를 단순화합니다. DocumentDB 데이터베이스 계정은 가용성이 높고, 대기 시간(ms)이 한 자리이며, 여러 개의 [잘 정의된 일관성 수준][consistency]과 멀티호밍(multi-homing) API를 사용한 투명한 지역 장애 조치(failover), 전 세계적으로 처리량과 저장소를 탄력적으로 확대하는 기능을 제공합니다.

  
## 다중 지역 계정 구성

Azure 포털을 사용하여 DocumentDB 계정을 전 세계적인 규모로 구성하는 작업을 1분 이내에 완료할 수 있습니다. 여러 개의 지원되는 잘 정의된 일관성 수준에서 적절한 일관성 수준을 선택하고, 임의의 Azure 지역과 데이터베이스 계정을 연결하기만 하면 됩니다. DocumentDB 일관성 수준은 특정 일관성 보증과 성능 간을 명확히 절충합니다.

![DocumentDB는 선택 가능한 여러 개의 잘 정의된(관대한) 일관성 모델을 제공합니다.][1]

DocumentDB는 선택 가능한 여러 개의 잘 정의된(관대한) 일관성 모델을 제공합니다.

올바른 일관성 수준을 선택하는 것은 응용 프로그램에 필요한 데이터 일관성 보증에 따라 달라집니다. DocumentDB는 모든 지정된 지역에서 데이터를 자동 복제하고 데이터베이스 계정에 대해 선택한 일관성을 보증합니다.


## 다중 지역 장애 조치 사용 

Azure DocumentDB는 여러 Azure 지역에서 데이터베이스 계정에 투명한 장애 조치가 가능합니다. 새로운 [멀티 호밍 API][developingwithmultipleregions]는 여러분의 앱이 논리적인 끝점을 계속 사용하고 장애 조치로 인해 중단되지 않도록 보증합니다. 장애 조치는 개발자가 제어하므로 응용 프로그램, 인프라, 서비스 또는 지역적 장애(실제 또는 시뮬레이션)를 비롯한 어떤 가능한 장애 상황에서도 데이터베이스 계정을 이동할 수 있는 유연성을 제공합니다. DocumentDB 지역 장애가 발생할 경우, 서비스는 데이터베이스 계정을 투명하게 장애 조치하고 응용 프로그램은 가용성을 잃지 않고 계속 데이터에 액세스합니다. DocumentDB는 [99\.99% 가용성 SLA][sla]를 제공하지만, [프로그램][arm]이나 Azure 포털을 통해 지역 장애를 시뮬레이션하여 응용 프로그램의 종단 간 가용성 속성을 테스트할 수 있습니다.


## 전 세계적 규모로 확대
DocumentDB를 통해 독립적으로 처리량을 프로비저닝하고 데이터베이스 계정과 연결된 전 세계 모든 지역에서 어떤 규모로든 각 DocumentDB 컬렉션에 대한 저장소를 사용할 수 있습니다. DocumentDB 컬렉션은 자동으로 글로벌 배포되고 데이터베이스 계정과 연결된 모든 지역에서 관리됩니다. 데이터베이스 계정 내의 컬렉션은 [DocumentDB 서비스를 사용할 수 있는][serviceregions] 모든 Azure 지역에 배포할 수 있습니다.

각 DocumentDB 컬렉션에 대해 구매한 처리량과 사용된 저장소는 모든 지역에 동등하게 자동 프로비저닝됩니다. 이에 따라 응용 프로그램이 전 세계적 규모로 매끄럽게 확대되고 [매 시간 사용하는 처리량과 저장소에 대해서만 가격을 지불합니다][pricing]. 예를 들어, DocumentDB 컬렉션에 대해 200만 RU를 프로비저닝했다면 데이터베이스 계정과 연결된 각 지역은 해당 컬렉션에 대해 200만 RU를 얻습니다. 이 내용은 아래에 나와 있습니다.

![4개 지역의 DocumentDB 컬렉션 처리량 확장][2]

DocumentDB는 P99에서 읽기 대기 시간이 10ms 미만이고 쓰기 대기 시간은 15ms 미만입니다. 읽기 요청은 [선택한 일관성 요구 사항][consistency]을 보증하기 위해 데이터 센터 경계를 넘어가지 않습니다. 쓰기는 언제나 클라이언트가 승인하기 전에 로컬에서 쿼럼을 커밋합니다. 각 데이터베이스 계정은 쓰기 지역 우선순위에 따라 구성됩니다. 가장 우선순위가 높은 지역은 해당 계정의 현재 쓰기 지역 역할을 합니다. 모든 SDK는 데이터베이스 계정 쓰기를 현재 쓰기 지역으로 투명하게 라우팅합니다.

마지막으로, DocumentDB는 완전히 [스키마와 관계없습니다][vldb]. 여러 데이터 센터에서 스키마나 보조 인덱스를 관리/업데이트하는 것을 걱정할 필요가 없습니다. [SQL 쿼리][sqlqueries]는 응용 프로그램과 데이터 모델이 발전하는 동안에도 지속적으로 동작합니다.


## 글로벌 배포 활성화 

하나 또는 그 이상의 Azure 지역을 DocumentDB 데이터베이스 계정과 연결하여 데이터의 로컬 배포 또는 글로벌 배포를 선택할 수 있습니다. 언제든지 데이터베이스 계정에 지역을 추가하거나 제거할 수 있습니다.

## 다음 단계

다음 문서에서 DocumentDB로 데이터를 글로벌 배포하는 방법에 대해 자세히 알아봅니다.

* [컬렉션 처리량 및 저장소 프로비전][throughputandstorage]
* [REST. .NET, Java, Python 및 Node SDK를 통한 멀티 호밍 API][developingwithmultipleregions]
* [DocumentDB의 일관성 수준][consistency]
* [가용성 SLA][sla]
* [데이터베이스 계정 관리][manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: https://azure.microsoft.com/documentation/articles/documentdb-partition-data/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: https://azure.microsoft.com/documentation/articles/documentdb-developing-with-multiple-regions/
[createaccount]: https://azure.microsoft.com/documentation/articles/documentdb-create-account/
[manageaccount]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/
[manageaccount-consistency]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#consistency
[manageaccount-addregion]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#addregion
[throughputandstorage]: https://azure.microsoft.com/documentation/articles/documentdb-manage/
[arm]: https://azure.microsoft.com/ko-KR/documentation/articles/documentdb-automation-resource-manager-cli/
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/ko-KR/regions/#services
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: https://azure.microsoft.com/documentation/articles/documentdb-sql-query/

<!---HONumber=AcomDC_0803_2016-->