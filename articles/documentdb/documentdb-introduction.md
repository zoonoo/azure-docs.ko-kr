<properties 
	pageTitle="JSON 데이터베이스인 DocumentDB 소개 | Microsoft Azure" 
	description="NoSQL JSON 데이터베이스인 DocumentDB에 대해 알아봅니다. 이 문서 데이터베이스는 빅 데이터, 탄력적 확장성 및 고가용성을 위해 빌드됩니다." 
	keywords="json 데이터베이스, 문서 데이터베이스"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="07/01/2016" 
	ms.author="mimig"/>

# DocumentDB 소개: NoSQL JSON 데이터베이스

Azure DocumentDB는 신속하고 예측 가능한 성능, 고가용성, 자동 크기 조정, 글로벌 배포 및 개발의 용이성을 위해 구축된 완전히 관리되는 NoSQL 데이터베이스 서비스입니다. 유연한 데이터 모델, 일관된 짧은 대기 시간 및 풍부한 쿼리 기능은 원활한 규모를 필요로 하는 웹, 모바일, 게임, 및 IoT와 다른 여러 응용 프로그램에 매우 적합합니다.

이 JSON 데이터베이스에 대해 알아보고 작동 방식을 확인하는 빠른 방법은 다음 세 단계를 수행하는 것입니다.

1. DocumentDB의 사용 이점을 소개하는 [DocumentDB 정의](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) 동영상(2분)을 시청합니다.
2. Azure 포털을 사용하여 DocumentDB를 시작하는 방법을 강조하는 [Azure에서 DocumentDB 만들기](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) 동영상(3분)을 시청합니다.
3. 다양한 활동을 통해 DocumentDB에서 사용할 수 있는 풍부한 쿼리 기능에 대해 알아볼 수 있는 [쿼리 실습](http://www.documentdb.com/sql/demo)을 방문합니다. 샌드박스 탭으로 이동하여 고유한 사용자 지정 SQL 쿼리를 실행하고 DocumentDB를 시험해 보세요.

그런 다음 이 문서로 돌아와서 다음 내용을 자세히 살펴보세요.

-	[DocumentDB의 정의 및 최신 응용 프로그램에 제공하는 가치](#what-is-azure-documentdb)
-	[DocumentDB에서 사용자 데이터를 관리하는 방법과 데이터에 액세스하는 방법](#data-management)
-	[DocumentDB를 사용하여 응용 프로그램을 개발하는 방법](#develop)
-	[DocumentDB 응용 프로그램을 빌드하기 위한 다음 단계](#next-steps)

## Azure DocumentDB 정의  

최신 응용 프로그램은 대량 데이터를 생성 및 사용하고 신속하게 응답합니다. 이러한 응용 프로그램은 매우 빠르게 발전하므로 기본 데이터 스키마도 마찬가지입니다. 이에 대한 응답으로, 응용 프로그램 데이터 모델 및 구조화되지 않은 데이터 피드를 신속하게 반복하는 기능을 유지하는 동시에 데이터를 저장 및 처리하는 단순하고 빠르며 확장성 있는 솔루션으로 스키마 없는 NoSQL 데이터베이스를 선택하는 개발자가 점점 증가하고 있습니다. 그러나 스키마 없는 데이터베이스는 대부분 복잡한 쿼리 및 트랜잭션 처리를 허용하지 않으므로 고급 데이터 관리가 어렵습니다. DocumentDB 제공되는 위치입니다. Microsoft는 오늘날의 응용 프로그램에 대한 데이터를 관리할 때 이러한 요구 사항을 충족하기 위해 DocumentDB를 개발하였습니다.

DocumentDB는 최신 모바일, 웹, 게임 및 IoT 응용 프로그램용으로 디자인된 진정한 스키마 없는 NoSQL 데이터베이스 서비스입니다. DocumentDB에서는 99%의 읽기가 10밀리초 이하로 제공되고 99%의 쓰기가 15밀리초 이하로 제공되는지 확인합니다. 또한 스키마 유연성 및 요청 시 데이터베이스를 쉽게 확장하고 축소하는 기능을 제공합니다. 인덱싱하는 JSON 문서에 대해 스키마를 가정하거나 요구하지 않습니다. 기본적으로 데이터베이스에 있는 모든 문서를 자동으로 인덱스하며 스키마 또는 보조 인덱스 생성을 예상하거나 요구하지 않습니다. DocumentDB는 SQL 언어를 사용한 복잡한 임시 쿼리를 사용할 수 있도록 하고 잘 정의된 일관성 수준을 지원하며 저장 프로시저, 트리거 및 UDF의 익숙한 프로그래밍 모델을 사용한 JavaScript 언어 통합 다중 문서 트랜잭션 처리를 제공합니다.

JSON 데이터베이스인 DocumentDB는 응용 프로그램 스키마를 쉽게 반복할 수 있도록 하는 JSON 문서를 지원하고 키-값, 문서 또는 테이블 형식 데이터 모델을 필요로 하는 응용 프로그램을 지원합니다. DocumentDB는 JSON 및 JavaScript의 편재성을 수용하여 응용 프로그램이 정의한 개체와 데이터베이스 스키마 간의 불일치를 제거합니다. 또한 긴밀한 JavaScript 통합을 통해 개발자가 데이터베이스 엔진의 데이터베이스 트랜잭션 내에서 직접 응용 프로그램 논리를 효율적으로 실행할 수 있습니다.

Azure DocumentDB는 다음과 같은 주요 기능 및 이점을 제공합니다.

-	**탄력적으로 확장 가능한 처리량 및 저장소:** 응용 프로그램 요구를 충족하기 위해 쉽게 DocumentDB JSON 데이터베이스를 확장 또는 축소합니다. 데이터는 예측 가능한 낮은 대기 시간을 위해 SSD(반도체 드라이브)에 저장됩니다. DocumentDB는 거의 무제한인 저장소 크기 및 프로비전된 처리량까지 확장할 수 있는 컬렉션이라고 하는 JSON 데이터를 저장하기 위한 컨테이너를 지원합니다. 응용 프로그램 증가에 따라 탄력적으로 DocumentDB를 확장하고 원활하게 예측 가능한 성능을 얻을 수 있습니다.

-	**다중 지역 복제:** DocumentDB에서는 DocumentDB 계정에 모든 지역에 투명하게 데이터를 복제하여 해당 보증을 포함한 일관성, 가용성과 성능 사이의 균형을 제공하는 한편 데이터에 대한 전역 액세스를 필요로 하는 응용 프로그램을 개발할 수 있도록 합니다. DocumentDB에서는 멀티 호밍 API를 사용하는 투명한 지역 장애 조치 및 전 세계에 처리량 및 저장소의 크기를 탄력적으로 조정하는 기능을 제공합니다. [DocumentDB로 데이터를 글로벌 배포](documentdb-distribute-data-globally.md)에서 자세히 알아봅니다.

-	**익숙한 SQL 구문을 사용한 임시 쿼리:** DocumentDB 내에 다른 형식의 JSON 문서를 저장하고 익숙한 SQL 구문을 통해 해당 문서를 쿼리합니다. DocumentDB는 동시성이 높고 잠금이 없으며 로그 구조화된 인덱싱 기술을 활용하여 모든 문서 콘텐츠를 자동으로 인덱싱합니다. 따라서 스키마 힌트, 보조 인덱스 또는 뷰를 지정할 필요 없이 풍부한 실시간 쿼리를 사용할 수 있습니다. [DocumentDB 쿼리](documentdb-sql-query.md)에서 자세히 알아보세요.

-	**데이터베이스 내의 JavaScript 실행:** 표준 JavaScript를 사용하여 응용 프로그램 논리를 저장 프로시저, 트리거 및 UDF(사용자 정의 함수)로 표현합니다. 이렇게 하면 응용 프로그램과 데이터베이스 스키마 간의 불일치에 관계 없이 응용 프로그램 논리가 데이터에서 작동할 수 있습니다. DocumentDB는 데이터베이스 엔진 내에서 직접 JavaScript 응용 프로그램 논리의 전체 트랜잭션 실행을 제공합니다. JavaScript가 전체 통합되므로 JavaScript 프로그램 내에서 격리된 트랜잭션으로 INSERT, REPLACE, DELETE 및 SELECT 작업을 실행할 수 있습니다. [DocumentDB 서버 쪽 프로그래밍](documentdb-programming.md)에서 자세히 알아보세요.

-	**튜닝 가능한 일관성 수준:** 잘 정의된 네 가지 일관성 수준에서 선택하여 일관성과 성능 간의 최적 절충을 실현합니다. 쿼리 및 읽기 작업에 대해 DocumentDB는 강력, 제한된 부실, 세션, 최종 등의 네 가지 일관성 수준을 제공합니다. 이러한 잘 정의된 세부적인 일관성 수준을 통해 일관성, 가용성 및 대기 시간 간에 타당한 절충을 수행할 수 있습니다. [일관성 수준을 사용하여 DocumentDB에서 가용성 및 성능 최대화](documentdb-consistency-levels.md)에서 자세히 알아보세요.

-	**완전히 관리:** 데이터베이스 및 컴퓨터 리소스를 관리할 필요가 없습니다. 완전히 관리되는 Microsoft Azure 서비스의 경우 가상 컴퓨터를 관리하거나 소프트웨어를 배포 및 구성하거나 크기 조정을 관리하거나 복잡한 데이터 계층 업그레이드를 처리할 필요가 없습니다. 모든 데이터베이스가 자동으로 백업되고 지역적 실패로부터 보호됩니다. 필요 시 쉽게 DocumentDB 계정을 추가하고 용량을 프로비전할 수 있으므로 데이터베이스 작동 및 관리 대신 응용 프로그램에 집중할 수 있습니다.

-	**의도적인 개방성:** 기존 기술과 도구를 사용하여 신속하게 시작합니다. DocumentDB에 대한 프로그래밍은 단순하고 사용하기 쉬우며 새 도구를 채택하거나 JSON 또는 JavaScript에 대한 사용자 지정 확장을 준수할 필요가 없습니다. 단순한 RESTful HTTP 인터페이스를 통해 CRUD, 쿼리 및 JavaScript 처리를 비롯한 모든 데이터베이스 기능에 액세스할 수 있습니다. DocumentDB는 기존 형식, 언어 및 표준을 수용하며 이를 바탕으로 중요한 데이터베이스 기능을 제공합니다.

DocumentDB를 사용하여 쿼리 검색 및 트랜잭션 처리가 필요한 유연한 데이터 집합을 저장할 수 있습니다. 응용 프로그램 시나리오에는 대화형 웹, 모바일 및 게임 응용 프로그램에 대한 사용자 데이터와 JSON 데이터를 생성하는 IoT 장치의 저장, 검색 및 처리가 포함될 수 있습니다. 데이터베이스는 크기에 제한 없이 JSON 문서를 저장할 수 있으므로 DocumentDB는 인터넷 규모로 실행되는 응용 프로그램에 적합합니다.

##<a name="data-management"></a>Azure DocumentDB 리소스
Azure DocumentDB는 잘 정의된 데이터베이스 리소스를 통해 데이터를 관리합니다. 이러한 리소스는 고가용성을 위해 복제되며 논리적 URI로 고유한 주소 지정이 가능합니다. DocumentDB는 모든 리소스에 대해 단순한 HTTP 기반 RESTful 프로그래밍 모델을 제공합니다.

DocumentDB 데이터베이스 계정은 Azure DocumentDB에 대한 액세스 권한을 제공하는 고유 네임스페이스입니다. 데이터베이스 계정을 만들려면 먼저 다양한 Azure 서비스에 액세스할 수 있는 Azure 구독이 있어야 합니다.

DocumentDB 내의 모든 리소스는 JSON 문서로 모델링되고 저장됩니다. 리소스는 메타데이터가 포함된 JSON 문서인 항목과 항목 컬렉션인 피드로 관리됩니다. 항목 집합은 해당 피드 내에 포함됩니다.

아래 그림은 DocumentDB 리소스 간의 관계를 보여 줍니다.

![NoSQL JSON 데이터베이스인 DocumentDB의 리소스 간 계층 관계][1]

데이터베이스 계정은 각각 여러 컬렉션을 포함하는 데이터베이스 집합으로 구성되고, 각 컬렉션에는 저장 프로시저, 트리거, UDF, 문서 및 관련 첨부 파일이 포함될 수 있습니다. 데이터베이스에는 각각 다양한 다른 컬렉션, 저장 프로시저, 트리거, UDF, 문서 또는 첨부 파일에 액세스할 권한 집합을 가진 관련 사용자도 있습니다. 데이터베이스, 사용자, 사용 권한 및 컬렉션은 잘 알려진 스키마가 있는 시스템 정의 리소스인 반면 문서, 저장 프로시저, 트리거, UDF 및 첨부 파일에는 임의의 사용자 정의 JSON 콘텐츠가 포함됩니다.

##<a name="develop"></a> Azure DocumentDB를 사용하여 개발
Azure DocumentDB는 HTTP/HTTPS 요청을 수행할 수 있는 임의의 언어로 호출할 수 있는 REST API를 통해 리소스를 노출합니다. 또한 DocumentDB는 많이 사용되는 몇 개의 언어를 위한 프로그래밍 라이브러리를 제공합니다. 이 라이브러리는 주소 캐싱, 예외 관리, 자동 재시도 등과 같은 세부 정보를 처리하여 Azure DocumentDB 작업의 많은 측면을 간소화합니다. 현재 라이브러리는 다음 언어 및 플랫폼에 사용할 수 있습니다.

다운로드 | 설명서
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET 라이브러리](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js 라이브러리](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java 라이브러리](http://azure.github.io/azure-documentdb-java/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript 라이브러리](http://azure.github.io/azure-documentdb-js/)
해당 없음 | [서버 쪽 JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python 라이브러리](http://azure.github.io/azure-documentdb-python/)

기본적인 만들기, 읽기, 업데이트 및 삭제 작업 외에도 DocumentDB는 JSON 문서 검색을 위한 풍부한 SQL 쿼리 인터페이스 및 JavaScript 응용 프로그램 논리의 트랜잭션 실행에 대한 서버 쪽 지원을 제공합니다. 쿼리 및 스크립트 실행 인터페이스는 모든 플랫폼 라이브러리 및 REST API를 통해 사용할 수 있습니다.

### SQL 쿼리
Azure DocumentDB는 관계적, 계층적 및 공간적 쿼리를 지원하는 JavaScript 유형 시스템 및 식을 기반으로 하는 SQL 언어를 사용하여 문서 쿼리를 지원합니다. DocumentDB 쿼리 언어는 JSON 문서를 쿼리하는 단순하지만 강력한 인터페이스입니다. 이 언어는 ANSI SQL 문법의 하위 집합을 지원하며 JavaScript 개체, 배열, 개체 생성 및 함수 호출의 전체 통합을 추가합니다. DocumentDB는 개발자의 명시적 스키마 또는 인덱싱 힌트 없이 해당 쿼리 모델을 제공합니다.

UDF(사용자 정의 함수)를 DocumentDB에 등록하고 SQL 쿼리의 일부로 참조하여 사용자 지정 응용 프로그램 논리를 지원하도록 문법을 확장할 수 있습니다. 이 UDF는 JavaScript 프로그램으로 작성되며 데이터베이스 내에서 실행됩니다.

.NET 개발자를 위해 DocumentDB는 [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx)의 일부로 LINQ 쿼리 공급자도 제공합니다.

### 트랜잭션 및 JavaScript 실행
DocumentDB를 사용하면 JavaScript로만 작성된 명명된 프로그램으로 응용 프로그램 논리를 작성할 수 있습니다. 이 프로그램은 컬렉션에 대해 등록되며 지정된 컬렉션 내 문서에 대한 데이터베이스 작업을 실행할 수 있습니다. 트리거, 저장 프로시저 또는 사용자 정의 함수로 실행하기 위해 JavaScript를 등록할 수 있습니다. 트리거와 저장 프로시저는 문서를 만들고 읽고 업데이트하고 삭제할 수 있는 반면 사용자 정의 함수는 컬렉션에 대한 쓰기 권한 없이 쿼리 실행 논리의 일부로 실행됩니다.

DocumentDB 내의 JavaScript 실행은 JavaScript를 Transact-SQL의 최신 대체로 사용하여 관계형 데이터베이스 시스템이 지원하는 개념을 따라 모델링됩니다. 모든 JavaScript 논리는 스냅숏 격리를 사용하여 앰비언트 ACID 트랜잭션 내에서 실행됩니다. 실행 중 JavaScript에서 예외가 발생하면 전체 트랜잭션이 중단됩니다.

## 다음 단계
Azure 계정이 이미 있는 경우 [Azure 포털](https://portal.azure.com/#gallery/Microsoft.DocumentDB)에서 [DocumentDB 데이터베이스 계정을 만들기](documentdb-create-account.md)로 DocumentDB를 시작할 수 있습니다.

Azure 계정이 없는 경우

- [Azure 무료 평가판](https://azure.microsoft.com/free/)에 등록하면 30일간 ₩240,000으로 모든 Azure 서비스를 사용해 볼 수 있습니다.
- MSDN 구독이 있는 경우 모든 Azure 서비스에 사용할 수 있는 [월별 150달러의 무료 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)에 대한 자격이 주어집니다.

자세히 배울 준비가 되면 [학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)를 방문하여 사용 가능한 모든 학습 리소스를 살펴보세요.


[1]: ./media/documentdb-introduction/json-database-resources1.png
 

<!---HONumber=AcomDC_0706_2016-->