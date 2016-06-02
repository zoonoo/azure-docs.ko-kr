### 필수 조건
- SQL Server 데이터베이스에 대한 액세스

논리 앱에서 매시업 SQL을 사용하려면 먼저 매시업 SQL 계정에 연결하도록 논리 앱에 권한을 부여해야 합니다. 다행히 Azure 포털의 논리 앱 내에서 이를 쉽게 수행할 수 있습니다.

매시업 SQL 계정에 연결하도록 논리 앱에 권한을 부여하는 단계는 다음과 같습니다.

1. 논리 앱 디자이너에서 매시업 SQL에 대한 연결을 만들려면 드롭다운 목록에서 **Microsoft 관리되는 API 표시**를 선택한 다음 검색 상자에 *매시업 SQL*을 입력합니다. 사용할 트리거 또는 동작을 선택합니다. ![매시업 SQL 1단계](./media/connectors-create-api-mashupsql/mashupsql-1.png)
2. 이전에 매시업 SQL에 대한 연결을 만들지 않은 경우 매시업 SQL 자격 증명을 제공하라는 메시지가 표시됩니다. 이러한 자격 증명을 사용하여 매시업 SQL 계정의 데이터에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다. ![매시업 SQL 2단계](./media/connectors-create-api-mashupsql/mashupsql-2.png)
5. 연결이 만들어졌으면 논리 앱의 다른 단계를 진행할 수 있습니다. ![매시업 SQL 3단계](./media/connectors-create-api-mashupsql/mashupsql-3.png)