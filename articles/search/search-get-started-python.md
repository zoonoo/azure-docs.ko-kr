---
title: 'Python 빠른 시작: Azure Search REST Api를 사용 하 여 인덱스 만들기, 로드 및 쿼리-Azure Search'
description: Python, Jupyter 노트북 및 Azure Search REST API를 사용 하 여 인덱스를 만들고, 데이터를 로드 하 고, 쿼리를 실행 하는 방법을 설명 합니다.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 1c570549514ff5a5e7e598aa54d8e2ac4b5a5341
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849793"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>빠른 시작: Jupyter 노트북을 사용 하 여 Python에서 Azure Search 인덱스 만들기
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [포털](search-create-index-portal.md)
> 

Python 및 [AZURE SEARCH REST api](https://docs.microsoft.com/rest/api/searchservice/)를 사용 하 여 Azure Search 인덱스를 만들고 로드 하 고 쿼리 하는 Jupyter 노트북을 빌드 하세요. 이 문서에서는 노트북을 단계별로 빌드하는 방법을 설명 합니다. 또는 [완성 된 Jupyter Python 노트북을 다운로드 하 여 실행할](https://github.com/Azure-Samples/azure-search-python-samples)수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작에는 다음 서비스 및 도구가 필요 합니다. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section) - Python 3.x 및 Jupyter Notebook 제공

+ [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 무료 계층을 사용할 수 있습니다. 

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="connect-to-azure-search"></a>Azure Search에 연결

이 작업에서 Jupyter 노트북을 시작 하 고 Azure Search에 연결할 수 있는지 확인 합니다. 서비스의 인덱스 목록을 요청 하 여이 작업을 수행 합니다. Anaconda3를 사용 하는 Windows에서는 Anaconda Navigator를 사용 하 여 노트북을 시작할 수 있습니다.

1. 새 Python3 노트북을 만듭니다.

1. 첫 번째 셀에서 JSON 작업에 사용 되는 라이브러리를 로드 하 고 HTTP 요청을 작성 합니다.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. 두 번째 셀에서 모든 요청에서 상수로 사용할 요청 요소를 입력 합니다. 검색 서비스 이름(YOUR-SEARCH-SERVICE-NAME)과 관리자 API 키(YOUR-ADMIN-API-KEY)를 유효한 값으로 바꿉니다. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. 세 번째 셀에서 요청을 작성 합니다. 이 GET 요청은 검색 서비스의 인덱스 컬렉션을 대상으로 하며 기존 인덱스의 이름 속성을 선택 합니다.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. 각 단계를 실행 합니다. 인덱스가 있는 경우 응답에는 인덱스 이름 목록이 포함 됩니다. 아래 스크린샷에서는 서비스에 이미 azureblob 인덱스와 realestate-us-sample 인덱스가 있습니다.

   ![Azure Search에 대 한 HTTP 요청이 있는 Jupyter 노트북의 Python 스크립트](media/search-get-started-python/connect-azure-search.png "Azure Search에 대 한 HTTP 요청이 있는 Jupyter 노트북의 Python 스크립트")

   반대로 빈 인덱스 컬렉션은 다음 응답을 반환 합니다.`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

포털을 사용 하지 않는 경우 데이터를 로드 하기 전에 서비스에 인덱스가 있어야 합니다. 이 단계에서는 [Create index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) 를 사용 하 여 인덱스 스키마를 서비스로 푸시합니다.

인덱스의 필수 요소에는 이름, 필드 컬렉션 및 키가 포함 됩니다. Fields 컬렉션은 *문서의*구조를 정의 합니다. 각 필드에는 필드를 사용 하는 방법 (예: 전체 텍스트 검색, 필터링 또는 검색 결과에서 검색할 수 있는지 여부)을 결정 하는 이름, 유형 및 특성이 있습니다. 인덱스 내에서 형식의 `Edm.String` 필드 중 하나를 문서 id의 *키* 로 지정 해야 합니다.

이 인덱스의 이름은 "호텔-퀵 스타트" 이며 아래에 표시 되는 필드 정의가 있습니다. 다른 연습에서 사용 되는 더 큰 [호텔 인덱스](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) 의 하위 집합입니다. 이 빠른 시작에서 간단히 살펴볼 것입니다.

1. 다음 셀에서 다음 예를 셀에 붙여넣어 스키마를 제공 합니다. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. 다른 셀에서 요청을 작성 합니다. 이 PUT 요청은 검색 서비스의 인덱스 컬렉션을 대상으로 하 고 이전 셀에서 제공한 인덱스 스키마를 기반으로 인덱스를 만듭니다.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. 각 단계를 실행 합니다.

   응답은 스키마의 JSON 표현을 포함 합니다. 다음 스크린샷에는 응답의 일부만 표시 됩니다.

    ![인덱스 만들기 요청](media/search-get-started-python/create-index.png "인덱스 만들기 요청")

> [!Tip]
> 인덱스 생성을 확인 하는 또 다른 방법은 포털의 인덱스 목록을 확인 하는 것입니다.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

문서를 푸시 하려면 인덱스의 URL 끝점에 대 한 HTTP POST 요청을 사용 합니다. REST API은 [문서를 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)합니다. 문서는 GitHub의 [Hotelsdata](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) 에서 시작 됩니다.

1. 새 셀에서 인덱스 스키마를 준수 하는 네 개의 문서를 제공 합니다. 각 문서에 대 한 업로드 동작을 지정 합니다.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. 다른 셀에서 요청을 작성 합니다. 이 POST 요청은 호텔-퀵 스타트 인덱스의 docs 컬렉션을 대상으로 하 고 이전 단계에서 제공 된 문서를 푸시합니다.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. 각 단계를 실행 하 여 검색 서비스의 인덱스에 문서를 푸시합니다. 결과는 다음 예제와 유사 하 게 표시 됩니다. 

    ![인덱스에 문서 보내기](media/search-get-started-python/load-index.png "인덱스에 문서 보내기")

## <a name="3---search-an-index"></a>3 - 인덱스 검색

이 단계에서는 [REST API 문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents)을 사용 하 여 인덱스를 쿼리 하는 방법을 보여 줍니다.

1. 셀에서 빈 검색 (search = *)을 실행 하 여 임의 문서의 순위가 지정 되지 않은 목록 (검색 점수 = 1.0)을 반환 하는 쿼리 식을 제공 합니다. 기본적으로 Azure Search는 한 번에 50 일치 항목을 반환 합니다. 구조적으로이 쿼리는 전체 문서 구조와 값을 반환 합니다. 결과의 모든 문서 수를 가져오려면 $count = true를 추가 합니다.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. 새 셀에서 다음 예제를 입력 하 여 "호텔" 및 "wifi" 라는 용어를 검색 합니다. $Select 추가 하 여 검색 결과에 포함할 필드를 지정 합니다.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. 다른 셀에서 요청을 작성 합니다. 이 GET 요청은 호텔-퀵 스타트 인덱스의 docs 컬렉션을 대상으로 하 고 이전 단계에서 지정한 쿼리를 연결 합니다.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. 각 단계를 실행 합니다. 결과는 다음 출력과 유사 하 게 표시 됩니다. 

    ![인덱스 검색](media/search-get-started-python/search-index.png "인덱스 검색")

1. 구문에 대 한 느낌을 얻기 위해 몇 가지 다른 쿼리 예제를 시도 합니다. 를 `searchstring` 다음 예제로 바꾸고 검색 요청을 다시 실행할 수 있습니다. 

   필터 적용: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   위의 두 가지 결과를 가져옵니다.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    특정 필드를 기준으로 정렬:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 버전의 호텔 인덱스를 사용 하는 것이 간소화 되었습니다. 전체 버전을 만들어 더 흥미로운 쿼리를 사용해 볼 수 있습니다. 전체 버전 및 모든 50 문서를 가져오려면 **데이터 가져오기** 마법사를 실행 하 여 기본 제공 샘플 데이터 원본에서 *호텔-샘플* 을 선택 합니다.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal에서 인덱스를 만듭니다.](search-get-started-portal.md)
