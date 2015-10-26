<properties
	pageTitle=".NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법"
	description="이 항목에서는 데이터 모델 이니셜라이저 및 .NET 백 엔드 모바일 서비스에서 데이터 모델을 변경하는 방법을 설명합니다."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	writer="glenga"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="NA"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="glenga"/>

# .NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법

이 항목에서는 Entity Framework의 Code First 마이그레이션을 사용하여 기존 데이터의 손실 없이 기존 Azure SQL 데이터베이스에 대한 데이터 모델을 변경하는 방법을 보여 줍니다. 이 절차에서는 Azure에 .NET 백 엔드 프로젝트를 이미 게시했고, 데이터베이스에 기존 데이터가 있으며, 원격 데이터 모델과 로컬 데이터 모델이 동기화되어 있다고 가정합니다. 또한 이 항목에서는 개발 중에 사용되며 Azure 모바일 서비스를 통해 구현되는 기본 Code First 이니셜라이저에 대해 설명합니다. 이러한 이니셜라이저를 사용하면 기존 데이터를 유지할 필요가 없을 때 Code First 마이그레이션을 사용하지 않고 스키마를 손쉽게 변경할 수 있습니다.

>[AZURE.NOTE]SQL 데이터베이스에 있는 테이블을 접두사 지정하는 데 사용되는 스키마 이름은 web.config 파일에서 MS\_MobileServiceName 앱 설정을 통해 정의됩니다. 포털에서 시작 프로젝트를 다운로드 하면 이 값이 이미 모바일 서비스 이름으로 설정되어 있습니다. 스키마 이름이 모바일 서비스와 일치하면 여러 모바일 서비스에서 동일한 데이터베이스 인스턴스를 안전하게 공유할 수 있습니다.

.NET 백 엔드 프로젝트에서는 자동 마이그레이션이 지원되지 않습니다.

## 데이터 모델 업데이트

.NET 백엔드 모바일 서비스에 기능을 추가할 때는 API에서 새 끝점을 노출하기 위해 새 컨트롤러를 추가합니다. 사용자 지정 컨트롤러 또는 테이블 컨트롤러로 새 API를 만듭니다. [TableController<TEntity>]는 [EntityData]에서 상속되는 데이터 형식을 노출합니다. 데이터베이스에서 유지되어야 하는 데이터를 사용하려면 이 데이터 형식도 [DbContext]에서 새 [DbSet<T>]으로 데이터 모델에 추가해야 합니다. Entity Framework의 Code First에 대한 내용은 참조 [Code first로 모델 만들기](https://msdn.microsoft.com/data/ee712907#codefirst)를 참조하세요.

Visual Studio를 사용하면 쉽게 새 테이블 컨트롤러를 만들고 새 데이터 형식을 클라이언트 앱에 노출할 수 있습니다. 자세한 내용은 [컨트롤러를 사용하여 모바일 서비스의 데이터에 액세스하는 방법](https://msdn.microsoft.com/library/windows/apps/xaml/dn614132.aspx)을 참조하세요.

## 데이터 모델 이니셜라이저

모바일 서비스는 .NET 백엔드 모바일 서비스 프로젝트에서 두 개의 데이터 모델 이니셜라이저 기본 클래스를 제공합니다. 두 이니셜라이저는 Entity Framework가 [DbContext]에서 데이터 모델 변경을 감지하면 데이터베이스에서 테이블을 삭제하고 다시 만듭니다. 또한 모바일 서비스가 로컬 컴퓨터에서 실행 중일 때는 물론 Azure에서 호스트될 때도 작동하도록 디자인되었습니다.

>[AZURE.NOTE].NET 백 엔드 모바일 서비스를 게시하면 데이터 액세스 작업이 발생할 때까지 이니셜라이저가 실행되지 않습니다. 즉, 새로 게시된 서비스의 경우 클라이언트에서 쿼리 등의 데이터 액세스 작업이 요청되어야만 저장소에 사용되는 데이터 테이블이 만들어집니다.
>
>시작 페이지의 **사용해보기** 링크에서 액세스하는 기본 제공 API 도움말 기능을 사용하여 데이터 액세스 작업을 실행할 수도 있습니다. API 페이지를 사용하여 모바일 서비스를 테스트하는 방법에 대한 자세한 내용은 [기존 앱에 모바일 서비스 추가](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#test-the-service-locally)에서 로컬로 모바일 서비스 프로젝트 테스트 섹션을 참조하세요.

두 이니셜라이저는 모바일 서비스에서 사용되는 스키마에 있는 모든 테이블, 뷰, 함수 및 프로시저를 데이터베이스에서 삭제합니다.

+ **ClearDatabaseSchemaIfModelChanges** <br/> 스키마 개체는 Code First가 데이터 모델에서 변경을 감지하는 경우에만 삭제됩니다. [Azure 관리 포털]에서 다운로드하는 .NET 백 엔드 프로젝트의 기본 이니셜라이저는 이 기본 클래스에서 상속됩니다.

+ **ClearDatabaseSchemaAlways**: <br/> 스키마 개체는 데이터 모델에 액세스할 때마다 삭제됩니다. 이 기본 클래스를 사용하면 데이터 모델을 변경하지 않으면서 데이터베이스를 재설정할 수 있습니다.

로컬 컴퓨터에서 실행 중인 다른 Code First 데이터 모델 이니셜라이저를 사용할 수 있습니다. 하지만 사용자에게 데이터베이스를 삭제할 수 있는 권한이 없기 때문에 데이터베이스를 삭제하려는 이니셜라이저는 Azure에서 실패하게 됩니다.

모바일 서비스 프로젝트를 로컬로 개발하는 동안에는 이니셜라이저를 계속 사용할 수 있으며, .NET 백 엔드 자습서에서는 개발자가 이니셜라이저를 사용하고 있다고 가정하고 설명합니다. 데이터 모델을 변경하고 데이터베이스에서 기존 데이터를 유지하려는 경우에는 Code First 마이그레이션을 사용해야 합니다.

>[AZURE.IMPORTANT]라이브 Azure 서비스에 대한 모바일 서비스 프로젝트를 개발하여 테스트할 경우 항상 테스트 전용 모바일 서비스 인스턴스를 사용해야 합니다. 현재 프로덕션에 있거나 클라이언트 앱에서 사용 중인 모바일 서비스를 사용하여 개발하거나 테스트해서는 안 됩니다.

다운로드한 빠른 시작 프로젝트에서 Code First 이니셜라이저는 WebApiConfig.cs 파일에 정의되어 있습니다. **Seed** 메서드를 재정의하여 초기 데이터 행을 새 테이블에 추가합니다. 데이터 시드 예제는 [마이그레이션에서 데이터 시드]를 참조하세요.

## <a name="migrations"></a>Code First 마이그레이션 사용

Code First 마이그레이션에서는 스냅숏 방법을 사용하여 데이터베이스에 대한 스키마를 변경하는 코드를 생성합니다. 마이그레이션을 사용하여 데이터 모델을 증분적으로 변경하고 데이터베이스의 기존 데이터를 유지할 수 있습니다.

>[AZURE.NOTE].NET 백 엔드 모바일 서비스 프로젝트를 Azure에 이미 게시했고 SQL 데이터베이스 테이블 스키마가 프로젝트의 현재 데이터 모델과 일치하지 않는 경우 Code First 마이그레이션을 사용하여 게시하기 전에 이니셜라이저를 사용하거나, 테이블을 수동으로 삭제하거나, 스키마와 데이터 모델을 동기화해야 합니다.

다음 단계에서는 마이그레이션을 켜고 프로젝트, 로컬 데이터베이스 및 Azure에서 데이터 모델 변경을 적용합니다.

1. Visual Studio의 솔루션 탐색기에서 모바일 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.

2. **도구** 메뉴에서 **NuGet 패키지 관리자**를 확장한 다음 **패키지 관리자 콘솔**을 클릭합니다.

	Code First 마이그레이션을 관리하는 데 사용할 패키지 관리자 콘솔이 표시됩니다.

3. 패키지 관리자 콘솔에서 다음 명령을 입력합니다.

		PM> Enable-Migrations

	이 명령은 프로젝트에 대한 Code First 마이그레이션을 켭니다.

4. 콘솔에서 다음 명령을 입력합니다.

		PM> Add-Migration Initial

	이 명령은 *Initial*이라는 새 마이그레이션을 만듭니다. 마이그레이션 코드는 마이그레이션 프로젝트 폴더에 저장됩니다.

5. App\_Start 폴더를 확장하고, WebApiConfig.cs 프로젝트 파일을 열고, 다음 **using** 문을 추가합니다.

		using System.Data.Entity.Migrations;
		using todolistService.Migrations;

	위 코드에서 _todolistService_ 문자열을 프로젝트의 네임스페이스로 바꾸어야 합니다. 다운로드한 퀵 스타트 프로젝트의 경우 <em>mobile&#95;service&#95;name</em>Service로 바꿉니다.

6. 이 코드 파일에서 **Database.SetInitializer** 메서드에 대한 호출을 주석으로 처리하고 다음 코드를 뒤에 추가합니다.

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

	이 코드는 데이터베이스를 삭제하고 다시 만드는 기본 Code First 데이터베이스 이니셜라이저를 사용하지 않도록 설정하고, 대신 최신 마이그레이션을 적용하라는 명시적 요청으로 바꿉니다. 이제 마이그레이션을 만들지 않은 경우 데이터 모델을 변경하면 데이터에 액세스할 때 InvalidOperationException이 발생합니다. 이후 서비스에서는 Code First 마이그레이션을 사용하여 데이터베이스에 대한 데이터 모델 변경을 마이그레이션해야 합니다.

7.  로컬 컴퓨터에서 모바일 서비스 프로젝트를 시작하려면 F5 키를 누르세요.

	이제 데이터베이스가 데이터 모델과 동기화됩니다. 시드 데이터를 제공한 경우 **연습**, **GET 테이블/todoitem**, **연습** 및 **보내기**를 클릭하여 시드 데이터를 확인할 수 있습니다. 자세한 내용은 [마이그레이션에서 데이터 시드]를 참조하세요.

8.   이제 데이터 모델을 변경하고(예: TodoItem 유형에 새 UserId 속성 추가), 프로젝트를 다시 빌드한 다음 패키지 관리자에서 다음 명령을 실행합니다.

		PM> Add-Migration NewUserId

	이 명령은 *NewUserId*라는 새 마이그레이션을 만듭니다. 이 변경을 구현하는 새 코드 파일이 마이그레이션 폴더에 추가됩니다.

9.  로컬 컴퓨터에서 모바일 서비스 프로젝트를 다시 시작하려면 F5 키를 다시 누르세요.

	마이그레이션이 데이터베이스에 적용되고 데이터베이스가 데이터 모델과 다시 동기화됩니다. 시드 데이터를 제공한 경우 **연습**, **GET 테이블/todoitem**, **연습** 및 **보내기**를 클릭하여 시드 데이터를 확인할 수 있습니다. 자세한 내용은 [마이그레이션에서 데이터 시드]를 참조하세요.

10. 모바일 서비스를 Azure에 다시 게시하고, 클라이언트 앱을 실행하여 데이터에 액세스한 다음 데이터가 로드되고 오류가 발생하지 않는지 확인합니다.

13. (선택 사항) [Azure 관리 포털]에서 모바일 서비스를 선택한 다음 **구성** > **SQL 데이터베이스**를 클릭합니다. 모바일 서비스의 데이터베이스에 대한 SQL 데이터베이스 페이지로 이동합니다.

14. (옵션) **관리**를 클릭하고, SQL 데이터베이스 서버에 로그인한 다음 **디자인**을 클릭하여 Azure에서 스키마가 변경되었는지 확인합니다.


## 이니셜라이저 없이 Code First 마이그레이션 사용
.NET 백 엔드 프로젝트와 함께 Code First 마이그레이션을 사용하기 전에 데이터 모델 이니셜라이저를 실행해야 합니다. 이니셜라이저를 사용하지 않으면 마이그레이션을 적용하려고 할 때 오류가 발생할 수 있습니다. 미리 정의된 데이터 모델 이니셜라이저 중 하나를 사용하지 않는 경우 다음 예제와 같이 Migrations\\Configuration.cs 파일에서 EntityTableSqlGenerator를 SqlGenerator로 사용하도록 마이그레이션이 구성되어 있는지 확인합니다.

    public Configuration()
    {
        AutomaticMigrationsEnabled = false;
        SetSqlGenerator("System.Data.SqlClient", new EntityTableSqlGenerator());
    }
    
##<a name="seeding"></a>마이그레이션에서 데이터 시드

마이그레이션을 실행할 때 마이그레이션에서 데이터베이스에 시드 데이터를 추가하도록 할 수 있습니다. **Configuration** 클래스에는 데이터를 삽입 또는 업데이트하도록 재정의할 수 있는 **Seed** 메서드가 있습니다. 마이그레이션을 사용하도록 설정하면 Configuration.cs 코드 파일이 마이그레이션 폴더에 추가됩니다. 이 예에서는 [TodoItems] 테이블에 데이터를 시드하도록 **Seed** 메서드를 재정의하는 방법을 보여 줍니다. 최신 버전으로 마이그레이션한 후 [Seed] 메서드가 호출됩니다.

###새 테이블 시드

다음 코드는 **TodoItems** 테이블을 새 데이터 행으로 시드합니다.

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

###테이블에서 새 열 시드

다음 코드는 UserId 열을 시드합니다.

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

이 코드는 [AddOrUpdate] 도우미 확장 메서드를 호출하여 새 UserId 열에 시드 데이터를 추가합니다. [AddOrUpdate]를 사용하면 중복 행이 생성되지 않습니다.

<!-- Anchors -->
[Migrations]: #migrations
[마이그레이션에서 데이터 시드]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/library/gg679604(v=vs.113).aspx
[Seed]: http://msdn.microsoft.com/library/hh829453(v=vs.113).aspx
[TodoItems]: http://msdn.microsoft.com/library/hh829453(v=vs.113).aspx
[Azure 관리 포털]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
[TableController<TEntity>]: https://msdn.microsoft.com/library/azure/dn643359.aspx
[EntityData]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.entitydata.aspx
[DbSet<T>]: https://msdn.microsoft.com/library/azure/gg696460.aspx

<!---HONumber=Oct15_HO3-->