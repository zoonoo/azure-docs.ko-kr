---
title: '빠른 시작: Python 및 Azure Search REST Api'
description: 만들기, 로드 및 Python, Jupyter 노트북 및 Azure Search REST API를 사용 하 여 인덱스를 쿼리 합니다.
ms.date: 05/23/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 99b4ec0be8e9fa631c5081edd42474ea89dc5dc3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244783"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>빠른 시작: Jupyter Python 노트북을 사용 하 여 Azure Search 인덱스 만들기
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [포털](search-create-index-portal.md)
> 

만듭니다, 로드 및 Python을 사용 하 여 Azure Search 인덱스를 쿼리 하는 Jupyter 노트북을 빌드 및 [Azure Search REST Api](https://docs.microsoft.com/rest/api/searchservice/)합니다. 이 문서에서는 빌드 노트북을 단계별로부터 시작 하는 방법에 설명 합니다. 또는 완성 된 notebook을 실행할 수 있습니다. 복사본을 다운로드 하려면로 이동 [Azure Search-python 샘플 리포지토리](https://github.com/Azure-Samples/azure-search-python-samples)합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 사용되는 서비스와 도구는 다음과 같습니다. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), 제공 Python 3.x 및 Jupyter Notebooks 합니다.

+ [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에 대 한 무료 계층을 사용할 수 있습니다. 

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-fiddler/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="connect-to-azure-search"></a>Azure Search에 연결

이 태스크에서는 Jupyter 노트북을 시작 하 고 Azure Search에 연결할 수 있는지 확인 합니다. 서비스에서 인덱스 목록을 요청 하 여이 작업을 합니다. Anaconda3 사용 하 여 Windows, Anaconda 탐색기 notebook에 사용할 수 있습니다.

1. 새 Python3 notebook을 만듭니다.

1. 첫 번째 셀에서 JSON을 사용 하 여 HTTP 요청을 공식화에 사용 되는 라이브러리를 로드 합니다.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. 두 번째 셀에서 모든 요청에 대해 상수 될 요청 요소를 입력 합니다. 유효한 값을 사용 하 여 검색 서비스 이름 (사용자 검색-서비스-이름) 및 관리 API 키 (YOUR-관리자-API-키)를 대체 합니다. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. 세 번째 셀에서 요청을 작성 합니다. GET 요청이 search 서비스의 인덱스 컬렉션을 대상으로 하 고 기존 인덱스의 name 속성을 선택 합니다.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. 각 단계를 실행 합니다. 인덱스가 존재 하는 경우 응답 인덱스 이름 목록을 포함 합니다. 아래 스크린샷에서 서비스 이미 azureblob-인덱스 된 realestate-us-샘플 인덱스입니다.

   ![Azure Search에 HTTP 사용 하 여 Jupyter notebook에서 Python 스크립트 요청](media/search-get-started-python/connect-azure-search.png "Azure Search에 HTTP 사용 하 여 Jupyter notebook에서 Python 스크립트 요청")

   반면, 빈 인덱스 컬렉션을이 응답을 반환 합니다. `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

> [!Tip]
> 무료 서비스에 대해 3 개의 인덱스, 인덱서 및 데이터 원본 제한 됩니다. 이 빠른 시작의 각 파일을 만듭니다. 계속 진행 하기 전에 새 개체를 만들 공간이 있는지 확인 합니다.

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

포털을 사용 하지 않는 데이터를 로드 하려면 먼저 인덱스 서비스에 있어야 합니다. 이 단계에서는 합니다 [인덱스 REST API 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 인덱스 스키마를 서비스에 적용할 합니다.

인덱스의 필수 요소는 이름, 필드 컬렉션 및 키를 포함 합니다. 구조를 정의 하는 필드 컬렉션을 *문서*합니다. 각 필드에 이름, 형식 및 필드가 사용 되는 방법을 결정 하는 특성 (예를 들어 인지 전체 텍스트 검색 가능, 필터링 가능, 검색 결과에서 검색 가능). 형식의 필드를 인덱스 `Edm.String` 로 지정 해야 합니다 *키* 문서 id에 대 한 합니다.

이 인덱스 "호텔 py" 라는 있고 아래 표시 된 필드 정의 합니다. 보다 넓은 범위의 일부입니다 [호텔 인덱스](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) 다른 연습에서 사용 합니다. 이 빠른 시작에서는 간단한 설명을 위해 잘립니다 했습니다.

1. 다음 셀에서 다음 예제에서는 스키마를 제공 하는 셀에 붙여 넣습니다. 

    ```python
    index_schema = {
       "name": "hotels-py",  
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

2. 다른 셀에서 요청을 작성 합니다. 이 PUT 요청 검색 서비스의 인덱스 컬렉션을 대상으로 하 고 이전 셀에서 제공 하는 인덱스 스키마에 따라 인덱스를 만듭니다.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. 각 단계를 실행 합니다.

   응답은 스키마의 JSON 표현을 포함합니다. 다음 스크린 샷에서 응답의 일부만을 표시 됩니다.

    ![인덱스를 만들려면 요청](media/search-get-started-python/create-index.png "인덱스를 만들려면 요청")

> [!Tip]
> 인덱스 생성을 확인 하는 다른 방법은 포털에서 인덱스 목록을 확인 하려면 것입니다.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

문서를 푸시 하려면 인덱스의 URL 끝점에 HTTP POST 요청을 사용 합니다. REST api [추가, 업데이트 또는 삭제 문서](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)합니다. 문서에서 발생 [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) github입니다.

1. 새 셀에서 인덱스 스키마를 준수 하는 네 개의 문서를 제공 합니다. 각 문서에 대 한 업로드 작업을 지정 합니다.

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

2. 다른 셀에서 요청을 작성 합니다. 이 POST 요청 py 호텔 인덱스의 문서 컬렉션을 대상으로 하 고 이전 단계에서 제공 하는 문서를 푸시합니다.

   ```python
   url = endpoint + "indexes/hotels-py/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Search 서비스에서 인덱스에 문서를 푸시 하려면 각 단계를 실행 합니다. 결과 다음 예와 유사 합니다. 

    ![인덱스에 문서를 보낼](media/search-get-started-python/load-index.png "인덱스 문서 보내기")

## <a name="3---search-an-index"></a>3 - 인덱스 검색

이 단계를 사용 하 여 인덱스를 쿼리 하는 방법을 보여 줍니다.는 [검색 문서 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)합니다.


1. 새 셀에는 쿼리 식을 제공 합니다. 다음 예제에서는 "호텔" 및 "wifi"를 검색 합니다. 반환 합니다는 *개수* 일치 하는 문서 및 *선택* 검색 결과에 포함할 필드를 합니다.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

2. 다른 셀에서 요청을 작성 합니다. 이 GET 요청 py 호텔 인덱스의 문서 컬렉션을 대상으로 하 고 이전 단계에서 지정 된 쿼리를 연결 합니다.

   ```python
   url = endpoint + "indexes/hotels-py/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

3. 각 단계를 실행 합니다. 결과 다음 출력과 유사 합니다. 

    ![인덱스 검색](media/search-get-started-python/search-index.png "인덱스 검색")

4. 구문을 이해할 수 있도록 다른 몇 가지 쿼리 예를 봅니다. 다음 예제에서는 searchstring 바꿉니다 수 있으며 다음 검색 요청을 다시 실행 하십시오. 

   필터를 적용 합니다. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description'
   ```

   상위 두 결과 수행 합니다.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'
   ```

    특정 필드를 기준으로 정렬 합니다.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'
   ```

## <a name="clean-up"></a>정리 

더 이상 필요한 경우 인덱스를 삭제 해야 합니다. 무료 서비스는 세 가지 인덱스 제한 됩니다. 다른 자습서에 대 한 공간을 확보 하는 데 적극적으로 사용 하지 않는 모든 인덱스를 삭제 하려고 할 수 있습니다.

   ```python
  url = endpoint + "indexes/hotels-py" + api_version
  response  = requests.delete(url, headers=headers)
   ```

기존 인덱스의 목록을 반환 하 여 인덱스 삭제를 확인할 수 있습니다. 호텔 py 사라집니다 인 성공 요청을 알 수 있습니다.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>다음 단계

쉽게,이 빠른 시작이에서는 호텔 인덱스의 축약된 된 버전을 사용합니다. 흥미로운 쿼리를 사용해 전체 버전을 만들 수 있습니다. 정식 버전 및 모든 50 문서를 가져오려면 다음을 실행 합니다 **데이터 가져오기** 마법사 *호텔 샘플* 기본 제공 샘플 데이터 원본에서.

> [!div class="nextstepaction"]
> [빠른 시작: Azure portal에서 인덱스 만들기](search-get-started-portal.md)
