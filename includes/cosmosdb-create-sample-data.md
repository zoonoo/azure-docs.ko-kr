이제 데이터 탐색기를 사용하여 새 컬렉션에 데이터를 추가할 수 있습니다.

1. 데이터 탐색기에서 새 데이터베이스가 컬렉션 창에 나타납니다. **항목** 데이터베이스를 확장하고 **ToDoList** 컬렉션을 확장하고 **문서**를 클릭한 후 **새 문서**를 클릭합니다. 

   ![Azure Portal의 데이터 탐색기에서 새 문서 만들기](./media/cosmosdb-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. 이제 다음과 같은 구조로 컬렉션에 새 문서 몇 개를 추가합니다. 여기서 각 문서의 ID에 대해 고유 값을 삽입하고 필요에 따라 다른 속성을 변경할 수 있습니다. Azure Cosmos DB가 데이터에 어떠한 스키마도 적용하지 않으므로 새 문서는 사용자가 원하는 어떠한 구조든 가질 수 있습니다.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     이제 데이터 탐색기에서 쿼리를 사용하여 데이터를 검색할 수 있습니다. 기본적으로 데이터 탐색기에서는 SELECT * FROM c를 사용하여 컬렉션의 모든 문서를 검색하지만, 이를 SELECT * FROM c ORDER BY c.name ASC로 변경하면 name 속성의 영문자 오름차순으로 모든 문서를 반환할 수 있습니다. 
 
     또한 서버 쪽 비즈니스 논리를 수행할 수 있도록 데이터 탐색기를 사용하여 저장 프로시저, UDF 및 트리거를 만들 수도 있습니다. 데이터 탐색기는 API에서 사용할 수 있는 모든 기본 제공 프로그래밍 방식 데이터 액세스를 표시하지만 Azure Portal의 데이터에도 쉽게 액세스할 수 있습니다.