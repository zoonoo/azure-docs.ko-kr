<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 변경된 내용

###### 참조 추가됨

프로젝트에 Azure Mobile Services NuGet 패키지가 추가되었습니다. 결과로 다음과 같은 .NET 참조가 프로젝트에 추가되었습니다. Microsoft.WindowsAzure.Mobile, Microsoft.WindowsAzure.Mobile.Ext, Newtonsoft.Json, System.Net.Http.Extensions, System.Net.Http.Primitives

###### 모바일 서비스에 대한 연결 문자열 값

App.xaml.cs 파일에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 MobileServiceClient 개체가 생성되었습니다.

### 모바일 서비스 시작

###### 테이블에 대한 참조 가져오기

다음 코드는 TodoItem에 대한 데이터를 포함하는 테이블에 대한 참조를 가져옵니다. 이 참조는 데이터 테이블을 읽고 업데이트하는 후속 작업에서 사용할 수 있습니다. 모바일 서비스가 쿼리에 응답하여 보내는 JSON을 해석하려면 특성이 설정된 TodoItem 클래스가 필요합니다.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

이 코드는 테이블의 사용 권한이 **응용 프로그램 키가 있는 모든 사용자**로 설정된 경우 작동합니다. 모바일 서비스를 보호하기 위해 사용 권한을 변경하는 경우 사용자 인증 지원을 추가해야 합니다. [인증 시작][인증 시작]을 참조하세요.

###### 항목 추가

데이터 테이블에 새 항목을 삽입합니다.

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

###### 테이블 읽기/쿼리

다음 코드에서는 테이블의 모든 항목을 쿼리합니다. 기본적으로 50개 항목인 데이터의 첫 번째 페이지만 반환합니다. 선택적 매개 변수이므로 원하는 페이지 크기를 전달할 수 있습니다.

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

###### 항목 업데이트

데이터 테이블의 행을 업데이트합니다. 매개 변수 항목은 업데이트할 TodoItem 개체입니다.

    await todoTable.UpdateAsync(item);

###### 항목 삭제

데이터베이스의 행을 삭제합니다. 매개 변수 항목은 삭제할 TodoItem 개체입니다.

    await todoTable.DeleteAsync(item);

  [인증 시작]: http://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
