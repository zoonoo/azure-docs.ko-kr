---
title: "Xamarin 및 Azure DocumentDB를 사용하여 모바일 응용 프로그램 빌드 | Microsoft Docs"
description: "자습서는 Azure DocumentDB를 사용하여 Xamarin iOS, Android 또는 Forms 응용 프로그램을 만듭니다. DocumentDB는 엄청 빠른 세계적 규모의 모바일 앱용 클라우드 데이터베이스입니다."
services: documentdb
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4a783397b038b7545bbbfc45813db3d9973c5e19
ms.lasthandoff: 03/22/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Xamarin 및 Azure DocumentDB를 사용하여 모바일 응용 프로그램 빌드
대부분의 모바일 앱은 클라우드에서 데이터를 저장해야 하며 Azure DocumentDB는 모바일 앱용 클라우드 데이터베이스입니다. 이 기능에는 모바일 개발자에게 필요한 모든 항목이 있습니다. 또한 완전히 관리되는 NoSQL Database as a Service이며 사용자가 전 세계 어디를 가든지 정확하게 응용 프로그램에 데이터를 가져올 수 있습니다. [Azure DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md)를 사용하여 중간 계층 없이 DocumentDB와 직접 상호 작용하기 위해 Xamarin 모바일 앱을 활성화할 수 있습니다.

이 문서에서는 Xamarin 및 DocumentDB를 사용하여 모바일 앱을 빌드하는 자습서를 제공합니다. [GitHub의 Xamarin 및 DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin)에서 사용자 및 사용 권한을 관리하는 방법을 비롯하여 자습서의 전체 소스 코드를 찾을 수 있습니다.

## <a name="documentdb-capabilities-for-mobile-apps"></a>모바일 앱용 DocumentDB 기능
DocumentDB는 모바일 앱 개발자를 위해 다음과 같은 주요 기능을 제공합니다.

![모바일 앱용 DocumentDB 기능](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* 스키마 없는 데이터에 대한 풍부한 쿼리 DocumentDB는 유형이 다른 컬렉션에서 스키마 없는 JSON 문서로 데이터를 저장하고 스키마 또는 인덱스를 걱정하지 않고 [다양하고 빠른 쿼리](documentdb-sql-query.md)를 제공합니다.
* 빠릅니다. 보장됩니다. DocumentDB를 사용하여 문서를 읽고 쓰는 데 몇 밀리초가 걸립니다. 개발자는 필요한 처리량을 지정할 수 있고 DocumentDB는 99.99% SLA로 지정됩니다.
* 크기가 무제한입니다. DocumentDB 컬렉션은 [앱이 증가함에 따라 증가합니다](documentdb-partition-data.md). 작은 데이터 크기나 초당 수백 개의 요청을 시작할 수 있으며 두 번째 처리량당 페타바이트 크기의 데이터인 수천만, 수억 개의 요청까지 임의로 크게 증가할 수 있습니다.
* 전역적으로 분산됩니다. 모바일 앱 사용자는 전 세계에서 사용할 수 있습니다. DocumentDB는 [전역적으로 분산된 데이터베이스](documentdb-distribute-data-globally.md)이며 맵을 한 번 클릭하여 사용자가 있는 곳의 데이터를 가져올 수 있습니다.
* 풍부한 권한이 기본 제공됩니다. DocumentDB를 사용하여 복잡한 사용자 지정 권한 부여 코드 없이 [사용자별 데이터](https://aka.ms/documentdb-xamarin-todouser)와 같은 인기 있는 패턴 또는 다중 사용자 공유 데이터를 쉽게 구현할 수 있습니다.
* 지역 공간 쿼리입니다. 현재 대부분의 모바일 앱은 지역 상황별 환경을 제공합니다. [지역 공간 유형](documentdb-geospatial.md) DocumentDB에 대한 최고 수준의 지원을 사용하면 이러한 환경을 쉽게 수행할 수 있습니다.
* 이진 첨부 파일입니다. 앱 데이터는 종종 이진 Blob을 포함합니다. 첨부 파일에 대한 기본 지원은 앱 데이터의 원스톱 상점으로 DocumentDB를 쉽게 사용할 수 있습니다.

## <a name="documentdb-and-xamarin-tutorial"></a>DocumentDB 및 Xamarin 자습서
다음 자습서에서는 간편한 5가지 단계에서 Xamarin 및 DocumentDB를 사용하여 모바일 응용 프로그램을 빌드하는 방법을 보여 줍니다. [GitHub의 Xamarin 및 DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin)에서 자습서의 전체 소스 코드를 찾을 수 있습니다.

### <a name="get-started"></a>시작
DocumentDB를 쉽게 시작할 수 있습니다. Azure Portal로 이동하고 새 DocumentDB 계정을 만든 다음 빠른 시작 탭으로 이동하고 Xamarin Forms "할 일 목록" 샘플을 다운로드합니다. DocumentDB 계정에 이미 연결되어 있습니다. 

![모바일 앱의 DocumentDB 빠른 시작](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

기존 Xamarin 앱이 있는 경우 이 [DocumentDB NuGet 패키지](documentdb-sdk-dotnet-core.md)를 추가하기만 하면 됩니다. DocumentDB는 Xamarin.IOS, Xamarin.Android 및 Xamarin Forms 공유 라이브러리를 지원합니다.

### <a name="work-with-data"></a>데이터 작업
데이터 레코드는 유형이 다른 컬렉션에서 스키마 없는 JSON 문서로 DocumentDB에 저장됩니다. 동일한 컬렉션에서 다른 구조를 사용하여 문서를 저장할 수 있습니다.

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Xamarin 프로젝트에서 스키마 없는 데이터에 언어가 통합된 쿼리를 사용할 수 있습니다.

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>사용자 추가
대부분의 시작된 샘플처럼 다운로드한 DocumentDB 샘플은 앱 코드에 하드 코딩된 마스터 키를 사용하여 서비스에 인증합니다. 로컬 에뮬레이터를 제외한 모든 위치에서 실행하려는 앱에 사용하지 않는 것이 좋습니다. 공격자가 마스터 키를 보유한 경우 DocumentDB 계정에 있는 모든 데이터는 손상됩니다. 대신 앱에서 로그인한 사용자에게 레코드에 대한 액세스 권한이 있기를 바랍니다. DocumentDB를 사용하면 개발자가 컬렉션에 대한 응용 프로그램 읽기 또는 읽기/쓰기 액세스 권한을 부여하거나 일련의 문서를 파티션 키 또는 특정 문서로 그룹화할 수 있습니다. 

예를 들어, 할 일 목록 앱을 다중 사용자 "할 일 목록" 앱으로 수정하는 방법은 다음과 같습니다. 

* Facebook, Active Directory 또는 다른 공급자를 사용하여 앱에 대한 로그인을 추가합니다.
* /userId를 파티션 키로 사용하여 DocumentDB UserItems 컬렉션을 만듭니다. 컬렉션에 대한 파티션 키를 지정하면 빠른 쿼리를 제공하는 동안 DocumentDB을 앱 사용자 증가 수만큼 무한히 확장할 수 있습니다.
* 사용자의 파티션 내에서 문서에 대한 액세스 권한을 사용하여 사용자를 인증하고 로그인한 사용자에게 짧은 수명의 토큰을 발급하는 간단한 웹 API인 DocumentDB 리소스 토큰 Broker를 추가합니다. 이 예제에서는 App Service에서 리소스 토큰 Broker를 호스팅합니다.
* 앱을 수정하여 Facebook을 사용하여 리소스 토큰 Broker를 인증하고 로그인한 Facebook 사용자에게 리소스 토큰을 요청한 다음 UserItems 컬렉션에서 사용자 데이터에 액세스합니다.  

[GitHub의 리소스 토큰 Broker](http://aka.ms/documentdb-xamarin-todouser)에서 이 패턴의 전체 코드 샘플을 찾을 수 있습니다. 이 다이어그램에서는 솔루션을 보여 줍니다.

![DocumentDB 사용자 및 사용 권한 broker](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

이제 두 명의 사용자가 동일한 "할 일 목록"을 받은 경우 리소스 토큰 Broker에서 액세스 토큰에 사용 권한을 추가했습니다.

### <a name="scale-on-demand"></a>주문형 확장
DocumentDB는 Database as a Service입니다. 사용자 기반이 늘어나면 VM을 프로비전하거나 코어를 증가시킬 것을 걱정할 필요가 없습니다. 앱에서 필요한 초당 작업의 수(처리량)를 DocumentDB에 지시하면 됩니다. 초당 요청 단위(RU)를 호출하는 처리량 측정을 사용하여 포털 크기 조정 탭을 통해 처리량을 지정할 수 있습니다. 예를 들어 1KB 문서의 읽기 작업에는 1RU가 필요합니다. "처리량" 메트릭에 대한 경고를 추가하여 트래픽 증가를 모니터링하고 경고를 실행하는 대로 처리량을 프로그래밍 방식으로 변경할 수 있습니다.

![주문형 DocumentDB 크기 조정 처리량](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>세계적 규모로 확장
앱이 인기를 얻게 되면 전 세계에 걸쳐 사용자가 늘어날 수 있습니다. 또는 DocumentDB 컬렉션을 만든 Azure 데이터 센터를 운석이 공격한 경우 무방비할 수 있습니다. Azure Portal에서 DocumentDB 계정을 사용하여 맵을 한 번 클릭하여 데이터를 지속적으로 전 세계의 여러 지역에 복제합니다. 이렇게 하면 사용자가 어디서나 데이터를 사용할 수 있고 만일의 경우에 준비된 장애 조치 정책을 추가할 수 있습니다.

![지리적 지역에 DocumentDB 크기 조정](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

축하합니다. 솔루션 완료하고 Xamarin 및 DocumentDB을 사용한 모바일 앱이 있습니다. DocumentDB JavaScript SDK를 사용하는 Cordova 앱 및 DocumentDB REST API를 사용하는 네이티브 iOS/Android 앱에서 비슷한 패턴을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [GitHub의 Xamarin 및 DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin)의 소스 코드를 봅니다.
* [DocumentDB.NET Core SDK](documentdb-sdk-dotnet-core.md)를 다운로드합니다.
* [.NET 응용 프로그램](documentdb-dotnet-samples.md)에 대한 추가 코드 샘플을 찾습니다.
* [DocumentDB의 다양한 쿼리 기능](documentdb-sql-query.md)에 대해 알아봅니다.
* [DocumentDB의 지리 공간적 지원](documentdb-geospatial.md)에 대해 알아봅니다.




