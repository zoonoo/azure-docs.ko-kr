<properties 
	pageTitle="DocumentDB Java SDK | Microsoft Azure" 
	description="릴리스 날짜, 사용 중지 날짜 및 DocumentDB Java SDK의 각 버전 간의 변경 내용을 포함하는 Java SDK에 대한 모든 것을 알아봅니다." 
	services="documentdb" 
	documentationCenter="java" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Java SDK

<table> <tr><td>**다운로드**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr> <tr><td>**참가**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr> <tr><td>**설명서**</td><td>[Java SDK 참조 설명서](http://azure.github.io/azure-documentdb-java/)</td></tr> <tr><td>**시작**</td><td>[Java SDK 시작](documentdb-java-application.md)</td></tr> <tr><td>**현재 지원되는 런타임**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr> </table></br>

## 릴리스 정보

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Upsert를 구현합니다. 새로운 upsertXXX 메서드가 Upsert 기능을 지원하기 위해 추가되었습니다.
- ID 기반 라우팅을 구현합니다. 공용 API 변경 없이 모두 내부에서 변경됩니다.

### <a name="1.3.0"/>1.3.0
- 다른 SDK와 일치하는 버전 번호 가져오기를 생략하는 릴리스

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- 지리 공간 인덱스 지원
- 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스에 대한 ID는 ?, /, #, \\, 문자를 포함하거나 공백으로 끝날 수 없습니다.
- ResourceResponse에 새 헤더 "인덱스 변환 진행"을 추가합니다.

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- V2 인덱싱 정책 구현

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## 릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용한 DocumentDB에 대한 요청은 서비스로부터 거부됩니다.

> [AZURE.WARNING]**1.0.0** 이전 버전의 Java에 대한 모든 버전의 Azure DocumentDB SDK는 **2016년 2월 29일**에 사용 중지됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 
| ---	  | ---	         | ---
| [1\.4.0](#1.4.0) | 2015년 10월 5일 |--
| [1\.3.0](#1.3.0) | 2015년 10월 5일 |--
| [1\.2.0](#1.2.0) | 2015년 8월 5일 |--
| [1\.1.0](#1.1.0) | 2015년 7월 9일 |--
| [1\.0.1](#1.0.1) | 2015년 5월 12일 |--
| [1\.0.0](#1.0.0) | 2015년 4월 7일 |--| 0.9.5-prelease 
| 2015년 3월 9일 | 2016년 2월 29일 | 0.9.4-prelease 
| 2015년 2월 17일 | 2016년 2월 29일 | 0.9.3-prelease 
| 2015년 1월 13일 | 2016년 2월 29일 | 0.9.2-prelease 
| 2014년 12월 19일 | 2016년 2월 29일 | 0.9.1-prelease 
| 2014년 12월 19일 | 2016년 2월 29일 | 0.9.0-prelease 
| 2014년 12월 10일 | 2016년 2월 29일 | 0.9.0-prelease 

## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 참고 항목

DocumentDB에 대해 자세히 알아보려면 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 서비스 페이지를 참조하세요.

<!----HONumber=AcomDC_1203_2015-->