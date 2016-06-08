<properties 
	pageTitle="DocumentDB 규모 및 성능 테스트 | Microsoft Azure" 
	description="Azure DocumentDB를 사용하여 규모 및 성능 테스트를 수행하는 방법에 대해 알아봅니다."
	keywords="성능 테스트"
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2016" 
	ms.author="arramac"/>

# Azure DocumentDB를 사용한 성능 및 규모 테스트
성능 및 규모 테스트는 응용 프로그램 개발의 핵심 단계입니다. 대부분의 응용 프로그램에서 데이터베이스 계층은 전반적인 성능 및 확장성에 큰 영향을 미치므로 성능 테스트의 주요 구성 요소입니다. [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)는 탄력적인 규모 및 예측 가능한 성능을 위해 작성되었으므로 고성능 데이터베이스 계층을 필요로 하는 응용 프로그램에 가장 적합합니다.

이 문서는 개발자가 자신의 DocumentDB 작업에 대한 성능 테스트 모음을 구현하거나 DocumentDB에서 고성능 응용 프로그램 시나리오를 평가할 때 참조로 사용됩니다. 또한 이 문서는 데이터베이스의 격리된 성능 테스트에 중점을 두지만 프로덕션 응용 프로그램에 대한 모범 사례도 제공합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- Azure DocumentDB의 성능 테스트를 위한 샘플 .NET 클라이언트 응용 프로그램은 어디에서 찾을 수 있나요?
- Azure DocumentDB에 수행된 요청에 대한 종단 간 성능에 영향을 주는 핵심 요인은 무엇인가요? 
- 클라이언트 응용 프로그램에서 Azure DocumentDB를 사용하여 높은 처리량 수준을 달성하려면 어떻게 하나요?

코드를 시작하려면 [DocumentDB 성능 테스트 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)에서 프로젝트를 다운로드합니다.

## 핵심 클라이언트 구성 옵션
DocumentDB는 보장된 대기 시간 및 처리량으로 원활하게 규모가 조정되는 신속하고 유연한 분산 데이터베이스입니다. DocumentDB를 사용하여 데이터베이스 계층의 규모를 조정하기 위해 주요 아키텍처를 변경하거나 복잡한 코드를 작성할 필요는 없습니다. 규모를 확장 및 축소하는 것은 단일 API 호출 또는 SDK 메서드 호출을 수행하는 것만큼 쉽습니다. 그러나 규모별로 테스트할 때는 네트워크 호출을 통해 DocumentDB에 액세스하는 것이 중요합니다. 성능 테스트 DocumentDB에 독립 실행형 클라이언트 응용 프로그램을 쓰는 경우 네트워크 대기 시간이 성능 측정에 미치는 영향을 방지하도록 적합하게 구성해야 합니다.

DocumentDB에서 최상의 종단 간 성능을 얻으려면 다음 클라이언트 구성 옵션을 고려합니다.

- **스레드/태스크 수 늘리기**: DocumentDB에 대한 호출이 네트워크를 통해 수행되므로 클라이언트 응용 프로그램이 요청 간에 대기하는 시간이 짧도록 요청의 병렬 처리 수준을 다양하게 지정해야 할 수 있습니다. 예를 들어 .NET의 [태스크 병렬 라이브러리](https://msdn.microsoft.com//library/dd460717.aspx)를 사용하는 경우 DocumentDB에 대해 약 100개의 읽기 또는 쓰기 태스크를 만드세요.
- **동일한 Azure 지역 내에서 테스트**: 가능한 경우 동일한 Azure 지역에 배포된 가상 컴퓨터 또는 앱 서비스에서 테스트합니다. 개략적인 비교를 위해 동일한 지역 내의 DocumentDB 호출이 1-2ms 내에 완료되지만 미국 서부와 동부 해안 사이의 대기 시간이 50ms보다 큽니다.
- **호스트당 System.Net MaxConnections 늘리기**: DocumentDB 요청이 기본적으로 HTTPS/REST를 통해 수행되며 호스트 이름 또는 IP 주소당 기본 연결 제한이 적용됩니다. 클라이언트 라이브러리가 DocumentDB에 대한 여러 동시 연결을 사용할 수 있도록 더 높은 값(100-1000)으로 설정해야 할 수 있습니다. .NET에서는 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx)입니다.
- **서버 쪽 GC 켜기**: 경우에 따라 가비지 수집의 빈도를 줄이는 것이 도움이 될 수 있습니다. .NET에서는 [gcServer](https://msdn.microsoft.com/library/ms229357.aspx)를 true로 설정합니다.
- **TCP 프로토콜과의 직접 연결 사용**: 최상의 성능을 얻으려면 [TCP 프로토콜](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.protocol.aspx)과의 [직접 연결](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionmode.aspx)을 사용합니다. 
- **RetryAfter 간격으로 백오프 구현**: 성능 테스트 중에는 작은 비율의 요청이 제한될 때까지 로드를 늘려야 합니다. 제한될 경우 클라이언트 응용 프로그램은 서버에서 지정한 재시도 간격 제한을 백오프해야 합니다. 이렇게 하면 재시도 간 기간을 최소화할 수 있습니다. [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)를 참조하세요.
- **클라이언트 작업 규모 확장**: 높은 처리량 수준에서 테스트하는 경우(>50,000 RU/s) 컴퓨터의 CPU 또는 네트워크 사용률이 최대화되므로 클라이언트 응용 프로그램은 병목 상태가 될 수 있습니다. 이 시점에 도달하면 여러 서버에 걸쳐 클라이언트 응용 프로그램을 확장하여 DocumentDB 계정을 계속 추가할 수 있습니다.

## 시작
가장 빠른 시작 방법은 아래 단계에 설명된 대로 아래의 .NET 샘플을 컴파일하고 실행하는 것입니다. 소스 코드를 검토하고 자체 클라이언트 응용 프로그램에 대해 비슷한 구성을 구현할 수도 있습니다.

**1단계:** [DocumentDB 성능 테스트 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)에서 프로젝트를 다운로드하거나 Github 리포지토리를 분기합니다.

**2단계:** App.config에서 EndpointUrl, AuthorizationKey, CollectionThroughput 및 DocumentTemplate(옵션)에 대한 설정을 수정합니다.

> [AZURE.NOTE] 높은 처리량의 컬렉션을 프로비전하기 전에 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하여 컬렉션당 비용을 추정합니다. DocumentDB는 시간 단위로 저장소 및 처리량의 비용을 별도로 청구하므로 테스트 후에 DocumentDB 컬렉션의 처리량을 삭제하거나 낮추어 비용을 절감할 수 있습니다.

**3단계:** 명령줄에서 콘솔 앱을 컴파일하고 실행합니다. 다음과 유사한 출력이 표시됩니다.

	Summary:
	---------------------------------------------------------------------
	Endpoint: https://docdb-scale-demo.documents.azure.com:443/
	Collection : db.testdata at 50000 request units per second
	Document Template*: Player.json
	Degree of parallelism*: 500
	---------------------------------------------------------------------

	DocumentDBBenchmark starting...
	Creating database db
	Creating collection testdata
	Creating metric collection metrics
	Retrying after sleeping for 00:03:34.1720000
	Starting Inserts with 500 tasks
	Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
	Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
	Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
	Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
	Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
	Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
	Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
	Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
	Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
	Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
	Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
	Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
	Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
	Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
	Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
	Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
	Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
	Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
	Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
	Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
	Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
	Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

	Summary:
	---------------------------------------------------------------------
	Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
	---------------------------------------------------------------------
	DocumentDBBenchmark completed successfully.


**4단계(필요한 경우):** 도구에서 보고된 처리량(RU/s)은 컬렉션의 프로비전된 처리량보다 같거나 높아야 합니다. 그렇지 않은 경우 DegreeOfParallelism을 조금씩 늘리면 제한에 도달하는 데 도움이 될 수 있습니다. 클라이언트 앱의 처리량이 안정화될 경우 같거나 다른 컴퓨터에서 앱의 여러 인스턴스를 시작하면 여러 다른 인스턴스 간에 프로비전된 제한에 도달하는 데 도움이 됩니다. 이 단계에 대한 도움이 필요한 경우 [DocumentDB에 문의](askdocdb@microsoft.com)를 통해 연락하거나 지원 티켓을 제출하세요.

실행 중인 앱이 있는 경우 다양한 [인덱싱 정책](documentdb-indexing-policies.md) 및 [일관성 수준](documentdb-consistency-levels.md)을 시도하면서 처리량 및 대기 시간에 미치는 영향을 이해할 수 있습니다. 소스 코드를 검토하고 자체 테스트 제품군 또는 프로덕션 응용 프로그램에 대해 비슷한 구성을 구현할 수도 있습니다.

## 요약
이 문서에서는 .NET 콘솔 앱과 검토된 키 구성 옵션을 사용하여 DocumentDB로 성능 및 규모 테스트를 수행하여 Azure DocumentDB에서 최상의 성능을 얻는 방법을 살펴보았습니다. DocumentDB 사용에 대한 자세한 내용은 아래 링크를 참조하세요.

* [DocumentDB 성능 테스트 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [DocumentDB에서 서버 쪽 분할](documentdb-partition-data.md)`
* [DocumentDB 컬렉션 및 성능 수준](documentdb-performance-levels.md)
* [MSDN의 DocumentDB .NET SDK 설명서](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB .NET 샘플(영문)](https://github.com/Azure/azure-documentdb-net)
* [성능 팁에 대한 DocumentDB 블로그](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)

<!---HONumber=AcomDC_0525_2016-->