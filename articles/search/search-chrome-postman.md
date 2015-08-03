<properties
	pageTitle="Azure 검색에서 Chrome Postman을 사용하는 방법"
	description="Azure 검색에서 Chrome Postman을 사용하는 방법"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Azure 검색에서 Chrome Postman을 사용하는 방법 #

[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman")은 개발자가 Azure 검색과 같은 REST 기반 API 서비스를 효율적으로 사용할 수 있도록 Google Chrome의 일부로 제공되는 도구입니다. Postman을 사용하면 코드를 작성하지 않고도 Postman을 통해 API 호출을 보내 검색 인덱스를 신속하게 만들고 쿼리할 수 있습니다. 이 방법을 통해 API를 효율적으로 배우고 새 기능을 사용해 볼 수 있습니다.

![][1]

## 요구 사항 ##

Azure 검색 서비스가 있어야 합니다. Azure 검색을 사용하는 모든 사용자 지정 응용 프로그램과 마찬가지로 서비스의 URL과 관리 `api-key`키가 있어야 인덱스를 만들 수 있습니다. 검색 서비스에 대한 값을 가져오는 방법에 대한 지침은 [포털에서 서비스 만들기](search-create-service-portal.md)를 참조하세요.

## Postman 설치 ##
Postman을 다운로드하려면 [Google Chrome Store](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm)를 방문합니다. 이 페이지의 링크를 통해 Postman용 REST 클라이언트를 다운로드하여 설치할 수 있습니다. 설치한 후에는 Chrome App Launcher에서 Postman을 시작할 수 있습니다.

![][2]

## Azure 검색을 쿼리하도록 Postman 구성 ##
Postman을 구성하려면 다음 단계를 따릅니다.

1. "Enter request URL here"에 Azure 검색 서비스 URL을 입력합니다.  
2. URL에 `?api-version=2015-02-28`을 추가합니다. 다른 API 버전을 지정할 수도 있습니다. 자세한 내용은 [검색 서비스 버전 관리](https://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.
3. `GET`을 선택해야 합니다.
4. **Headers** 단추를 클릭합니다.
5. 다음 값을 입력합니다.
	- `api-key`: [관리 키]
	- `Content-Type`: `application/json; charset=utf-8`
6. **Send**를 클릭하여 Azure 검색에 대한 REST 호출을 실행하고 JSON 응답을 시각화합니다.

![][3]

## Postman을 사용하여 Azure 검색 인덱스 만들기 ##

이제 새 Azure 검색 인덱스를 만드는 REST 호출을 실행하여 마지막 단계에서 완료한 항목을 확장합니다. 이전 호출과 달리 인덱스 만들기에는 HTTP PUT 및 인덱스 스키마 정의가 있는 JSON 문서가 필요합니다. 이 샘플에서는 자전거 도로 목록을 저장할 인덱스를 만듭니다. 다음을 수행합니다.

1. 검색 서비스 이름을 사용하여 URL을 `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28`로 변경합니다.
2. 요청 형식을 `GET`에서 `PUT`으로 변경합니다.
3. 원시 본문 내용에 다음 JSON을 입력합니다.

	    {
	    "name": "trails",
	    "fields": [
	    {"name": "id", "type": "Edm.String", "key": true, "searchable": false},
	    {"name": "name", "type": "Edm.String"},
	    {"name": "county", "type": "Edm.String"},
	    {"name": "elevation", "type": "Edm.Int32"},
	    {"name": "location", "type": "Edm.GeographyPoint"} ]
	    }

4. **Send**를 클릭합니다.

![][4]

## Postman을 사용하여 Azure 검색 인덱스에 문서 게시 ##
이제 인덱스를 만들었으므로 문서를 로드할 수 있습니다. 이렇게 하려면 USGS(United States Geological Survey) 데이터 집합에서 5개의 도로에 대한 데이터를 사용하여 문서 그룹을 일괄적으로 게시합니다.

1. 검색 서비스 이름을 사용하여 URL을 `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28`로 변경합니다. URL에 방금 만든 인덱스의 경로가 포함되었습니다.
2. HTTP 형식을 `POST`로 변경합니다.
3. 원시 본문 내용에 다음 JSON을 입력합니다.

	    {
	      "value": [
		    {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
		    {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
		    {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
		    {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
		    {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
	      ]
	    }

4. **Send**를 클릭합니다.

![][5]

## Postman을 사용하여 인덱스 쿼리 ##
마지막 단계에서는 인덱스를 쿼리하고 *trail*이라는 단어에 대한 간단한 전체 텍스트 검색 요청을 실행합니다.

1. 검색 서비스 이름을 사용하여 URL에 `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail`을 입력합니다. URL에 `search` 쿼리 매개 변수와 검색 용어 *trail*이 포함되었습니다.
2. HTTP 요청 형식을 `GET`으로 변경합니다.
3. **Send**를 클릭합니다.

Azure 검색에서 반환된 JSON 검색 결과가 응답에 표시됩니다.

![][6]

## 다음 단계 ##
Postman에서 Azure 검색을 사용하는 방법에 대한 모든 기본 사항을 살펴보았으므로 다음 단계를 통해 몇 가지 유용한 정보를 얻을 수 있습니다.

1. Postman은 자주 실행되는 요청을 저장하는 편리한 방법인 `Collections`를 지원합니다. 다른 사용자가 자신의 Postman 복사본에서 실행할 수 있도록 컬렉션을 공유할 수 있습니다.
2. Azure 검색 설명서에서 각 호출과 관련된 HTTP 요청 형식(`GET`, `PUT` 등)을 확인하고 Postman에서 적절하게 변경하세요.

REST API에 대한 설명서는 [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 확인할 수 있습니다.

또한 [비디오 및 자습서 목록](https://msdn.microsoft.com/library/azure/dn818681.aspx)에서 추가 예제를 확인할 수도 있습니다.

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png
 

<!---HONumber=July15_HO4-->