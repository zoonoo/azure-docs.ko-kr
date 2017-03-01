---
title: "DocumentDB .NET Core API 및 SDK | Microsoft 문서"
description: "릴리스 날짜, 사용 중지 날짜 및 DocumentDB .NET Core SDK의 각 버전 간 변경 내용을 포함하여 .NET Core API 및 SDK에 대한 모든 것을 알아봅니다."
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/16/2017
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: 2a04860ea7349798d5786e50dc1d564896341d60
ms.openlocfilehash: 50f13ae0184e5b9609994d382acc126eb2868271
ms.lasthandoff: 02/18/2017


---
# <a name="documentdb-apis-and-sdks"></a>DocumentDB API 및 SDK
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

## <a name="documentdb-net-core-api-and-sdk"></a>DocumentDB .NET Core API 및 SDK
<table>

<tr><td>**SDK 다운로드**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API 설명서**</td><td>[.NET API 참조 설명서](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**샘플**</td><td>[.NET 코드 샘플](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**시작**</td><td>[DocumentDB.NET Core SDK 시작](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**웹앱 자습서**</td><td>[DocumentDB를 사용한 웹 응용 프로그램 개발](documentdb-dotnet-application.md)</td></tr>

<tr><td>**현재 지원되는 프레임워크**</td><td>[.NET Standard 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>릴리스 정보

DocumentDB .NET Core SDK에는 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md)의 최신 버전에 대응하는 기능이 있습니다.

> [!NOTE] 
> DocumentDB .NET Core SDK는 UWP(유니버설 Windows 플랫폼) 앱과 호환되지 않습니다. UWP 앱을 지원하는 .NET Core SDK에 관심이 있는 경우 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com)(으)로 전자 메일을 보내세요.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.1.0)

분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.0.0)

DocumentDB .NET Core SDK를 사용하면 Windows, Mac 및 Linux에서 실행하는 빠른 플랫폼 간 [ASP.NET Core](https://www.asp.net/core) 및 [.NET Core](https://www.microsoft.com/net/core#windows) 앱을 빌드할 수 있습니다. DocumentDB .NET Core SDK 최신 릴리스는 [Xamarin](https://www.xamarin.com)과 완벽하게 호환되며 iOS, Android 및 Mono(Linux)를 대상으로 하는 응용 프로그램을 빌드하는 데 사용됩니다.  

### <a name="a-name010-preview010-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentdbcore010-preview"></a><a name="0.1.0-preview"/>[0.1.0-preview](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/0.1.0-preview)

DocumentDB .NET Core Preview SDK를 사용하면 Windows, Mac 및 Linux에서 실행하는 빠른 플랫폼 간 [ASP.NET Core](https://www.asp.net/core) 및 [.NET Core](https://www.microsoft.com/net/core#windows) 앱을 빌드할 수 있습니다.

DocumentDB .NET Core Preview SDK에는 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md)의 최신 버전에 대응하는 기능이 있으며 다음이 지원됩니다.
* 모든 [연결 모드](documentdb-performance-tips.md#networking): 게이트웨이 모드, Direct TCP 및 Direct HTTP 
* 모든 [일관성 수준](documentdb-consistency-levels.md): 강함, 세션, 제한된 부실, 최종
* [분할된 컬렉션](documentdb-partition-data.md) 
* [다중 하위 지역 데이터베이스 계정 및 지역에서 복제](documentdb-distribute-data-globally.md)

이 SDK와 관련된 질문이 있으면 [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)에 게시하거나 [github 저장소](https://github.com/Azure/azure-documentdb-dotnet/issues)에서 문제를 제기하세요. 

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.1.0](#1.1.0) |2017년 2월 16일 |--- |
| [1.0.0](#1.0.0) |2016년 12월 21일 |--- |
| [0.1.0-preview](#0.1.0-preview) |2016년 11월 15일 |2015년 12월 31일 |

## <a name="see-also"></a>참고 항목
DocumentDB에 대해 자세히 알아보려면 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 서비스 페이지를 참조하세요. 


