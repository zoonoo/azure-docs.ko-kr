<properties 
	pageTitle="DocumentDB Node.js SDK | Microsoft Azure" 
	description="릴리스 날짜, 사용 중지 날짜 및 DocumentDB Node.js SDK의 각 버전 간의 변경 내용을 포함한 Node.js SDK에 대해 모두 알아봅니다." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Node.js SDK

<table>
<tr><td>**다운로드**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**참가**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**설명서**</td><td>[Node.js SDK 참조 설명서](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**샘플**</td><td>[Node.js 코드 샘플](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr>
<tr><td>**시작**</td><td>[Node.js SDK 시작](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**현재 지원되는 플랫폼**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

## 릴리스 정보

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

- ReadConflicts, readConflictAsync 및 queryConflicts를 추가하여 충돌을 쿼리하는 기능을 구현했습니다.
- 업데이트된 API 설명서
- 문제 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync 오류  

### <a name="1.0.0"/>1.0.0</a>

- GA SDK

## 릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 매끄럽게 전환할 수 있도록 SDK를 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용한 DocumentDB에 대한 요청은 서비스로부터 거부됩니다.

> [AZURE.WARNING]
**1.0.0** 이전 버전의 Node.js용 Azure DocumentDB SDK 모든 버전은 **2016년 2월 29일**에 사용 중지됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 
| ---	  | ---	         | ---
| [1\.4.0](#1.4.0) | 2015년 10월 6일 |---
| [1\.3.0](#1.3.0) | 2015년 10월 6일 |---
| [1\.2.2](#1.2.2) | 2015년 9월 10일 |---
| [1\.2.1](#1.2.1) | 2015년 8월 15일 |---
| [1\.2.0](#1.2.0) |2015년 8월 15일 |---
| [1\.1.0](#1.1.0) | 2015년 7월 9일 |---
| [1\.0.3](#1.0.3) | 2015년 6월 4일 |---
| [1\.0.2](#1.0.2) | 2015년 5월 23일 |---
| [1\.0.1](#1.0.1) | 2015년 5월 15일 |---
| [1\.0.0](#1.0.0) | 2015년 4월 8일 |---
| 0.9.4-prelease | 2015년 4월 6일 | 2016년 2월 29일
| 0.9.3-prelease | 2015년 1월 14일 | 2016년 2월 29일
| 0.9.2-prelease | 2014년 12월 18일 | 2016년 2월 29일
| 0.9.1-prelease | 2014년 8월 22일 | 2016년 2월 29일
| 0.9.0-prelease | 2014년 8월 21일 | 2016년 2월 29일


## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 참고 항목

DocumentDB에 대해 자세히 알아보려면 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 서비스 페이지를 참조하세요.

<!----HONumber=AcomDC_1203_2015-->
