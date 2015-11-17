<properties
	pageTitle="REST API를\을 사용하여 Azure 검색 인덱스 만들기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색 및 .NET SDK를 사용하여 코드에 인덱스를 만듭니다."
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# REST API를 사용하여 Azure 검색 인덱스 만들기
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

이 문서는 [Azure 검색 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 사용하여 인덱스를 만드는 방법을 보여줍니다. 아래 콘텐츠 중 일부는 [인덱스 만들기(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)에서 가져온 것입니다. 자세한 컨텍스트에 대해서는 부모 문서를 참조하세요.

인덱스를 만드는 필수 구성 요소에는 일반적으로 'httpClient'를 통해 이전에 설정된 검색 서비스 연결이 있습니다.

REST API를 통해 인덱스를 만들기 위해 세 부분으로 이루어진 접근 방법에는 연결을 설정하고 인덱스 스키마를 제공한 다음 인덱스를 작성하는 명령을 실행이 있습니다. 코드 조각은 [프로필 샘플 점수 매기기](search-get-started-scoring-profiles.md)에서 가져온 것입니다.

##인덱스 스키마 정의

REST API는 JSON의 요청을 수락합니다. 스키마를 만드는 한 가지 방법은 JSON 문서로 독립 실행형 스키마 파일을 포함하는 것입니다. 다음 예제에는 그림이 있습니다.

이 코드 조각이 파생되는 샘플에서 아래 JSON은 schema.json이라는 파일에 저장됩니다.

	{
	    "name": "musicstoreindex",
	    "fields": [
	        { "name": "key", "type": "Edm.String", "key": true },
	        { "name": "albumTitle", "type": "Edm.String"},
	        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	        { "name": "genre", "type": "Edm.String" },
	        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	        { "name": "artistName", "type": "Edm.String"},
	        { "name": "orderableOnline", "type": "Edm.Boolean" },
	        { "name": "rating", "type": "Edm.Int32" },
	        { "name": "tags", "type": "Collection(Edm.String)" },
	        { "name": "price", "type": "Edm.Double", "filterable": false },
	        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	        { "name": "inventory", "type": "Edm.Int32" },
	        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	    ],
	    "scoringProfiles": [
	        {
	            "name": "boostGenre",
	            "text": {
	                "weights": {
	                    "albumTitle": 5,
	                    "genre": 50,
	                    "artistName": 5
	                }
	            }
	        },
	        {
	            "name": "newAndHighlyRated",
	            "functions": [
	                {
	                    "type": "freshness",
	                    "fieldName": "lastUpdated",
	                    "boost": 10,
	                    "interpolation": "quadratic",
	                    "freshness": {
	                        "boostingDuration": "P365D"
	                    }
	                },
	                {
	                    "type": "magnitude",
	                    "fieldName": "rating",
	                    "boost": 10,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 1,
	                        "boostingRangeEnd": 5,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        },
	        {
	            "name": "boostMargin",
	            "functions": [
	
	                {
	                    "type": "magnitude",
	                    "fieldName": "margin",
	                    "boost": 50,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 50,
	                        "boostingRangeEnd": 100,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        }
	    ]
	}

##연결하기 위한 HTTP 요청 설정 및 인덱스 작성

REST API는 모든 요청에 대한 HTTP 연결을 공식화합니다. 각 요청은는 항상 사용 중인 API의 버전인 서비스 URL과 서비스에서 읽기-쓰기 작업의 권한을 부여하는 관리자 키를 규정합니다(키 이름을 지정하는 방법이기 때문에 기본 키로서 아래를 참조).

코드 조각은 app.config 파일(표시 되지 않음)에 지정된 인덱스에 대한 이름 등 입력으로 정적 값을 사용합니다. 샘플은 코드를 간단하게 유지하기 위해 이러한 방식으로 만들어졌습니다.

        private const string ApiVersion = "api-version=2015-02-28";
        private static string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];
        private static string indexName = ConfigurationManager.AppSettings["indexName"];
        private static string primaryKey = ConfigurationManager.AppSettings["primaryKey"];

다음 예제에서는 `api-version`과 함께 서비스 URL에 대한 HTTP PUT 요청과 만들려는 인덱스 이름을 볼 수 있습니다.

        static bool CreateIndex()
        {
            // Create an index using an index name
            Uri requestUri = new Uri(serviceUrl + indexName + "?" + ApiVersion);

            // Load the json containing the schema from an external file
            string json = File.ReadAllText("schema.json");

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PutAsync(requestUri,        // To create index use PUT
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.Created)   // For Posts we want to know response had no content
                {
                    Console.WriteLine("Index created. \r\n");
                    return true;
                }

                Console.WriteLine("Index creation failed: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->