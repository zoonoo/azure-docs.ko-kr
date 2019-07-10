---
title: 'PowerShell 빠른 시작: 만들기, 로드 및 Azure Search REST Api-Azure Search를 사용 하 여 인덱스를 쿼리 합니다.'
description: 인덱스 만들기, 데이터를 로드 및 PowerShell의를 사용 하 여 쿼리를 실행 하는 방법과 Invoke-restmethod 및 Azure Search REST API입니다.
ms.date: 06/10/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: afd73ee3461fff11019be887dbf3078963644c5b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485495"
---
# <a name="quickstart-create-an-azure-search-index-in-powershell-using-rest-apis"></a>빠른 시작: REST Api를 사용 하 여 PowerShell에서 Azure Search 인덱스 만들기
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [포털](search-create-index-portal.md)
> 

이 문서 만들기, 로드 및 PowerShell을 사용 하 여 Azure Search 인덱스를 쿼리 하는 과정을 단계별로 안내 하며 [Azure Search REST Api](https://docs.microsoft.com/rest/api/searchservice/)합니다. 이 문서에서는 PowerShell 명령을 대화형으로 실행 하는 방법에 설명 합니다. 또는 완성 된 스크립트를 실행할 수 있습니다. 복사본을 다운로드 하려면로 이동 합니다 [azure search-powershell 샘플](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) 리포지토리.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만든 다음 [Azure Search에 등록](search-create-service-portal.md)하십시오.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 사용되는 서비스와 도구는 다음과 같습니다. 

+ [PowerShell 5.1 이상](https://github.com/PowerShell/PowerShell)를 사용 하 여 [Invoke-restmethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) 순차적이 고 대화형 단계에 대 한 합니다.

+ [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

2. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="connect-to-azure-search"></a>Azure Search에 연결

1. PowerShell에서 만들기를 **$headers** 콘텐츠 형식 및 API 키를 저장할 개체입니다. 검색 서비스에 유효한 키를 사용 하 여 관리 API 키 (YOUR-관리자-API-키)를 대체 합니다. 세션의 기간에 대 한이 헤더를 한 번 설정 해야 하지만 모든 요청에 추가 합니다. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. 만들기는 **$url** 서비스를 지정 하는 개체 컬렉션을 인덱싱합니다. 올바른 검색 서비스를 사용 하 여 서비스 이름 (YOUR-검색-서비스-이름)을 대체 합니다.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06"
    ```

3. 실행할 **Invoke-restmethod** GET 요청을 서비스에 보내고 연결을 확인 합니다. 추가 **Convertto-json** 서비스에서 다시 보낸 응답을 볼 수 있도록 합니다.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   서비스 비어에 인덱스가 없는 경우 결과 다음 예제와 비슷합니다. 그렇지 않은 경우 인덱스 정의의 JSON 표현을 표시 됩니다.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

포털을 사용 하지 않는 데이터를 로드 하려면 먼저 인덱스 서비스에 있어야 합니다. 이 단계는 인덱스를 정의 하 고 서비스에 푸시합니다. 합니다 [인덱스 REST API 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 이 단계에 사용 됩니다.

인덱스의 필수 요소에는 이름과 필드 컬렉션이 포함 됩니다. 구조를 정의 하는 필드 컬렉션을 *문서*합니다. 각 필드에는 이름, 형식 및 사용 하는 방식을 결정 하는 특성 (예를 들어 인지 전체 텍스트 검색 가능, 필터링 가능, 검색 결과에서 검색 가능). 형식의 필드를 인덱스 `Edm.String` 로 지정 해야 합니다 *키* 문서 id에 대 한 합니다.

이 인덱스 "호텔-빠른 시작" 라는 있고 아래 표시 된 필드 정의 합니다. 보다 넓은 범위의 일부입니다 [호텔 인덱스](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) 다른 연습에서 사용 합니다. 이 빠른 시작에서는 간단한 설명을 위해 잘립니다 했습니다.

1. 이 예제를 만들려면 PowerShell 붙여를 **$body** 인덱스 스키마를 포함 하는 개체입니다.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. 서비스에서 인덱스 컬렉션에 URI를 설정 하며 *호텔-빠른 시작* 인덱스입니다.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. 사용 하 여 명령을 실행 **$url**를 **$headers**, 및 **$body** 서비스에서 인덱스를 만들려고 합니다. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    결과 (간단히 하기 위해 처음 두 필드에 잘림) 다음과 유사 하 게 같아야 합니다.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> 확인을 위해 포털에서 인덱스 목록을 확인할 수 있습니다.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

문서를 푸시 하려면 인덱스의 URL 끝점에 HTTP POST 요청을 사용 합니다. 이 작업에 대 한 REST api [추가, 업데이트 또는 삭제 문서](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)합니다.

1. 이 예제를 만들려면 PowerShell 붙여를 **$body** 업로드 하려는 문서를 포함 하는 개체입니다. 

    이 요청에는 두 개의 전체와 부분 레코드를 포함합니다. 일부 레코드는 불완전 한 문서를 업로드할 수 있습니다 하는 방법을 보여 줍니다. `@search.action` 매개 변수는 인덱싱을 수행 하는 방법을 지정 합니다. 유효한 값에는 업로드, merge, mergeOrUpload 및 삭제 포함 됩니다. MergeOrUpload 동작 하거나 hotelId에 대 한 새 문서를 만들고 = 3, 또는 이미 있는 경우 콘텐츠를 업데이트 합니다.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. 로 끝점을 설정 합니다 *호텔-빠른 시작* 문서 컬렉션 인덱스 작업 (인덱스/호텔-빠른 시작/docs/index)를 포함 합니다.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. 사용 하 여 명령을 실행 **$url**를 **$headers**, 및 **$body** 호텔-빠른 시작 인덱스에 문서를 로드 합니다.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    결과 다음 예와 유사 합니다. 표시 된 [상태 코드 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)합니다.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 - 인덱스 검색

이 단계를 사용 하 여 인덱스를 쿼리 하는 방법을 보여 줍니다.는 [검색 문서 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)합니다.

검색 $urls 작은따옴표를 사용 해야 합니다. 쿼리 문자열에 포함할 **$** 문자 및 있습니다 전체 문자열을 따옴표로 묶은 경우 이스케이프할 필요가 생략할 수 있습니다...

1. 로 끝점을 설정 합니다 *호텔-빠른 시작* 문서 컬렉션 추가 **검색** 쿼리 문자열에 전달할 매개 변수. 
  
   이 문자열에는 빈 검색 실행 (검색 = *), unranked는 목록을 반환 (검색 점수 = 1.0) 임의의 문서. 기본적으로 Azure Search는 한 번에 50 일치를 반환합니다. 구조화 된으로이 쿼리는 전체 문서 구조 및 값을 반환합니다. 추가 **$count = true** 결과의 모든 문서의 개수를 가져올 수 있습니다.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. 보낼 명령을 실행 합니다 **$url** 서비스입니다.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    결과 다음 출력과 유사 합니다.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

구문을 이해할 수 있도록 다른 몇 가지 쿼리 예를 봅니다. 축 자 $filter 쿼리 문자열 검색을 수행, 범위, 특정 필드에 검색 결과 집합을 제한할 수 있습니다.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up"></a>정리 

더 이상 필요한 경우 인덱스를 삭제 해야 합니다. 무료 서비스는 세 가지 인덱스 제한 됩니다. 사용 하지 않는 적극적으로 다른 자습서를 단계별로 실행할 수 있도록 모든 인덱스를 삭제 하려고 할 수 있습니다.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 만들기 및 Azure Search에서 콘텐츠에 액세스 하기 위한 기본 워크플로 단계별로 실행 하려면 PowerShell를 사용 합니다. Azure 데이터 원본에서 인덱싱 등의 고급 시나리오를 이동 좋습니다 염두에서 개념을 사용 하 여

> [!div class="nextstepaction"]
> [REST 자습서: 인덱스 및 반 구조화 된 데이터 (JSON blob)를 Azure Search에서 검색](search-semi-structured-data.md)