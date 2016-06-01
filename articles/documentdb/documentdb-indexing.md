<properties 
	pageTitle="DocumentDB의 자동 인덱싱 | Microsoft Azure" 
	description="Azure DocumentDB의 자동 인덱싱 작동 방식에 대해 알아봅니다." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2016" 
	ms.author="arramac"/>
	
# Azure DocumentDB에서 자동 인덱싱

이 문서는 2015년 8월 31일 - 9월 4일 사이에 [41st Internal Conference on Very Large Databases](http://www.vldb.org/2015/)에서 발표되는 ["Azure DocumentDB를 사용한 스키마 제약 없는 인덱싱"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) 논문에서 발췌한 것이며 Azure DocumentDB에서 인덱싱이 작동하는 방식을 소개합니다.

이 문서를 읽은 후 다음 질문에 답할 수 있습니다.

- DocumentDB는 JSON 문서에서 스키마를 유추하는 방법
- DocumentDB가 서로 다른 문서 간에 인덱스를 빌드하는 방법
- DocumentDB가 대규모로 자동 인덱싱을 수행하는 방법

##<a id="HowDocumentDBIndexingWorks"></a> DocumentDB 인덱싱 작동 방식

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)는 JSON 용으로 작성된 진정한 스키마 없는 데이터베이스입니다. 대규모 데이터 인덱스는 모든 스키마 또는 보조 인덱스 정의를 예상하거나 필요로 하지 않습니다. DocumentDB를 사용하는 응용 프로그램 데이터 모델을 신속하게 정의하고 반복할 수 있습니다. 컬렉션에 문서를 추가하면 DocumentDB에서 자동으로 모든 문서 속성을 인덱싱하므로 쿼리에 이러한 속성을 사용할 수 있습니다. 자동 인덱싱을 사용하면 스키마 또는 보조 인덱스에 대한 걱정 없이 완전히 임의의 스키마에 속하는 문서를 저장할 수 있습니다.

데이터베이스 및 응용 프로그램 프로그래밍 모델 간의 임피던스 불일치를 제거하는 목표로, DocumentDB는 JSON의 단순성 및 스키마 사양 부족을 이용합니다. 문서에 대해 가정하지 않고 인스턴스 특정 값 외에 스키마가 다양한 DocumentDB 컬렉션 내 문서를 허용합니다. 다른 문서 데이터베이스와 달리 DocumentDB의 데이터베이스 엔진은 문서 스키마의 개념을 알 수 없도록 하고 문서 구조 및 인스턴스 값 사이의 경계를 모호하게 하는 JSON 문법의 수준에서 직접 작동합니다. 차례로 스키마 또는 보조 인덱스 없이 문서를 자동으로 인덱스할 수 있습니다.

DocumentDB의 인덱싱은 JSON 문법에서 문서를 **트리로 표시**할 수 있다는 사실을 이용합니다. JSON 문서를 트리로 표현하려면 문서 아래에 있는 나머지 실제 노드의 상위 항목이 되는 더미 루트 노드를 만들어야 합니다. JSON 문서의 배열 인덱스를 포함하는 각 레이블은 트리의 노드가 됩니다. 아래 그림은 예제 JSON 문서 및 해당 트리 표현을 보여 줍니다.

>[AZURE.NOTE] JSON은 각 문서에 스키마(메타데이터)와 데이터가 둘 다 포함되어 자체적으로 설명합니다. 예를 들어 `{"locationId": 5, "city": "Moscow"}`는 두 속성 `locationId` 및 `city`가 있고 숫자 및 문자열 속성 값을 가진다는 것을 표시합니다. DocumentDB는 문서 스키마를 유추하고 삽입 또는 교체 시 스키마나 보조 인덱스를 정의하지 않고도 인덱스할 수 있습니다.


**트리로서의 JSON 문서:**

![트리로서의 문서](media/documentdb-indexing/DocumentsAsTrees.png)

예를 들어, 위에 표시된 예제에서:

- 위의 예제에서의 JSON 속성인 `{"headquarters": "Belgium"}` 속성은 path/headquarters/Belgium에 해당합니다.
- JSON 배열 `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}`는 `/exports/[]/city/Moscow` 및 `/exports/[]/city/Athens` 경로에 해당합니다.

자동 인덱싱으로, (1) 문서 트리의 모든 경로가 인덱스됩니다(개발자가 특정 경로 패턴을 제외하도록 인덱싱 정책을 명시적으로 구성되지 않는 경우). (2) DocumentDB 컬렉션에 대한 문서의 각 업데이트는 인덱스의 구조를 업데이트합니다(즉, 노드가 추가되거나 제거됩니다.). 문서 자동 인덱싱의 기본 요구 사항 중 하나는 깊이 중첩된 구조의 문서를 인덱스하고 쿼리하는 비용이, 10 수준인 경우, 키-값 쌍을 하나의 수준 깊이로 구성된 플랫 JSON 문서와 동일합니다. 따라서 정규화된 경로 표현은 자동 인덱싱 및 쿼리 하위 시스템이 빌드되는 파운데이션입니다.

경로의 관점에서 균일하게 스키마 및 인스턴스 값 모두를 처리하는 중요한 이유는 논리적으로, 개별 문서와 같이 경로 및 해당 경로를 포함하는 문서 ID 간 맵을 유지함을 보여주는 두 문서의 인덱스가 트리로 표힌될 수도 있기 때문입니다. DocumentDB는 이 사실을 이용하여 컬렉션 내 개별 문서를 나타내는 트리의 모든 집합을 구성하는 인덱스 트리를 빌드합니다. DocumentDB 컬렉션에서 인덱스 트리는 새 문서가 추가되거나 컬렉션에 업데이트되면 시간이 지남에 따라 증가합니다.


**트리로 DocumentDB 인덱스:**

![트리로 인덱스](media/documentdb-indexing/IndexAsTree.png)

스키마가 없음에도 불구하고, DocumentDB의 SQL 및 JavaScript 쿼리 언어는 관계형 프로젝션 및 필터, 문서에서의 계층적 탐색, 공간 작업 및 전적으로 JavaScript로 작성된 UDF의 호출을 제공합니다. DocumentDB의 쿼리 런타임은 데이터의이 인덱스 트리 표현에 대해 직접 작동할 수 있으므로 이 쿼리를 지원할 수입니다.

기본 인덱싱 정책은 자동으로 모든 문서의 모든 속성을 인덱스하고 일관성있는 쿼리(문서 쓰기와 인덱스가 동기적으로 업데이트됨을 의미)를 제공합니다. 어떻게 DocumentDB가 대규모의 인덱스 트리에 대한 일관된 업데이트를 지원합니까? DocumentDB는 쓰기 최적화, 잠금 없는 로그 구조 인덱스 유지 관리 기술을 사용합니다. DocumentDB가 일관성 있는 쿼리를 제공하는 동시에 빠른 쓰기의 지속적인 볼륨을 지원함을 의미합니다.

DocumentDB의 인덱싱은 다중 테넌트를 처리하고 저장소 효율성을 위해 설계되었습니다. 비용 효율성을 위해 인덱스의 디스크에 있는 저장소 오버헤드가 낮고 예측 가능합니다. 인덱스 업데이트는 DocumentDB 컬렉션당 할당된 시스템 리소스의 예산 내에서 수행됩니다.

##<a name="NextSteps"></a> 다음 단계
- 2015년 8월 31일 - 9월 4일 사이에 41st Internal Conference on Very Large Databases에서 발표되는 ["Azure DocumentDB를 사용한 스키마 제약 없는 인덱싱"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)을 다운로드합니다.
- [DocumentDB SQL을 사용한 쿼리](documentdb-sql-query.md)
- [여기](documentdb-indexing-policies.md)서 DocumentDB 인덱스를 사용자 지정하는 방법을 알아보세요.
 

<!---HONumber=AcomDC_0518_2016-->