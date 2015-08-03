<properties 
	pageTitle="모바일 서비스에서 일시 삭제 사용(Windows 스토어) | 모바일 개발자 센터" 
	description="응용 프로그램에서 Azure 모바일 서비스 일시 삭제 기능을 사용하는 방법에 대해 알아봅니다." 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# 모바일 서비스에서 일시 삭제 사용

##개요

JavaScript 또는 .NET 백 엔드를 사용하여 만든 테이블에서 선택적으로 일시 삭제를 사용하도록 설정할 수 있습니다. 일시 삭제를 사용할 때는 [SQL 비트 유형]의 새 열인 *__deleted*가 데이터베이스에 추가됩니다. 일시 삭제를 사용하도록 설정한 상태로 삭제 작업을 수행해도 데이터베이스에서 행이 실제로 삭제되는 것은 아니며 삭제된 열의 값이 TRUE로 설정됩니다.

일시 삭제를 사용하도록 설정한 상태로 테이블에서 레코드를 쿼리하면 기본적으로 쿼리에서 삭제된 행이 반환되지 않습니다. 이러한 행을 요청하려면 [REST 쿼리 작업](http://msdn.microsoft.com/library/azure/jj677199.aspx)에서 쿼리 매개 변수 *__includeDeleted=true*를 전달해야 합니다. .NET 클라이언트 SDK에서는 도우미 메서드 `IMobileServiceTable.IncludeDeleted()`을(를) 사용할 수도 있습니다.

.NET 백 엔드에 대한 일시 삭제 지원은 Microsoft Azure 모바일 서비스 .NET 백 엔드 버전 1.0.402에서 처음 공개되었습니다. 최신 NuGet 패키지는 [Microsoft Azure 모바일 서비스 .NET 백 엔드](http://go.microsoft.com/fwlink/?LinkId=513165)에서 제공됩니다.


일시 삭제를 사용하는 경우 제공될 수 있는 몇 가지 이점은 다음과 같습니다.

* [모바일 서비스용 오프라인 데이터 동기화] 기능을 사용할 때 클라이언트 SDK는 삭제된 레코드를 자동으로 쿼리하여 로컬 데이터베이스에서 제거합니다. 일시 삭제를 사용하도록 설정하지 않으면 클라이언트 SDK가 로컬 저장소에서 제거할 레코드를 확인할 수 있도록 백 엔드에서 추가 코드를 작성해야 합니다. 그렇지 않으면 이처럼 삭제된 레코드와 관련하여 클라이언트 로컬 저장소와 백 엔드가 일치하지 않게 되므로 클라이언트 메서드 `PurgeAsync()`을(를) 호출하여 로컬 저장소를 정리해야 합니다.
* 일부 응용 프로그램에서는 업무 요구 사항에 따라 실제로 데이터를 삭제할 수 없거나 데이터를 감사한 후에만 삭제해야 합니다. 이러한 시나리오에서는 일시 삭제 기능이 유용할 수 있습니다.
* 일시 삭제를 사용하여 "삭제 취소" 기능을 구현할 수 있으므로 실수로 삭제된 데이터를 복구할 수 있습니다. 그러나 일시 삭제된 레코드는 데이터베이스의 공간을 차지하므로 일시 삭제된 레코드를 정기적으로 영구 삭제하는 예약된 작업을 만들어야 합니다. 이러한 작업의 예제는 [.NET 백 엔드에서 일시 삭제 사용] 및 [JavaScript 백 엔드에서 일시 삭제 사용]을 참조하세요. 또한 이와 같이 영구 삭제된 레코드가 장치의 로컬 데이터 저장소에 남아 있지 않도록 클라이언트 코드가 `PurgeAsync()`을(를) 정기적으로 호출해야 합니다.





##.NET 백 엔드에 대해 일시 삭제를 사용하도록 설정

.NET 백 엔드에 대한 일시 삭제 지원은 Microsoft Azure 모바일 서비스 .NET 백 엔드 버전 1.0.402에서 처음 공개되었습니다. 최신 NuGet 패키지는 [Microsoft Azure 모바일 서비스 .NET 백 엔드](http://go.microsoft.com/fwlink/?LinkId=513165)에서 제공됩니다.

다음 단계에서는 .NET 백 엔드 모바일 서비스에 대해 일시 삭제를 사용하도록 설정하는 방법을 안내합니다.

1. Visual Studio에서 .NET 백 엔드 모바일 서비스 프로젝트를 엽니다.
2. .NET 백 엔드 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다. 
3. 패키지 관리자 대화 상자에서 업데이트 아래의 **Nuget.org**를 클릭하고 [Microsoft Azure 모바일 서비스 .NET 백 엔드](http://go.microsoft.com/fwlink/?LinkId=513165) NuGet 패키지 1.0.402 이상 버전을 설치합니다.
3. Visual Studio의 솔루션 탐색기에서 .NET 백 엔드 프로젝트 아래의 **컨트롤러** 노드를 확장하고 *TodoItemController.cs* 등의 컨트롤러 원본을 엽니다.
4. 컨트롤러의 `Initialize()` 메서드에서 `enableSoftDelete: true` 매개 변수를 EntityDomainManager 생성자로 전달합니다.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


##JavaScript 백 엔드에 대해 일시 삭제를 사용하도록 설정

모바일 서비스에 대해 새 테이블을 만드는 경우 테이블 만들기 페이지에서 일시 삭제를 사용하도록 설정할 수 있습니다.

![][2]

JavaScript 백 엔드의 기존 테이블에 대해 일시 삭제를 사용하도록 설정하려면

1. [관리 포털]에서 모바일 서비스를 클릭한 다음 데이터 탭을 클릭합니다.
2. 데이터 페이지에서 원하는 테이블을 클릭하여 선택합니다. 그런 다음 명령 모음에서 **일시 삭제 사용** 단추를 클릭합니다. 테이블에 대해 일시 삭제가 이미 사용하도록 설정되어 있으면 이 단추는 표시되지 않습니다. 그러나 테이블의 **찾아보기** 또는 **열** 탭을 클릭하면 *__deleted* 열을 확인할 수 있습니다.

    ![][0]

    테이블에 대해 일시 삭제를 사용하지 않도록 설정하려면 **열** 탭을 클릭하고 *__deleted* 열과 **삭제** 단추를 차례로 클릭합니다.

    ![][1]

## <a name="using-with-dotnet"></a>.NET 백 엔드에서 일시 삭제 사용


다음의 예약된 작업에서는 1개월보다 오래된 일시 삭제된 레코드를 삭제합니다.

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;
     
        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, 
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }
     
        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);
     
            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();
     
            return Task.FromResult(true);
        }
    }

.NET 백 엔드 모바일 서비스와 예약된 작업에 대해 자세히 알아보려면 [JavaScript 백 엔드 모바일 서비스에서 반복 작업 예약](mobile-services-dotnet-backend-schedule-recurring-tasks.md)을 참조하세요.




##JavaScript 백 엔드에서 일시 삭제 사용

테이블 스크립트를 사용하여 JavaScript 백 엔드 모바일 서비스의 일시 삭제 기능과 관련된 논리를 추가합니다.

삭제 취소 요청을 검색하려면 업데이트 테이블 스크립트에서 "undelete" 속성을 사용합니다.
    
    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
스크립트의 쿼리 결과에 삭제된 레코드를 포함하려면 "includeDeleted" 매개 변수를 true로 설정합니다.
    
    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

HTTP 요청을 통해 삭제된 레코드를 검색하려면 "__includedeleted=true" 쿼리 매개 변수를 추가합니다.

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### 일시 삭제된 레코드를 삭제하는 예약된 작업 샘플

아래에는 특정 날짜 전에 업데이트된 레코드를 삭제하는 예약된 작업의 샘플이 나와 있습니다.

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

JavaScript 백 엔드 모바일 서비스와 예약된 작업에 대해 자세히 알아보려면 [JavaScript 백 엔드 모바일 서비스에서 반복 작업 예약](mobile-services-schedule-recurring-tasks.md)을 참조하세요.





<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[SQL 비트 유형]: http://msdn.microsoft.com/library/ms177603.aspx
[모바일 서비스용 오프라인 데이터 동기화]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[관리 포털]: https://manage.windowsazure.com/


 

<!---HONumber=July15_HO4-->