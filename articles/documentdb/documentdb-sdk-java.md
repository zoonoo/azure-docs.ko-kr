---
title: "Azure DocumentDB Java API, SDK 및 리소스 | Microsoft Docs"
description: "릴리스 날짜, 사용 중지 날짜 및 DocumentDB Java SDK의 각 버전 간의 변경 내용을 포함하는 Java API 및 SDK에 대한 모든 것을 알아봅니다."
services: documentdb
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/16/2017
ms.author: khdang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 6e558d2b651b9c50bafa5814a18ad360f7beb2f0
ms.lasthandoff: 03/25/2017


---
# <a name="documentdb-java-sdk-release-notes-and-resources"></a>DocumentDB Java SDK: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK 다운로드**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**API 설명서**</td><td>[Java API 참조 설명서](http://azure.github.io/azure-documentdb-java/)</td></tr>

<tr><td>**SDK에 참여**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**시작**</td><td>[Java SDK 시작](documentdb-java-get-started.md)</td></tr>

<tr><td>**웹앱 자습서**</td><td>[DocumentDB를 사용한 웹 응용 프로그램 개발](documentdb-java-application.md)</td></tr>

<tr><td>**현재 지원되는 런타임**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name11001100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb1100"></a><a name="1.10.0"/>[1.10.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.10.0)
* 초당 2,500 RU 및 초당 100 RU의 규모 조정 능력을 통해 분할된 컬렉션을 지원합니다.
* 일부 쿼리에서 NullRef 예외를 일으킬 수 있는 네이티브 어셈블리 버그를 수정했습니다.

### <a name="a-name196196httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb196"></a><a name="1.9.6"/>[1.9.6](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.6)
* 게이트웨이 모드에서 쿼리에 대한 예외를 일으킬 수 있는 쿼리 엔진 구성에서 버그를 수정했습니다.
* 컬렉션을 만든 후 즉시 요청에 대한 "소유자 리소스를 찾을 수 없습니다." 예외를 일으킬 수 있는 세션 컨테이너에 몇 가지 버그가 수정되었습니다.

### <a name="a-name195195httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb195"></a><a name="1.9.5"/>[1.9.5](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.5)
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다. [집계 지원](documentdb-sql-query.md#Aggregates)을 참조하세요.
* 변경 피드에 대한 지원이 추가되었습니다.
* RequestOptions.setPopulateQuotaInfo를 통한 컬렉션 할당량 정보에 대한 지원이 추가되었습니다.
* RequestOptions.setScriptLoggingEnabled를 통한 저장 프로시저 스크립트에 대한 지원이 추가되었습니다.
* 제한 오류가 발생할 때 DirectHttps 모드의 쿼리가 중단될 수 있는 버그를 수정했습니다.
* 세션 일관성 모드의 버그를 수정했습니다.
* 요청 속도가 높은 경우 HttpContext에서 NullReferenceException을 유발할 수 있는 버그를 수정했습니다.
* DirectHttps 모드의 성능이 향상되었습니다.

### <a name="a-name194194httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb194"></a><a name="1.9.4"/>[1.9.4](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.4)
* ConnectionPolicy.setProxy() API와 함께 샘플 클라이언트 인스턴스 기반 프록시 지원이 추가되었습니다.
* DocumentClient 인스턴스를 올바르게 종료하기 위한 DocumentClient.close() API가 추가되었습니다.
* 게이트웨이 대신 네이티브 어셈블리에서 쿼리 계획을 파생하여 직접 연결 모드의 쿼리 성능을 개선했습니다.
* 사용자가 POJO에서 JsonIgnoreProperties를 정의할 필요가 없도록 FAIL_ON_UNKNOWN_PROPERTIES = false로 설정했습니다.
* SLF4J를 사용하도록 로깅을 리팩터링했습니다.
* 일관성 판독기의 몇 가지 버그를 수정했습니다.

### <a name="a-name193193httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb193"></a><a name="1.9.3"/>[1.9.3](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.3)
* 직접 연결 모드에서 연결 누수를 방지하기 위해 연결 관리의 버그를 수정했습니다.
* NullReferenece 예외가 throw 될 수 있는 상위 쿼리의 버그를 수정했습니다.
* 내부 캐시에 대한 네트워크 호출의 수를 줄여 성능을 개선했습니다.
* DocumentClientException에 상태 코드 ActivityID 및 요청 URI를 추가하여 문제 해결을 개선했습니다.

### <a name="a-name192192httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb192"></a><a name="1.9.2"/>[1.9.2](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.2)
* 연결 관리의 문제를 해결하여 안정성을 높였습니다.

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)
* BoundedStaleness 일관성 수준에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 CRUD 작업에 대한 직접 연결 지원이 추가되었습니다.
* SQL을 사용하여 데이터베이스를 쿼리할 때의 버그가 수정되었습니다.
* 세션 토큰이 잘못 설정된 경우에 발생할 수 있는 세션 캐시의 버그가 수정되었습니다.

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)
* 파티션 간 병렬 쿼리에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 TOP/ORDER BY 쿼리에 대한 지원이 추가되었습니다.
* 강력한 일관성에 대한 지원이 추가되었습니다.
* 직접 연결을 사용할 때 이름 기반 요청에 대한 지원이 추가되었습니다.
* 모든 요청 다시 시도 간에 ActivityId가 일관성을 유지하도록 수정되었습니다.
* 동일한 이름의 컬렉션을 다시 만들 때 세션 캐시와 관련된 버그가 수정되었습니다.
* 지역 펜싱 공간 쿼리에 대한 컬렉션 인덱싱 정책을 지정하는 동안 Polygon 및 LineString 데이터 형식이 추가되었습니다.
* Java 1.8용 Java 문서 관련 문제가 해결되었습니다.

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
* PartitionKeyDefinitionMap에서 버그를 수정하여 단일 파티션 컬렉션을 캐시하고 추가 인출 파티션 키를 요청하지 않았습니다.
* 잘못된 파티션 키 값이 제공되는 경우 버그가 다시 시도되지 않도록 수정했습니다.

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.
* 최대 재시도 횟수와 최대 재시도 대기 시간을 지정하는 옵션과 함께 정제된 요청의 자동 재시도 지원이 추가되었습니다.  RetryOptions와 ConnectionPolicy.getRetryOptions()을 참조하세요.
* IPartitionResolver 기반 사용자 지정 파티셔닝 코드의 사용이 중단되었습니다. 보다 큰 저장소 및 처리량에는 파티션된 컬렉션을 사용하세요.

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
* 제한에 대한 재시도 정책 지원이 추가되었습니다.  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
* 문서에 대한 TTL(Time to Live) 지원이 추가되었습니다.

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
* [분할된 컬렉션](documentdb-partition-data.md) 및 [사용자 정의 성능 수준](documentdb-performance-levels.md)이 구현되었습니다.

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
* 다른 SDK와 일치하도록 little-endian의 해시 값을 생성하는 HashPartitionResolver의 버그를 수정합니다.

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
* 여러 파티션 간의 응용 프로그램 분할을 지원하기 위해 해시 및 범위 파티션 해결 프로그램을 추가합니다.

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
* Upsert를 구현합니다. 새로운 upsertXXX 메서드가 Upsert 기능을 지원하기 위해 추가되었습니다.
* ID 기반 라우팅을 구현합니다. 공용 API 변경 없이 모두 내부에서 변경됩니다.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* 다른 SDK와 일치하는 버전 번호 가져오기를 생략하는 릴리스

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
* 지리 공간 인덱스 지원
* 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스에 대한 ID는 ?, /, #, \, 문자를 포함하거나 공백으로 끝날 수 없습니다.
* ResourceResponse에 새 헤더 "인덱스 변환 진행"을 추가합니다.

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
* V2 인덱싱 정책 구현

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
* GA SDK

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용한 DocumentDB에 대한 요청은 서비스로부터 거부됩니다.

> [!WARNING]
> **1.0.0** 이전 버전의 Java에 대한 모든 버전의 Azure DocumentDB SDK는 **2016년 2월 29일**에 사용 중지됩니다.
> 
> 

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.10.0](#1.10.0) |2017년 3월 11일 |--- |
| [1.9.6](#1.9.6) |2017년 2월 21일 |--- |
| [1.9.5](#1.9.5) |2017년 1월 31일 |--- |
| [1.9.4](#1.9.4) |2016년 11월 24일 |--- |
| [1.9.3](#1.9.3) |2016년 10월 30일 |--- |
| [1.9.2](#1.9.2) |2016년 10월 28일 |--- |
| [1.9.1](#1.9.1) |2016년 10월 26일 |--- |
| [1.9.0](#1.9.0) |2016년 10월 3일 |--- |
| [1.8.1](#1.8.1) |2016년 6월 30일 |--- |
| [1.8.0](#1.8.0) |2016년 6월 14일 |--- |
| [1.7.1](#1.7.1) |2016년 4월 30일 |--- |
| [1.7.0](#1.7.0) |2016년 4월 27일 |--- |
| [1.6.0](#1.6.0) |2016년 3월 29일 |--- |
| [1.5.1](#1.5.1) |2015년 12월 31일 |--- |
| [1.5.0](#1.5.0) |2015년 12월 4일 |--- |
| [1.4.0](#1.4.0) |2015년 10월 5일 |--- |
| [1.3.0](#1.3.0) |2015년 10월 5일 |--- |
| [1.2.0](#1.2.0) |2015년 8월 5일 |--- |
| [1.1.0](#1.1.0) |2015년 7월 9일 |--- |
| [1.0.1](#1.0.1) |2015년 5월 12일 |--- |
| [1.0.0](#1.0.0) |2015년 4월 7일 |--- |
| 0.9.5-prelease |2015년 3월 9일 |2016년 2월 29일 |
| 0.9.4-prelease |2015년 2월 17일 |2016년 2월 29일 |
| 0.9.3-prelease |2015년 1월 13일 |2016년 2월 29일 |
| 0.9.2-prelease |2014년 12월 19일 |2016년 2월 29일 |
| 0.9.1-prelease |2014년 12월 19일 |2016년 2월 29일 |
| 0.9.0-prelease |2014년 12월 10일 |2016년 2월 29일 |

## <a name="faq"></a>FAQ
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
DocumentDB에 대해 자세히 알아보려면 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 서비스 페이지를 참조하세요.


