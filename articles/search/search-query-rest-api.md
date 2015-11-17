<properties
	pageTitle="REST 호출을 사용하여 Azure 검색 쿼리 작성 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색에 검색 쿼리를 작성하고 .NET 라이브러리 또는 SDK를 사용하여 검색 결과를 필터링하고 정렬하고 패싯하는 검색 매개 변수를 사용합니다."
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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#REST 호출을 사용하여 Azure 검색의 쿼리 작성 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

이 문서는 [Azure 검색 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 사용하여 인덱스에 대한 쿼리를 생성하는 방법을 보여줍니다. 아래 콘텐츠 중 일부는 [문서 검색(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)에서 가져온 것입니다. 자세한 컨텍스트에 대해서는 부모 문서를 참조하세요.

가져오기에 대한 필수 구성 요소에는 검색 가능한 데이터를 제공하는 문서와 함께 제 위치에 이미 로드된 기존 인덱스가 포함시키는 것입니다.

REST API를 사용하는 경우 쿼리는 GET HTTP 요청을 기반으로 합니다. 코드 조각은 [프로필 샘플 점수 매기기](search-get-started-scoring-profiles.md)에서 가져온 것입니다.

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->