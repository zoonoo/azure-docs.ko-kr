앱에서 어떤 일이 일어나는지 빠르게 검토해 보겠습니다. DocumentDBRepository.cs 파일을 열어 보면 이들 코드 줄이 DocumentDB 리소스를 만드는 것을 알 수 있습니다. 

* DocumentClient가 초기화됩니다.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* 새 데이터베이스가 만들어집니다.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* 새 컬렉션이 만들어집니다.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```
