<properties 
	pageTitle="DocumentDB Node.js SDK | Microsoft Azure" 
	description="릴리스 날짜, 사용 중지 날짜 및 DocumentDB Node.js SDK의 각 버전 간의 변경 내용을 포함한 Node.js SDK에 대해 모두 알아봅니다." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/26/2016" 
	ms.author="andrl"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Node.js SDK

<table>
<tr><td>**다운로드**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**기고**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**설명서**</td><td>[Node.js SDK 참조 설명서](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**샘플**</td><td>[Node.js 코드 샘플](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr>
<tr><td>**시작**</td><td>[Node.js SDK 시작](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**현재 지원되는 플랫폼**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##릴리스 정보

###<a name="1.7.0"/>1.7.0</a>

- 문서의 TTL(Time to Live) 기능에 대한 지원이 추가되었습니다.

###<a name="1.6.0"/>1.6.0</a>
- [분할된 컬렉션](documentdb-partition-data.md) 및 [사용자 정의 성능 수준](documentdb-performance-levels.md)이 구현되었습니다. 

###<a name="1.5.6"/>1.5.6</a>

- 잘못된 concat 결과로 인해 링크를 반환하지 못했던 RangePartitionResolver.resolveForRead 버그가 수정되었습니다.

###<a name="1.5.5"/>1.5.5</a>

- hashParitionResolver resolveForRead() 해결: 예외를 throw하는 제공된 파티션 키가 없는 경우 모든 등록된 링크의 목록을 대신 반환합니다.

###<a name="1.5.4"/>1.5.4</a>

- 문제 해결 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - 전용 HTTPS 에이전트: DocumentDB 목적으로 전역 에이전트를 수정하지 않도록 합니다. 모든 lib의 요청에 대해 전용 에이전트를 사용 합니다.

###<a name="1.5.3"/>1.5.3</a>

- 문제 해결 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 미디어 id에서 대시를 올바르게 처리합니다.

###<a name="1.5.2"/>1.5.2</a>

- 문제 해결 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 수신기 누수 경고

###<a name="1.5.1"/>1.5.1</a>

- 문제 해결 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - 대/소문자 구분 시스템에 대해 해시-해시로 폴더 이름 바꾸기

### <a name="1.5.0"/>1.5.0</a>

- 해시 및 범위 파티션 해결 프로그램을 추가하여 분할 지원 구현

### <a name="1.4.0"/>1.4.0</a>

- Upsert를 구현합니다. DocumentClient의 새로운 upsertXXX 메서드입니다. 

### <a name="1.3.0"/>1.3.0</a>

- 다른 SDK와 정렬된 버전 번호를 가져오기 위해 건너뛰었습니다.

### <a name="1.2.2"/>1.2.2</a>

- 프라미스 래퍼를 새 리포지토리로 분할합니다.
- npm 레지스트리에 대한 패키지 파일로 업데이트합니다.

### <a name="1.2.1"/>1.2.1</a>

- ID 기반 라우팅을 구현합니다.
- 문제 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) 수정 - 현재 속성이 메서드 current()와 충돌합니다.

### <a name="1.2.0"/>1.2.0</a>

- 지리 공간 인덱스에 대한 지원이 추가되었습니다.
- 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스의 ID는 ?, /, #, &#47;&#47; 문자를 포함할 수 없으며 공백으로 끝날 수도 없습니다. 
- 새 헤더 "인덱스 변환 진행률"을 ResourceResponse에 추가합니다.

### <a name="1.1.0"/>1.1.0</a>

- V2 인덱싱 정책을 구현합니다.

### <a name="1.0.3"/>1.0.3</a>

- 문제 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 핵심 및 프라미스 SDK에서 eslint 및 grunt 구성을 구현했습니다.

### <a name="1.0.2"/>1.0.2</a>

- 문제 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - 프라미스 래퍼에 오류가 있는 헤더가 포함되지 않습니다.

### <a name="1.0.1"/>1.0.1</a>

- readConflicts, readConflictAsync 및 queryConflicts를 추가하여 충돌을 쿼리하는 기능을 구현했습니다.
- API 설명서가 업데이트되었습니다.
- 문제 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync 오류

### <a name="1.0.0"/>1.0.0</a>

- GA SDK.

## 릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용한 DocumentDB에 대한 요청은 서비스로부터 거부됩니다.

> [AZURE.WARNING]
**1.0.0** 이전 버전의 Node.js용 Azure DocumentDB SDK 모든 버전은 **2016년 2월 29일**에 사용 중지됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 
| ---	  | ---	         | ---
| [1\.7.0](#1.7.0) | 2016년 4월 26일 |---
| [1\.6.0](#1.6.0) | 2016년 3월 29일 |---
| [1\.5.6](#1.5.6) | 2016년 3월 08일 |---
| [1\.5.5](#1.5.5) | 2016년 2월 02일 |---
| [1\.5.4](#1.5.4) | 2016년 2월 01일 |--- 
| [1\.5.2](#1.5.2) | 2016년 1월 26일 |---
| [1\.5.2](#1.5.2) | 2016년 1월 22일 |---
| [1\.5.1](#1.5.1) | 2016년 1월 4일 |---
| [1\.5.0](#1.5.0) | 2015년 12월 31일 |---
| [1\.4.0](#1.4.0) | 2015년 10월 06일 |---
| [1\.3.0](#1.3.0) | 2015년 10월 06일 |---
| [1\.2.2](#1.2.2) | 2015년 9월 10일 |---
| [1\.2.1](#1.2.1) | 2015년 8월 15일 |---
| [1\.2.0](#1.2.0) | 2015년 8월 05일 |---
| [1\.1.0](#1.1.0) | 2015년 7월 09일 |---
| [1\.0.3](#1.0.3) | 2015년 6월 04일 |---
| [1\.0.2](#1.0.2) | 2015년 5월 23일 |---
| [1\.0.1](#1.0.1) | 2015년 5월 15일 |---
| [1\.0.0](#1.0.0) | 2015년 4월 08일 |---
| 0.9.4-prelease | 2015년 4월 06일 | 2016년 2월 29일
| 0.9.3-prelease | 2015년 1월 14일 | 2016년 2월 29일
| 0.9.2-prelease | 2014년 12월 18일 | 2016년 2월 29일
| 0.9.1-prelease | 2014년 8월 22일 | 2016년 2월 29일
| 0.9.0-prelease | 2014년 8월 21일 | 2016년 2월 29일


## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 참고 항목

DocumentDB에 대해 자세히 알아보려면 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 서비스 페이지를 참조하세요.

<!---HONumber=AcomDC_0518_2016-->