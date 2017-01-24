---
title: "DocumentDB에서 여러 지역을 사용하여 개발 | Microsoft Docs"
description: "완전히 관리되는 NoSQL 데이터베이스 서비스, Azure DocumentDB에서 여러 지역의 데이터에 액세스하는 방법을 알아봅니다."
services: documentdb
documentationcenter: 
author: kiratp
manager: jhubbard
editor: 
ms.assetid: d4579378-0b3a-44a5-9f5b-630f1fa4c66d
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: kipandya
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a0b1418168d493ce0e669a9eb0594d37e741df7d


---
# <a name="developing-with-multi-region-documentdb-accounts"></a>다중 지역 DocumentDB 계정으로 개발
> [!NOTE]
> DocumentDB 데이터베이스의 전역 배포는 일반적으로 사용 가능하며, 새로 만든 DocumentDB 계정에 대해 자동으로 사용되도록 설정됩니다. 현재, 모든 기존 계정에 대해 전역 배포를 사용하도록 설정하기 위해 작업 중이지만 계정에 대해 전역 배포를 일시적으로 사용하도록 설정하려면 [지원 서비스에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. Microsoft에서 처리해 드리겠습니다.
>
>

[전역 배포](documentdb-distribute-data-globally.md)를 활용하기 위해 클라이언트 응용 프로그램은 문서 작업을 수행하는 데 사용할 정렬된 기본 지역 목록을 지정할 수 있습니다. 이는 연결 정책을 설정하여 수행할 수 있습니다. Azure DocumentDB 계정 구성, 현재 지역 가용성과 지정된 기본 설정 목록에 기초한 최적 끝점은 쓰기 및 읽기 작업을 수행할 SDK가 선택합니다.

이 기본 설정 목록은 DocumentDB 클라이언트 SDK로 연결을 초기화할 때 지정됩니다. SDK는 Azure 지역의 정렬된 목록인 "PreferredLocations"라는 선택적 매개 변수를 수락합니다.

SDK는 현재 쓰기 지역에 모든 쓰기를 자동 전송합니다.

모든 읽기는 PreferredLocations 목록에서 첫 번째 사용 가능한 지역으로 전송됩니다. 요청이 실패하면 클라이언트는 목록의 다음 지역으로 옮겨갑니다.

클라이언트 SDK는 PreferredLocations에 지정된 지역에서 읽기를 시도합니다. 따라서 가령 데이터베이스 계정이 3개 지역에서 사용할 수 있지만 클라이언트는 PreferredLocations에 쓰기에 해당하지 않는 지역 중 두 가지만 지정했다면, 장애 조치 시에도 쓰기 지역에서 읽기를 제공하지 않습니다.

응용 프로그램은 두 가지 속성(WirteEndpoint 및 ReadEndpoint)을 확인하여 SDK가 선택한 현재의 쓰기 끝점과 읽기 끝점을 확인할 수 있습니다. SDK 버전 1.8 이상부터 사용 가능합니다.

PreferredLocations 속성이 설정되지 않는다면 모든 요청은 현재 쓰기 지역에서 제공됩니다.

## <a name="net-sdk"></a>.NET SDK
SDK는 코드 변경 없이 사용할 수 있습니다. 이 경우 SDK는 읽기와 쓰기를 현재 쓰기 하위 지역에 자동으로 가져옵니다.

.NET SDK의 1.8 버전 이상에서 DocumentClient 생성자의 ConnectionPolicy 매개 변수는 Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations라는 속성이 있습니다. 이 속성은 컬렉션 `<string>` 형식이며 지역 이름 목록을 포함합니다. 문자열 값은 [Azure 지역][regions] 페이지의 지역 이름 열에 따라 서식이 지정되고 첫 글자와 마지막 글자 앞/뒤에 공백이 없습니다.

현재 읽기 및 쓰기 끝점은 각각 DocumentClient.WriteEndpoint와 DocumentClient.ReadEndpoint에서 이용할 수 있습니다.

> [!NOTE]
> 끝점의 URL은 수명이 긴 상수로 간주하지 말아야 합니다. 서비스는 언제든지 이 URL을 업데이트할 수 있습니다. SDK가 이런 변경 내용을 자동으로 처리합니다.
>
>

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
    
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();

    //Setting read region selection preference
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript 및 Python SDK
SDK는 코드 변경 없이 사용할 수 있습니다. 이 경우 SDK는 읽기와 쓰기를 현재 쓰기 지역에 자동으로 가져옵니다.

각 SDK의 1.8 버전 이상에서 DocumentClient 생성자의 ConnectionPolicy 매개 변수에는 DocumentClient.ConnectionPolicy.PreferredLocations라는 새로운 속성이 있습니다. 이 매개 변수는 지역 이름 목록을 가지는 문자열 배열입니다. 이름은 [Azure 지역][regions] 페이지의 지역 이름 열에 따라 서식이 지정됩니다. 또한, 편의 개체 AzureDocuments.Regions에 사전 지정된 상수를 사용할 수도 있습니다.

현재 쓰기 및 읽기 끝점은 각각 DocumentClient.getWriteEndpoint와 DocumentClient.getReadEndpoint에서 이용할 수 있습니다.

> [!NOTE]
> 끝점의 URL은 수명이 긴 상수로 간주하지 말아야 합니다. 서비스는 언제든지 이 URL을 업데이트할 수 있습니다. SDK가 이런 변경 내용을 자동으로 처리합니다.
>
>

다음은 NodeJS/Javascript의 코드 예제입니다. Python과 Java도 같은 패턴을 따릅니다.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();

    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>REST (영문)
데이터베이스 계정을 여러 지역에서 이용할 수 있게 되면 클라이언트는 다음 URI에서 GET 요청을 수행하여 가용성을 쿼리할 수 있습니다.

    https://{databaseaccount}.documents.azure.com/

서비스는 지역 목록과 복제본의 해당 DocumentDB 끝점 URI를 반환합니다. 현재 쓰기 지역이 응답에 표시됩니다. 클라이언트는 다음과 같이 모든 추가 REST API 요청에 알맞은 끝점을 선택할 수 있습니다.

예제 응답

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* 모든 PUT, POST 및 DELETE 요청은 표시된 쓰기 URI로 이동해야 합니다.
* 모든 GET과 다른 읽기 전용 요청(예: 쿼리)은 클라이언트가 선택한 끝점으로 이동할 수 있습니다.

읽기 전용 지역에 대한 쓰기 요청은 HTTP 오류 코드 403(“사용 권한 없음”)과 함께 실패합니다.

클라이언트의 최초 검색 단계 이후에 쓰기 지역이 변경되면 나중에 이전 쓰기 지역에 쓰려고 하면 HTTP 오류 코드 403(“사용 권한 없음”)과 함께 실패합니다. 클라이언트는 업데이트된 쓰기 지역을 가져오려면 지역 목록을 다시 가져와야 합니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서 DocumentDB로 데이터를 글로벌 배포하는 방법에 대해 자세히 알아봅니다.

* [DocumentDB로 데이터를 글로벌 배포](documentdb-distribute-data-globally.md)
* [일관성 수준](documentdb-consistency-levels.md)
* [여러 지역에 처리량을 적용하는 방법](documentdb-manage.md)
* [Azure 포털을 사용하여 지역 추가](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/



<!--HONumber=Dec16_HO2-->


