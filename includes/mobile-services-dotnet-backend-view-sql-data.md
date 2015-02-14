
이 자습서의 마지막 단계(선택 사항)에서는 모바일 서비스와 연결된 SQL 데이터베이스를 확인하고 저장된 데이터를 검토합니다. 

1. Azure 관리 포털에서 모바일 서비스와 연결된 데이터베이스에 대해 관리를 클릭합니다.
 
	![sign-in to manage SQL Database](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. 관리 포털에서 쿼리를 실행하여 Windows 스토어 앱에 의한 변경 사항을 확인합니다. 쿼리는 다음과 유사하지만, <code>todolist</code> 대신 데이터베이스 이름을 사용하게 됩니다.</p>

        SELECT * FROM [todolist].[todoitems]

    ![query SQL Database for stored items](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	테이블에는 Id, __createdAt, __updatedAt 및 __version 열이 포함되어 있습니다. 이러한 열은 오프라인 데이터 동기화를 지원하며 [EntityData](http://msdn.microsoft.com/ko-kr/library/microsoft.windowsazure.mobile.service.entitydata.aspx) 기본 클래스에서 구현됩니다. 자세한 내용은 [오프라인 데이터 동기화 시작]을 참조하세요.<!--HONumber=42-->
