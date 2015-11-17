<properties
	pageTitle="REST API를 사용하여 Azure 검색으로 데이터 가져오기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="REST API를 사용하여 Azure 검색에서 인덱스에 데이터를 업로드하는 방법"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# REST API를 사용하여 Azure 검색으로 데이터 가져오기
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

이 문서는 [Azure 검색 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 사용하여 인덱스를 채우는 방법을 보여줍니다. 아래 콘텐츠 중 일부는 [문서 추가, 업데이트 또는 삭제](Azure 검색 REST API](https://msdn.microsoft.com/library/azure/dn798930.aspx)에서 가져온 것입니다. 자세한 컨텍스트에 대해서는 부모 문서를 참조하세요.

가져오기 필수 구성 요소에는 데이터를 수신하는 장소에 이미 기존 인덱스를 포함시키는 것입니다.

REST API를 사용하면 데이터 수집은 Put 또는 POST HTTP 요청을 기반으로 합니다. 코드 조각은 [프로필 샘플 점수 매기기](search-get-started-scoring-profiles.md)에서 가져온 것입니다.

        static bool PostDocuments(string fileName)
        {
            // Add some documents to the newly created index
            Uri requestUri = new Uri(serviceUrl + indexName + "/docs/index?" + ApiVersion);

            // Load the json containing the data from an external file
            string json = File.ReadAllText(fileName);

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PostAsync(requestUri,       // To add data use POST
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.OK)
                {
                    Console.WriteLine("Documents posted from file {0}. \r\n", fileName);
                    return true;
                }

                Console.WriteLine("Documents failed to upload: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->