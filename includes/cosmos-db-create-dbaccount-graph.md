1. 새 창에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터베이스**를 클릭한 후 **Azure Cosmos DB**를 클릭합니다.
   
   ![더 많은 서비스와 Azure Cosmos DB를 강조 표시한 Azure Portal의 스크린샷](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. **새 계정** 블레이드에서 Azure Cosmos DB 계정에 대해 원하는 구성을 지정합니다. 

    Azure Cosmos DB를 사용하면 Gremlin(그래프), MongoDB, SQL(DocumentDB) 및 테이블(키-값)의 4가지 프로그래밍 모델 중 하나를 선택할 수 있습니다.  
       
    이 빠른 시작에서는 Graph API에 대한 프로그래밍을 수행하므로 양식을 작성할 때 **Gremlin(그래프)**를 선택합니다. 하지만 카탈로그 앱의 문서 데이터, 키/값(테이블) 데이터 또는 MongoDB 앱에서 마이그레이션한 데이터가 있는 경우, Azure Cosmos DB는 모든 중요 업무용 응용 프로그램에 대해 전 세계에 분산된 고가용성 데이터베이스 서비스 플랫폼을 제공할 수 있습니다.

    스크린샷의 정보를 가이드로 사용하여 새 계정 블레이드를 채웁니다. 계정을 설정하면서 고유한 값을 선택하게 되므로 사용자의 값이 스크린샷과 정확하게 일치하지는 않습니다. 
 
    ![새 Azure Cosmos DB 블레이드의 스크린샷](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    설정|제안 값|설명
    ---|---|---
    ID|*고유 값*|Azure Cosmos DB 계정을 식별하기 위해 선택하는 고유한 이름입니다. *documents.azure.com*은 URI를 만들기 위해 제공하는 ID에 추가됩니다. 따라서 고유하지만 식별 가능한 ID를 사용합니다. ID는 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다.
    API|Gremlin(그래프)|[Graph API](../articles/cosmos-db/graph-introduction.md)에 대한 프로그래밍은 이 문서의 뒷부분에 나옵니다.|
    구독|*사용자의 구독*|Azure Cosmos DB 계정에 사용할 Azure 구독입니다. 
    리소스 그룹|*ID와 동일한 값*|계정의 새 리소스 그룹 이름입니다. 간단히 하기 위해 ID와 동일한 이름을 사용할 수 있습니다. 
    위치|*사용자와 가장 가까운 지역*|Azure Cosmos DB 계정을 호스트할 지리적 위치입니다. 데이터에 빠르게 액세스할 수 있도록 사용자와 가장 가까운 위치를 선택합니다.

4. **만들기** 를 클릭하여 계정을 만듭니다.
5. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.

    ![배포가 시작됨 알림](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  배포가 완료되면 모든 리소스 타일에서 새 계정을 엽니다. 

    ![모든 리소스 타일의 DocumentDB 계정](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)